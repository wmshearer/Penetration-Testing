# OSCP Challenge 6 - Lab Machines

## Network Subnets
# Subnet A (external): 10.10.125.x
# Subnet B (internal): 192.168.165.x

## Machines

| Machine   | IP                | Credentials                      |
|-----------|-------------------|----------------------------------|
| DC01      | 10.10.125.152     | No credentials provided          |
| MS01      | 192.168.165.153   | Eric.Wallows / EricLikesRunning800 |
| MS02      | 10.10.125.154     | No credentials provided          |
| Frankfurt | 192.168.165.156   | No credentials provided          |
| Charlie   | 192.168.165.157   | No credentials provided          |
| Pascha    | 192.168.165.155   | No credentials provided          |

## Initial Foothold
# Host:     MS01
# IP:       192.168.165.153
# Username: Eric.Wallows
# Password: EricLikesRunning800

## Notes
# - DC01 and MS02 are on the 10.10.125.x subnet (likely requires pivot)
# - All other machines are on 192.168.165.x subnet
# - MS01 is the only machine with pre-provided credentials