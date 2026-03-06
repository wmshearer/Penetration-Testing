# Hashcat

## Hash Type & Hashcat Code

| Hash Type | Hashcat Code |
|-----------|-------------|
| MD5 | 0 |
| MD4 | 900 |
| MD2 | 600 |
| SHA1 | 100 |
| SHA2-224 | 1300 |
| SHA2-256 | 1400 |
| SHA2-384 | 10800 |
| SHA2-512 | 1700 |
| SHA3-224 | 17300 |
| SHA3-256 | 17400 |
| SHA3-384 | 17500 |
| SHA3-512 | 17600 |
| NTLM | 1000 |
| NetNTLMv1 | 5500 |
| NetNTLMv2 | 5600 |
| bcrypt | 3200 |
| MD5crypt (Unix) | 500 |
| SHA512crypt (Unix) | 1800 |
| SHA256crypt (Unix) | 7400 |
| WPA/WPA2 | 2500 |
| MySQL4.1/MySQL5+ | 300 |
| MySQL323 | 200 |
| MSSQL 2000 | 131 |
| MSSQL 2005 | 132 |
| MSSQL 2012/2014 | 1731 |
| Oracle H: Type (Oracle 7+) | 3100 |
| Oracle S: Type (Oracle 11+) | 112 |
| Oracle T: Type (Oracle 12+) | 12300 |
| Kerberos 5 AS-REQ (TGT) | 7500 |
| Kerberos 5 TGS-REP (RC4) | 13100 |
| Kerberos 5 TGS-REP (AES128) | 19600 |
| Kerberos 5 TGS-REP (AES256) | 19700 |
| LM | 3000 |
| HMAC-MD5 | 50 |
| HMAC-SHA1 | 150 |
| HMAC-SHA256 | 1450 |
| HMAC-SHA512 | 1750 |
| Django (SHA1) | 124 |
| Django (SHA256) | 10000 |
| phpass | 400 |
| Drupal7 | 7900 |
| WordPress | 400 |
| Juniper IVE | 8100 |
| Cisco-PIX MD5 | 2400 |
| Cisco-ASA MD5 | 2410 |
| Cisco-IOS SHA256 | 5700 |
| Cisco Type 7 | N/A (not crackable via hashcat) |
| RACF | 8500 |
| DPAPI masterkey | 15900 |
| PDF 1.1-1.3 RC4 40-bit | 10400 |
| PDF 1.4-1.6 RC4 128-bit | 10500 |
| PDF 1.7 AES-256 | 10700 |
| ZIP (PKZIP) | 17200 |
| 7-Zip | 11600 |
| RAR3 | 12500 |
| RAR5 | 13000 |


## Determine the Type of Hash

```bash
hashid "PASTE HASH"

hashid "4a41e0fdfb57173f8156f58e49628968a8ba782d0cd251c6f3e2426cb36ced3b647bf83057dabeaffe1475d16e7f62b7"

#Results
Analyzing '4a41e0fdfb57173f8156f58e49628968a8ba782d0cd251c6f3e2426cb36ced3b647bf83057dabeaffe1475d16e7f62b7'
[+] SHA-384 
[+] SHA3-384 
[+] Skein-512(384) 
[+] Skein-1024(384) 

# IMPORTANT NOTE: $ signs can be interprutted differently, so a single quote can alleviate that:
hashid '$2y$10$XrrpX8RD6IFvBwtzPuTlcOqJ8kO2px2xsh17f60GZsBKLeszsQTBC'

# Alternate way
hashcat --identify hash.txt

#Results

The following 4 hash-modes match the structure of your input hash:

| Name| Category
  ======+============================================================+======================================
  10800 | SHA2-384                                                   | Raw Hash
  17500 | SHA3-384                                                   | Raw Hash
  17900 | Keccak-384                                                 | Raw Hash
  10870 | sha384(utf16le($pass))                                     | Raw Hash

```

## Run Hashcat

```bash
hashcat -m <mode> hash.txt /usr/share/wordlists/rockyou.txt

#Output results to a file
hashcat -m 3200 hash.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
```