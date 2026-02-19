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

# Enumerating multiple hosts via a file with host IPs. (This has null username/password in the command)

for ip in $(cat smb-hosts.txt); do enum4linux -a -u "" -p "" $ip > enum4linux-$ip.txt; done

```