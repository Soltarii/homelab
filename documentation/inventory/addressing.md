# Network Addressing Plan

The following documentation is intended to be an authoritative source for VMID assignments, VLAN assignments, IP address assignments, gateways, DNS, and reserved ranges.

## Addressing Design

A full explanation of my addressing design can be found in /documentation/architecture/network.md. In summary, my homelab internal addressing scheme is designed to provide a holistic, predictable relationship between VMID allocation, VLAN assignment, and IPv4 assignment on a basis of granular functionality. 

## Address Space

My holistic environment encompasses 2 primary network domains: My "internal" private /17 homelab LAN, and my "external" private /24 home LAN shared between my family members. The "internal" homelab LAN address range is further subnetted by node functionality as described above. 
All VLANs and subnets use a 1:1 mapping. All subnets use their default gateway as their primary DNS, with 1.1.1.1 (Cloudflare) and 8.8.8.8 (Google) as backup DNS.
Currently, all address assignments are statically assigned. Dynamic address assignments are expected to used with simulated hosts in the Labs subnet later.

### Internal-LAN VLAN-Subnet Associations 

Primary range:

- 10.10.0.0/17 (10.10.0.0 - 10.10.127.255)

Subnet ranges: 

- 10.10.(VLAN).(10-99)/24 (10.10.10.10 - 10.10.99.99)*

- VLAN 10 (Management): 10.10.10.0/24
- VLAN 11 (Infrastructure): 10.10.11.0/24
- VLAN 12 (Storage): 10.10.12.0/24
- VLAN 13 (Backups): 10.10.13.0/24
- VLAN 20 (Labs): 10.10.20.0/24
- VLAN 30 (DMZ): 10.10.30.0/24
- VLAN 999 (Native): None

*Exceptions: Default gateways and broadcast addresses.

### External-LAN VLAN-Subnet Associations

Network range:

- 192.168.254.0/24 (192.168.254.0 - 192.168.254.255)

- VLAN 99 (Out-of-Band/External LAN): 192.168.254.0/24
- VLAN 999 (Native): None

## Current Assignments

### Management Subnet

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
| 10.10.10.254 | Virtual | 100 | 10 | OPNsense VLAN 10 | DG/DNS |
| 10.10.10.255 | Virtual | 100 | 10 | VLAN 10 Broadcast | Broadcast |

### Infrastructure Subnet

| IPv4 Address | Node Type | VMID | VLAN | Name | Purpose |
|--------------|-----------|------|------|------|---------|
| 10.10.11.10 | Virtual | 110 | 11 | Automation | Automation |
| 10.10.11.11 | Virtual | 111 | 11 | Monitoring | Monitoring |
| 10.10.11.12 | Virtual | 112 | 11 | Security | Security |
| 10.10.11.254 | Virtual | 100 | 11 | OPNsense VLAN 11 | DG/DNS |
| 10.10.11.255 | Virtual | 100 | 11 | VLAN 11 Broadcast | Broadcast |

### Storage Subnet

| IPv4 Address | Node Type | VMID | VLAN | Name | Purpose |
|--------------|-----------|------|------|------|---------|
| 10.10.12.10 | Virtual | 120 | 12 | TrueNAS | Storage |
| 10.10.12.254 | Virtual | 100 | 12 | OPNsense VLAN 12 | DG/DNS |
| 10.10.12.255 | Virtual | 100 | 12 | VLAN 12 Broadcast | Broadcast |

### Backups Subnet

| IPv4 Address | Node Type | VMID | VLAN | Name | Purpose |
|--------------|-----------|------|------|------|---------|
| 10.10.13.10 | Virtual | 130 | 13 | PBS-Primary | Backup |
| 10.10.13.11 | Virtual | 131 | 13 | PBS-Secondary | Backup |
| 10.10.13.254 | Virtual | 100 | 13 | OPNsense VLAN 13 | DG/DNS |
| 10.10.13.255 | Virtual | 100 | 13 | VLAN 13 Broadcast | Broadcast |

### Labs Subnet

| IPv4 Address | Node Type | VMID | VLAN | Name | Purpose |
|--------------|-----------|------|------|------|---------|
| 10.10.20.10 | Virtual | 200 | 20 | Windows-Server-2025 | Windows/Administration |
| 10.10.20.254 | Virtual | 100 | 20 | OPNsense VLAN 20 | DG/DNS |
| 10.10.20.255 | Virtual | 100 | 20 | VLAN 20 Broadcast | Broadcast |

### DMZ Subnet

| IPv4 Address | Node Type | VMID | VLAN | Name | Purpose |
|--------------|-----------|------|------|------|---------|
| 10.10.30.10 | Virtual | 300 | 30 | Wiki.js | Documentation |
| 10.10.30.11 | Virtual | 301 | 30 | Minecraft | Server |
| 10.10.30.254 | Virtual | 100 | 30 | OPNsense VLAN 30 | DG/DNS |
| 10.10.30.255 | Virtual | 100 | 30 | VLAN 30 Broadcast | Broadcast |

### Out-of-Band/External LAN

| 192.168.254.250 | Physical | n/a | 99 | APC 1500 | UPS |
| 192.168.254.251 | Physical | n/a | 99 | iDRAC | BIOS |
| 192.168.254.252 | Virtual | 100 | 99 | OPNsense vtnet0 | WAN |
| 192.168.254.253 | Virtual | n/a | 99 | Proxmox vmbr0 | Hypervisor/WAN |
| 192.168.254.254 | Physical | n/a | 99 | Nokia Beacon 6 | Router/WAN/DG/DNS |
