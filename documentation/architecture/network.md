# Network Design

This document is designed to explain the general "how" and "why" to the design decisions of all independent network nodes and their overarching concepts, including: VMID assignment, VLAN assignment, IP address assignment, port configurations, inter-node communication, and node functional purpose. Inter-node diagramming is to be found in topology.md. Subsequent sections will proceed in the nature of traffic flow through my network originating from the Internet, with broad conceptualizations preceding granular explanations.

## Domain Spaces

My holistic environment encompasses 2 primary network domains: My "internal" private /17 homelab LAN, and my "external" private /24 home LAN shared between my family members. The "internal" homelab LAN address range is further subnetted by node functionality as described below. 
All VLANs and subnets use a 1:1 mapping. All subnets use their default gateway as their primary DNS, with 1.1.1.1 (Cloudflare) and 8.8.8.8 (Google) as backup DNS.
Currently, all address assignments are statically assigned. Dynamic address assignments are expected to used with simulated hosts in the Labs subnet later.

## External LAN Nodes

### SOHO Router + ONT

Traffic flow from the Internet begins with my service plan from my ISP dictating the method and quality of WAN connectivity. The method in question is fiber-optic cabling delivered as FttP, with the fiber-cabling running through my house and terminating at an ONT. The ONT converts and transmits the signal via Ethernet to my SOHO router, which acts as the gateway for my "external" private LAN. Beyond servicing my family's devices, my scope of focus targets servicing 2 nodes within this domain: 1) My workstation PC, and 2) my Dell R640 server. The SOHO router seems to NAT from a public-facing IP, and also serves as a DHCP server.

### Workstation PC

My workstation PC acts as the standard administrative console for interacting with Dell R640 server, which subsequently hosts my "internal" homelab LAN and all connected nodes and services beyond it. Due to limited physical ports, my workstation PC does not correct directly to my R640 and instead requires my SOHO router as an intermediary. Occassional console connections to homelab hardware utilize a USB-to-DB9 connector and PuTTY for serial communication. Beyond this, all administrative interaction between my workstation PC and all domain nodes occur via Windows Powershell CLI or Firefox web GUI to internal management interfaces. Additionally, cross-domain secure access is facilitated by Tailscale VPN, registered with several administrative nodes including this workstation PC, a remote laptop (not covered here, functionally identical), and Proxmox.

### PLANNED: APC Smart-UPS

My UPS is currently integrated into my homelab as a PDU, simply powering the following hardware through grouped outlets: My Dell R640, Catalyst 3750, Nexus 3064, ASA 5520, and cabinet fan. Future development is planned (upon expanding Ethernet port access to my external-LAN) to utilize the built-in management interface for remotely configuring and toggling power control to the various outlet groups. This feature would allow hard-control to the accessability of my internal-LAN vastly improving power efficiency and availability.

### PLANNED: Dell R640 Server - iDRAC 

Originally utilized to update firmware, configure the BIOS of the R640 to optimize hardware performance and security, and setup and initialize the host OS on the internal BOSS storage pool, the iDRAC management interface is currently not connected. Consistent iDRAC access would vastly improve access control by utilizing a feature similar to WoL to remotely power on/off the server, providing better granular control over the internal-LAN than just future-planned Smart-UPS control. This would also improve disaster-recovery by helping construct a out-of-band pipeline in cases of emergency. 

### Dell R640 Server - Proxmox vmbr0

My Dell R640 server acts as the bare-metal host of the core of my "internal" homelab LAN, but functionally overlaps with my "external" home LAN. Featuring 4x-1Gb Ethernet ports and 2x-10/25Gb SFP+ ports, the R640 connects to my external-LAN-servicing SOHO router via 1x-1Gb Ethernet for internet access and WAN uplink to my ISP. The R640 hosts Proxmox as my hypervisor, facilitating VM creation, inter-node communication, and resource allocation. Within Proxmox, the virtual bridge "vmbr0" links Proxmox to the R640's NIC corresponding to the SOHO router uplink, enabling L2-L3 access to my external-LAN nodes (most importantly, my administrative interfaces on my workstation PC). Proxmox vmbr0 can be considered and is often referred to as the Proxmox WAN interface.

### OPNsense vtnet0

Hosted as a VM on Proxmox, OPNsense is a FOSS derived from pfSense, serving as my internal-LAN's router and primary firewall and thus the domain boundary between my "internal" and "external" LANs. This is the first VM initiated upon Proxmox boot. OPNsense creates a virtual NIC port (vtnet0) which virtually connects to the virtual bridge (vmbr0) on Proxmox, specifically designated for WAN uplink. The vtnet0 interface serves as the firewall for inbound WAN traffic. This network segmentation enables security domains that improve data confidentiality and integrity, operational performance, policy-driven access control, experimental capability, and much more.

Due to being initialized before TrueNAS, OPNsense requires using the "Local" storage to avoid circular dependency.

## Internal LAN Nodes

### Dell R640 Server - Proxmox vmbr1

Arguably considered the first online node decidedly within the internal-LAN, "vmbr1" is a virtual bridge initialized upon Proxmox boot that creates a virtual NIC, much like "vmbr0" that VM hosts can connect to. Unlike vmbr0, vmbr1 is specifically dedicated to internal-facing nodes and does not link to a physical NIC on the R640, and thus is not a direct Internet uplink. Proxmox vmbr1 is integral to internal-LAN operational capability by acting as a secure cross-VM and cross-services Layer-2 channel, additionally featuring a Layer-3 interface for management access and control.

### OPNsense vtnet1

Upon the initialization of the OPNsense VM, much like "vtnet0", "vtnet1" is the other virtual NIC port created with virtually connects to the internal-facing virtual bridge (vmbr1) on Proxmox. This vtnet1 serves as the default gateway for all internal subnets, creating the connection terminus for internal-LAN hosts for Layer-3  Internet connectivity. The vtnet1 interface is configured in router-on-a-stick fashion for inter-VLAN routing, consisting over various sub-interfaces allowing for multiple IP assignments for participation in every internal subnet. Each sub-interface hosts its interface's ingress firewall, evaluating inbound LAN traffic from each respective subnet for access control and QoS.

### TrueNAS

TrueNAS is the 2nd VM to initialize automatically upon Proxmox boot, responsible for managing the the physical hard drives directly attached to the R640 and provided to TrueNAS via HBA passthrough. TrueNAS connects to Proxmox vmbr1 (Layer-2), which uplinks to vtnet1 (Layer-3). TrueNAS converges the exposed storage drives into separate storage pools, grouped by function, to serve other hosts. The storage pools are then exposed back to Proxmox via NFS to be accessed and utilized by other hosts. While (currently) most other Proxmox hosts require TrueNAS storage pools to be exposed for creation and initialization, OPNsense, TrueNAS, and Ubuntu-Automation share the "Local" storage pool with the Proxmox OS. 

Unlike most other hosts (except OPNsense), TrueNAS has 2 interfaces: 1) In the Management VLAN/subnet; 2) In the dedicated Storage VLAN/subnet. 

### Proxmox Backup Server (PBS-Primary)

### Proxmox Backup Server (PBS-Secondary)

### Automation (Ubuntu-Automation)

### Monitoring (Ubuntu-Monitoring)

### Security (Ubuntu-Security)

### Catalyst 3750

### Nexus 3064

### Meraki MS220

### ASA 5520

### Firepower 1010

### WLAN 2504 Controller

### AP1

### AP2

### Windows Server 2025

### Wiki.js

### Minecraft



## Design Schema

My homelab internal addressing scheme is designed to provide a holistic, predictable relationship between VMID allocation, VLAN assignment, and IPv4 assignment on a basis of granular functionality. 

VMIDs are the broadest categorized functional domains, grouped in hundred-wide ranges intended to only range from 100 to 999.

VLANs are more granular categorizations within their parent VMID domain directly matching the leading two digits of their relevant VMID (i.e VLAN 13 = VMIDs 130 - 139).
There are 2 exceptions to this: 1) If the interface is an internal default gateway; 2) If the interface resides in the external Home LAN.
1) All internal default gateways are subinterfaces on OPNsense, relating to their subnet instead of the parent VMID.
2) All external Home LAN interfaces use the arbitrary Native VLAN = 99.

IPv4 addresses reflect the most granular scopes of node functionality. Internal addresses define their 3rd octet mirroring their respective VLAN; their 4th octet categorizes functionally/identity-equivalent hosts within the same 10-wide ranges (i.e. AP1 = 10.10.10.80 and AP2 = 10.10.10.81), intended to only range from 10-99.
There are 3 exceptions to this: 1) If the interface is a default gateway; 2) If the interface resides in the external home LAN.
1) All default gateways use .254 as the 4th octet. 
2) External home LAN interfaces follow reverse sequential numbering from the default gateway assignment, and matching 4th octet assignment when possible to congruent hosts within the internal homelab network (i.e. vmbr0 = 192.168.254.253 and vmbr1 = 10.10.10.253).

### Internal Homelab LAN

Primary range:

10.10.0.0/17 (10.10.0.0 - 10.10.127.255)

Subnet ranges: 

10.10.(VLAN).(10-99)/24 (10.10.10.10 - 10.10.99.99)*

VLAN 10 (Management): 10.10.10.0/24
VLAN 11 (Infrastructure): 10.10.11.0/24
VLAN 12 (Storage): 10.10.12.0/24
VLAN 13 (Backups): 10.10.13.0/24
VLAN 20 (Labs): 10.10.20.0/24
VLAN 30 (DMZ): 10.10.30.0/24
VLAN 999 (Native): None

*Exceptions: Default gateways and broadcast addresses.

### External Home LAN

Network range:

192.168.254.0/24 (192.168.254.0 - 192.168.254.255)

VLAN 99 (Out-of-Band/External LAN): 192.168.254.0/24
VLAN 999 (Native): None

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
