# John The Ripper

## Running JTR
```bash
<tool>2john <target-file> > hash.txt

#Examples:
ssh2john id_rsa > hash.txt
zip2john archive.zip > hash.txt
office2john document.docx > hash.txt
pdf2john file.pdf > hash.txt
putty2john private.ppk > hash.txt

#Some notable Exceptions:

kirbi2john (Kerberos tickets): kirbi2john ticket.kirbi > hash.txt

hccap2john (WPA handshakes): hccap2john capture.hccap > hash.txt

wpapcap2john (pcap files): wpapcap2john capture.pcap > hash.txt


```

## JTR Table

# John the Ripper - \*2john Tools Reference

| Tool | Purpose | JTR Format Name | Hashcat Mode |
|------|---------|-----------------|-------------|
| `keepass2john` | KeePass database (.kdbx) | KeePass | 13400 |
| `ssh2john` | SSH private keys | SSH RSA/DSA/EC | 22931 |
| `zip2john` | ZIP archives | PKZIP | 17200 |
| `rar2john` | RAR archives | RAR3 / RAR5 | 12500 / 13000 |
| `pdf2john` | PDF files | PDF | 10400-10700 |
| `office2john` | MS Office documents (.doc, .xls, .ppt) | Office | 9400-9800 |
| `gpg2john` | GPG/PGP keys | GPG | 17010 |
| `7z2john` | 7-Zip archives | 7-Zip | 11600 |
| `bitlocker2john` | BitLocker encrypted drives | BitLocker | 22100 |
| `dmg2john` | macOS DMG images | DMG | 23500 |
| `ecryptfs2john` | eCryptfs encrypted folders | eCryptfs | 12200 |
| `encfs2john` | EncFS encrypted folders | EncFS | 12200 |
| `ethereum2john` | Ethereum wallets | Ethereum | 15600 |
| `hccap2john` | WPA/WPA2 handshakes | WPA-PMKID-PBKDF2 | 2500 |
| `itunes_backup2john` | iTunes backups | iTunes backup | 14800 |
| `keychain2john` | macOS Keychain files | Keychain | 23100 |
| `kirbi2john` | Kerberos tickets (.kirbi) | Kerberos | 13100 |
| `krb2john` | Kerberos 5 TGS | Kerberos 5 TGS | 13100 |
| `lotus2john` | Lotus Notes/Domino | Lotus Notes | 9100 |
| `luks2john` | LUKS encrypted drives | LUKS | 14600 |
| `mozilla2john` | Mozilla Firefox/Thunderbird passwords | Mozilla | 16000 |
| `pfx2john` | PFX/PKCS#12 certificates | PKCS#12 | 22100 |
| `pgpdisk2john` | PGP Virtual Disk | PGP Disk | 22600 |
| `putty2john` | PuTTY private keys (.ppk) | PuTTY | 22931 |
| `pwsafe2john` | Password Safe database | Password Safe | 5200 |
| `radius2john` | RADIUS shared secrets | RADIUS | N/A |
| `sipdump2john` | SIP digest authentication | SIP | 11400 |
| `truecrypt2john` | TrueCrypt volumes | TrueCrypt | 6211-6223 |
| `vncpcap2john` | VNC pcap files | VNC | 2100 |
| `wpapcap2john` | WPA pcap handshakes | WPA | 2500 |

## Step 1: Extract a hash

```bash
keepass2john FILE > hash.txt

#Example
keepass2john Database.kdbx > hash.txt
  
cat hash.txt                    
Database:$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1

#NOTE: JTR automatically put the file name "Database" as the username of the hash. However in the instance of KeePass, it uses a master password without an associated username. So we need to remove it.

$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1

```

## Step 2: Crack Extracted Hash
```bash
john EXTRACTED HASH FILE --wordlist=/usr/share/wordlists/rockyou.txt

john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

# NOTE: You can utilize Hashcat for this step. Its also worth noting this is not a traditional hash, so `hashid` command will not work. Instead search online or through hashcat -h or through Johns help file.

```

Step 3: If cracked with John - Show Hashes

```bash
john hash.txt --show
```