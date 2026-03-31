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

## SNMP Enum

```bash
onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt 192.168.165.149

# Results
public

# Bulk Query
snmpbulkwalk -c public -v2c 192.168.165.149 .

# Username query failed
# Focused Query
snmpbulkwalk -c public -v2c 192.168.165.149 . | grep NET-SNMP-EXTEND-MIB
# Results
NET-SNMP-EXTEND-MIB::nsExtendNumEntries.0 = INTEGER: 1
NET-SNMP-EXTEND-MIB::nsExtendCommand."RESET" = STRING: ./home/john/RESET_PASSWD
NET-SNMP-EXTEND-MIB::nsExtendArgs."RESET" = STRING: 
NET-SNMP-EXTEND-MIB::nsExtendInput."RESET" = STRING: 
NET-SNMP-EXTEND-MIB::nsExtendCacheTime."RESET" = INTEGER: 5
NET-SNMP-EXTEND-MIB::nsExtendExecType."RESET" = INTEGER: exec(1)
NET-SNMP-EXTEND-MIB::nsExtendRunType."RESET" = INTEGER: run-on-read(1)
NET-SNMP-EXTEND-MIB::nsExtendStorage."RESET" = INTEGER: permanent(4)
NET-SNMP-EXTEND-MIB::nsExtendStatus."RESET" = INTEGER: active(1)
NET-SNMP-EXTEND-MIB::nsExtendOutput1Line."RESET" = STRING: Resetting password of kiero to the default value
NET-SNMP-EXTEND-MIB::nsExtendOutputFull."RESET" = STRING: Resetting password of kiero to the default value
NET-SNMP-EXTEND-MIB::nsExtendOutNumLines."RESET" = INTEGER: 1
NET-SNMP-EXTEND-MIB::nsExtendResult."RESET" = INTEGER: 0
NET-SNMP-EXTEND-MIB::nsExtendOutLine."RESET".1 = STRING: Resetting password of kiero to the default value

# Username john found
```

## Attempt to Login to FTP with John:John since Hydra Failed
```bash
hydra -l john -P /usr/share/wordlists/rockyou.txt ftp://192.168.165.149 -t 4
```