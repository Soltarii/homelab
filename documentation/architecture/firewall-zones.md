# Firewall Zones

This document is designed to explain the "how" and "why" to the design decisions of security policy between nodes within my Internal-LAN and External-LAN.

## Design Schema

Firewall zones are primarily designed to follow the following principles: 1) Least Privilege; 2) Default Deny; 3) Stateful Inspection.
1) The Principle of Least Privilege states that nodes/hosts wil be permitted only the strictest access required for operational function;
2) Default Deny refers to the action taken against traffic not explicitly permitted within the firewall ACL;
3) Stateful Inspection refers to the act of tracking and acting upon traffic based on the state of session initiation and flow (i.e. return traffic from an Internet website first requested by an internal client).

Beyond these core principles, firewall zones are subsequently designed for congruency with my addressing schema, which unites the identification assignments between my VMIDs, VLANs, and IP addresses. A comprehensive breakdown of the addressing schema can be found in "network.md". In summary, nodes/hosts are categorized and grouped in increasingly granular functional domains as follows: VMID (broadest) -> VLAN (intermediate) -> IP Address (specific). Firewall zones follow the closest relationship with subnet assignment (nearly identical with VLANs), since firewalls exist on the default gateway interface for a given subnet. All default gateways for my Internal-LAN exists on my OPNsense VM.

## Zone Definitions

**Management**: This zone directly maps to interfaces within the Management VLAN/subnet with implicit unrestricted access to any other node/host regardless of it's firewall zone. This zone is optimized for administrative services traffic between operationally-necessary nodes/hosts. 

**Infrastructure**: This zone directly maps to interfaces within the Infrastructure VLAN/subnet with mostly unrestricted to most other nodes/hosts. This zone is optimized for the operational maintenance and observability services into most nodes/hosts that are already live. 

**Storage**: This zone directly maps to interfaces within the Storage VLAN/subnet with highly restricted access to very specific nodes/hosts directly related to retention of all present data within the Internal-LAN. This zone is optimized for low latency and high bandwidth required for data storage and retrieval.

**Backups**: This zone directly maps to interfaces within the Backups VLAN/subnet with highly restricted access to very specific nodes/hosts directly relating to retention and safeguarding of most present and historic data within the Internal-LAN for disaster-recovery. This zone is optimized for bulk data transfer with focus in operational security and integrity.

**Labs**: This zone directly maps to interfaces within the Labs VLAN/subnet with restricted access to non-critical nodes/hosts within the Internal-LAN only, with exception to outbound Internet services. This zone is optimized for high security for internal experimentation, safeguarding Internal-LAN operations.

**DMZ**: This zone directly maps to interfaces within the DMZ VLAN/subnet with highly restricted access to specific services internally-hosted but publicly-reachable. This zone is optimized for extremely high security against untrusted external access, but low latency for trusted external and internal access.

**WAN**: This zone directly maps to interfaces within the External-LAN with total restricted access to the Internal-LAN with very few exceptions. This zone is optimized for the highest degree of security due to being the only zone that is completely externally-facing, with the only exception to inbound-initiated DMZ-specific access.

## Allowed Communication Matrix

| Source | Destination | Policy |
|--------|-------------|--------|
| Management | Any | Allow (admin) |
| Infrastructure | Management | Allow (limited)
| Infrastructure | Storage | Allow |
| Infrastructure | Backup | Allow |
| Storage | Backup | Allow |
| Backup | Storage | Allow |
| Labs | Internet | Allow |
| Labs | Infrastructure | Default Deny |
| DMZ | Internet | Allow |
| DMZ | Internal | Deny |
| WAN | DMZ | Explicit Ports Only |
| WAN | Internal | Deny |

## Zone Details

**Management**
- Full administrative access
- SSH
- HTTPS
- APIs

**Infrastructure**
- Monitoring
- Automation
- Logging

**Storage**
- NFS
- SMB
- iSCSI

**Backups**
- PBS backups
- Backup syncs
- Data restoration

**Labs**
- Internal experiments

**DMZ**
- Web-hosting
- Port 25565 (Gaming Servers) 

**WAN**
- Inbound-DMZ ONLY
- Outbound internet services
- Trusted administrative access

## Future Rules

- VPN
- Reverse Proxy
- Public Services
- Guest Network
