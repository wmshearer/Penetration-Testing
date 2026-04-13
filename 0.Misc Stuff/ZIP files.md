```bash
# ZIP / Archive Files
```bash
## Attempting to Unzip
unzip backup.zip
# Example Result:
# Archive: backup.zip
# [backup.zip] backup/cafeteria.xml password:
# If prompted for a password -> archive is password protected. Proceed to cracking below.
```
## Unzip with a Known Password
```bash
unzip -P 'myspace1' backup.zip
# What to do next? Files extracted to current directory. Inspect contents for credentials or sensitive data.
```
## Cracking ZIP Password with fcrackzip
```bash
# Install if needed
sudo apt install fcrackzip

# Crack with rockyou.txt wordlist
fcrackzip -uDp /usr/share/wordlists/rockyou.txt backup.zip
# Example Result:
# PASSWORD FOUND!!!!: pw == myspace1
# What to do next? Unzip with the found password:
unzip -P 'myspace1' backup.zip
```
## Cracking ZIP Password with zip2john + john
```bash
# Step 1: Extract the hash from the zip
zip2john backup.zip > zip.hash

# Step 2: Crack the hash with john
john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
# Example Result:
# myspace1         (backup.zip)
# What to do next? Unzip with the found password:
unzip -P 'myspace1' backup.zip
```
## Cracking ZIP Password with hashcat
```bash
# Step 1: Extract the hash with zip2john
zip2john backup.zip > zip.hash

# Step 2: Crack with hashcat
# ZIP mode 13600
hashcat -m 13600 zip.hash /usr/share/wordlists/rockyou.txt
# Example Result:
# myspace1
# What to do next? Unzip with the found password:
unzip -P 'myspace1' backup.zip
```
## Handling Other Archive Types with 7zip
```bash
# Install if needed
sudo apt install p7zip-full
```
# List contents without extracting
```bash
7z l backup.7z

# Extract
7z x backup.7z

# Extract with known password
7z x backup.7z -p'myspace1'
```
## Handling .tar.gz Archives
```bash
# Extract
tar -xzvf backup.tar.gz

# List contents without extracting
tar -tzvf backup.tar.gz
```
## Handling .rar Archives
```bash
# Install if needed
sudo apt install unrar

# Extract
unrar x backup.rar

# Extract with known password
unrar x -p'myspace1' backup.rar

# Crack .rar password with john
rar2john backup.rar > rar.hash
john --wordlist=/usr/share/wordlists/rockyou.txt rar.hash
```

## Cracking ZIP Password with zip2john + john
```bash
# Step 1: Extract the hash and save it to a file (tee displays it on screen AND saves it)
zip2john emails.zip | tee emails_hash

# Step 2: Crack the hash with john
john emails_hash --wordlist=/usr/share/wordlists/rockyou.txt
# Example Result:
# 1chief          (emails.zip)
# Session completed.
# What to do next? Unzip with the found password.
# NOTE: Use --show to display all cracked passwords reliably:
john --show emails_hash

# Step 3: Extract with 7zip using the cracked password
7z x emails.zip
# When prompted enter the cracked password: 1chief
# Or pass it directly:
7z x emails.zip -p'1chief'
```