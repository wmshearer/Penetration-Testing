# DNS

# Can use SecLists wordlist for better enumeration
```bash
host www.megacorpone.com

host -t mx megacorpone.com # Mail Exchange Record

host -t txt megacorpone.com # Text Record
```

## DNS Recon

```bash
dnsrecon -d megacorpone.com -t std # Standard recon

dnsrecon -d megacorpone.com -D ~/list.txt -t brt # Bruteforce

# -d for domain name, -D filename with subdomain strings
```
# DNS Enum
```bash
dnsenum megacorpone.com # Bruteforce
```
## NSLookup
```bash
nslookup mail.megacorptwo.com

nslookup -type=TXT info.megacorptwo.com <DNS Server IP>
```