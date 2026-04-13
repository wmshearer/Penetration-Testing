# Upgrading a Shell

## Techniques
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/sh")'
python3 -c 'import pty; pty.spawn("/bin/sh")'
script -qc /bin/bash /dev/null
```

## Full TTY Shell
```bash
# On Kali
CTRL+z
stty raw -echo
fg
stty -a
 
# On Target
export SHELL=bash
export TERM=xterm256-color  OR  export TERM=xterm
stty rows 38
stty columns 116
```