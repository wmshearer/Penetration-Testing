# SMTP Enumeration

```bash

nc -nv 192.168.50.8 25

#Results
(UNKNOWN) [192.168.50.8] 25 (smtp) open
220 mail ESMTP Postfix (Ubuntu)

VRFY root
#results
252 2.0.0 root

VRFY idontexist
#results
550 5.1.1 <idontexist>: Recipient address rejected: User unknown in local recipient table

#NOTE: The 252 SMTP response code does not verify the root user exists but will accept and attempt delivery of any messages. Response code 550 indicates the mailbox is unavailable.
```

## Username discovery

- Create a python script that  issues a VRFY command for a given username. Run the script by providing the username to be tested as a first argument and the target IP as a second argument.

```python
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py <username> <target_ip>")
        sys.exit(0)

# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the Server
ip = sys.argv[2]
connect = s.connect((ip,25))

# Receive the banner
banner = s.recv(1024)

print(banner)

# VRFY a user
user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

# Close the socket
s.close()
```

## Run the created script with a specific username

```bash
python3 smtp.py root 192.168.50.8

#Results
b'220 mail ESMTP Postfix (Ubuntu)\r\n'
b'252 2.0.0 root\r\n'

python3 smtp.py johndoe 192.168.50.8

#Results
b'220 mail ESMTP Postfix (Ubuntu)\r\n'
b'550 5.1.1 <johndoe>: Recipient address rejected: User unknown in local recipient table\r\n'

#NOTE THE 252 vs 550
```

## Create a Python script to check against MULTIPLE usernames. 
#### In order for this to work, create a file called users.txt and use one username per line.

```python
python3 vrfy.py <target_ip>
```



```python
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 2:
        print("Usage: vrfy.py <target_ip>")
        sys.exit(0)

# Read usernames from file
with open("users.txt", "r") as f:
        users = f.read().splitlines()

# Create a Socket and Connect to the Server
ip = sys.argv[1]

for user in users:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        connect = s.connect((ip, 25))

        # Receive the banner
        banner = s.recv(1024)

        # VRFY each user
        s.send(b'VRFY ' + user.encode() + b'\r\n')
        result = s.recv(1024)

        print(f"[*] Testing username: {user}")
        print(result)

        # Close the socket
        s.close()
 ```