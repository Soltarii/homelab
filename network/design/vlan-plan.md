# VLAN Plan

## Design Goals

The network is segmented using VLANs rather than separate physical networks.

Primary objectives:

- Security isolation
- Clear traffic boundaries
- Simplified firewall policy
- Scalable infrastructure growth

The VLAN design is structured to match the schema tying VMID, VLAN, and IP assignment together. Starting with VMID, the defined ranges categorize the broad discipline of the hosts into hundred-wide ID blocks. The VLAN ID is next defined matching the first two digits of the VMID. The IP address assignment is defined to match the final digit of the VMID if applicable, or otherwise sequentially follow within a ten-wide range of specified function.

---

# VLAN Design

| VLAN ID | Name | Purpose | Security Zone |
|---------|------|---------|---------------|
| 10 | Management | Network/Device Administration | Management |
| 11 | Infrastructure | Internal Services Stacks | Internal |
| 12 | Storage | TrueNAS/NFS/iSCSI | Restricted |
| 13 | Backups | Recovery/Version Control | Restricted |
| 20 | Labs | Testing/Exercises | Internal |
| 30 | DMZ | Public-facing Services | Untrusted |
| 99 | Native | Default Traffic Flow | Untrusted |

# VLAN Inventory

| VLAN ID | VMID | Name | Purpose |
|---------|------|------|---------|
| 10 | 100 | OPNsense | L3 Routing/Firewall |
| 11 | 110 | Automation | x |
| 11 | 111 | Monitoring | x |
| 11 | 112 | Security | x |
| 12 | 120 | TrueNAS | Storage |
| 13 | 130 | PBS-Primary | Primary Backup |
| 13 | 131 | PBS-Secondary | Secondary Backup |
| 20 | 200 | Windows-Server-2025 | Active Directory Testing |
| 30 | 300 | Wiki.js | Knowledge Base |
| 30 | 301 | Minecraft | Gaming Server Host |
