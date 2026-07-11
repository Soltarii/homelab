# IP Address Plan

## Addressing Design

The IPv4 address assignment schema is designed for congruency relating to a node's VMID (if applicable) and VLAN assignment.
VMIDs are categorized by functional domains (see vms.nd) in hundred-wide ranges from 100 to 900.

VLANs are more granular categorizations within their parent VMID domain directly matching the leading two digits of their relevant VMID (i.e VLAN 13 = VMIDs 130 - 139).
There are 2 exceptions to this: 1) If the interface is an internal default gateway; 2) If the interface resides in the external Home LAN.
1) All internal default gateways are subinterfaces on OPNsense, relating to their subnet instead of the parent VMID.
2) All external Home LAN interfaces use the arbitrary Native VLAN = 99.

IPv4 addresses reflect the most granular scopes of node functionality. Internal addresses define their 3rd octet mirroring their respective VLAN; their 4th octet categorizes equivalent >
There are 3 exceptions to this: 1) All default gateways use .254 as the 4th octet; 2) External Home LAN interfaces follow reverse sequential numbering from the default gateway assignmen>

## Address Space

Primary range:

10.10.0.0/17 (10.10.0.0 - 10.10.127.255)

(Internal) subnet ranges: 

10.10.(VLAN).(10-99)/24 (10.10.10.10 - 10.10.99.99)*

VLAN 10 (Management): 10.10.10.0/24
VLAN 11 (Infrastructure): 10.10.11.0/24
VLAN 12 (Storage): 10.10.12.0/24
VLAN 13 (Backup): 10.10.13.0/24
VLAN 20 (Labs): 10.10.20.0/24
VLAN 30 (DMZ): 10.10.30.0/24
VLAN 99 (Native): 10.10.99.0/24

(External) Home LAN range:

192.168.254.0/24 (192.168.254.0 - 192.168.254.255)

*Exceptions: Default gateways and broadcast addresses.

## Current Assignments

| IPv4 Address | Node Type | VMID | VLAN | Name | Purpose |
|--------------|-----------|------|------|------|---------|
| 10.10.10.10 | Virtual | 120 | 10 | TrueNAS | Storage |
| 10.10.10.20 | Physical | n/a | 10 | Catalyst 3750 | Switch |
| 10.10.10.30 | Physical | n/a | 10 | Nexus 3064 | Switch |
| 10.10.10.40 | Physical | n/a | 10 | Meraki MS220 | Switch |
| 10.10.10.50 | Physical | n/a | 10 | ASA 5520 | Firewall |
| 10.10.10.60 | Physical | n/a | 10 | Firepower 1010 | Firewall |
| 10.10.10.70 | Physical | n/a | 10 | WLAN 2504 Controller | WLC/WLAN |
| 10.10.10.80 | Physical | n/a | 10 | AP1 | WLAN |
| 10.10.10.81 | Physical | n/a | 10 | AP2 | WLAN |
| 10.10.10.253 | Virtual | n/a | 10 | Proxmox vmbr1 | Hypervisor/LAN |
| 10.10.10.254 | Virtual | 100 | 10 | OPNsense VLAN 10 | DG |
| 10.10.10.255 | Virtual | 100 | 10 | VLAN 10 Broadcast | Broadcast |
| 10.10.11.10 | Virtual | 110 | 11 | Automation | Automation |
| 10.10.11.11 | Virtual | 111 | 11 | Monitoring | Monitoring |
| 10.10.11.12 | Virtual | 112 | 11 | Security | Security |
| 10.10.11.254 | Virtual | 100 | 11 | OPNsense VLAN 11 | DG |
| 10.10.11.255 | Virtual | 100 | 11 | VLAN 11 Broadcast | Broadcast |
| 10.10.12.10 | Virtual | 120 | 12 | TrueNAS | Storage |
| 10.10.12.254 | Virtual | 100 | 12 | OPNsense VLAN 12 | DG |
| 10.10.12.255 | Virtual | 100 | 12 | VLAN 12 Broadcast | Broadcast |
| 10.10.13.10 | Virtual | 130 | 13 | PBS-Primary | Backup |
| 10.10.13.11 | Virtual | 131 | 13 | PBS-Secondary | Backup |
| 10.10.13.254 | Virtual | 100 | 13 | OPNsense VLAN 13 | DG |
| 10.10.13.255 | Virtual | 100 | 13 | VLAN 13 Broadcast | Broadcast |
| 10.10.20.10 | Virtual | 200 | 20 | Windows-Server-2025 | Windows/Administration |
| 10.10.20.254 | Virtual | 100 | 20 | OPNsense VLAN 20 | DG |
| 10.10.20.255 | Virtual | 100 | 20 | VLAN 20 Broadcast | Broadcast |
| 10.10.30.10 | Virtual | 300 | 30 | Wiki.js | Documentation |
| 10.10.30.11 | Virtual | 301 | 30 | Minecraft | Server |
| 10.10.30.254 | Virtual | 100 | 30 | OPNsense VLAN 30 | DG |
| 10.10.30.255 | Virtual | 100 | 30 | VLAN 30 Broadcast | Broadcast |
| 10.10.99.254 | Virtual | 100 | 99 | OPNsense VLAN 99 | DG |
| 10.10.99.255 | Virtual | 100 | 99 | VLAN 99 Broadcast | Broadcast |
| 192.168.254.250 | Physical | n/a | 99 | APC 1500 | UPS |
| 192.168.254.251 | Physical | n/a | 99 | iDRAC | BIOS |
| 192.168.254.252 | Virtual | 100 | 99 | OPNsense vtnet0 | WAN |
| 192.168.254.253 | Virtual | n/a | 99 | Proxmox vmbr0 | Hypervisor/WAN |
| 192.168.254.254 | Physical | n/a | 99 | Nokia Beacon 6 | Router/WAN |
