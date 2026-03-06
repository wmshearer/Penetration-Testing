# Password Manager Tools

## 1Password and KeePass

![alt text](image-1.png)

```bash
KeePass .kdbx file

# Step 1: Locate the database files by searching for all .kdbx files on the system. Open Powershell: 

Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```
![alt text](image-2.png)