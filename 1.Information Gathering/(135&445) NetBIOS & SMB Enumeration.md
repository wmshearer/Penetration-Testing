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
smbclient -L //<IP>

smbclient //server/share (-U <username/domain>)
```
## SMBMap
```bash
smbmap -H <IP> -u <username> -p <password> (-d <domain> -r <share_name>)
```

## If have read/write access on ADMIN$, can PsExec in
```bash
impacket-psexec '<username>:<password>@<IP>'

smb> put <file> # Upload
smb> get <file> # Download

```