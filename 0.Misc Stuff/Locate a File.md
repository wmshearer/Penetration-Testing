# Locate a File
```
./find_employee_names.py
```

## Windows File Search

```bash
 # Search for config files in XAMPP
 PS C:\Users\offsec> Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue 

 # Search user filesystem for any readable text file
 PS C:\Users\offsec> Get-ChildItem -Path C:\Users\offsec -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue

 # See EVERYTHING (in that current directory and subdirectories)
 PS C:\Users\offsec> tree /F

 # Find password stuffs
 dir /s *pass* == *cred* == *vnc* == *.config*

 # can replace pass with known usernames
 ```

 ## Linux

 ```bash
  # Search for specific filenames
 kali@kali:~$ find / -iname local.txt 2>/dev/null

 # Search for existence of binary
 kali@kali:~$ whereis nc
 
 # Find password stuffs
 kali@kali:~$ findstr /si password *.txt  
 kali@kali:~$ findstr /si password *.xml  
 kali@kali:~$ findstr /si password *.ini  
 kali@kali:~$ Findstr /si password *.config 
 kali@kali:~$ findstr /si pass/pwd *.ini

 # can replace password with known usernames
 ```