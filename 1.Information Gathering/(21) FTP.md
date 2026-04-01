# Init FTP connection
```bash
ftp <Target IP>
# or
ftp <USER>@<Target IP>

# Login with anonymous:anonymous
ftp> USER anonymous
ftp> PASS anonymous

# List all files (incl. hidden)
ftp> ls -a

# Download all files from FTP

wget -m ftp://anonymous:anonymous@<IP>

# NSE
locate .nse | grep ftp

#Then
nmap -p21 --script=<name> <IP>

# Bruteforce
hydra -L users.txt -P passwords.txt <IP> ftp
```