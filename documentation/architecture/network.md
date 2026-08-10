# Network Design

This document is designed to explain the general "how" and "why" to the design decisions of all independent network nodes and their overarching concepts, including: VMID assignment, VLAN assignment, IP address assignment, port configurations, inter-node communication, and node functional purpose. Subsequent sections will proceed in the nature of traffic flow through my network originating from the Internet, with broad conceptualizations preceding granular explanations.

## Domain Spaces

My holistic environment encompasses 2 primary network domains: My "internal" private /17 homelab LAN, and my "external" private /24 home LAN shared between my family members. The "internal" homelab LAN address range is further subnetted by node functionality as described below. The internal-LAN address range was chosen to utilize a private IP address space different from the already allocated 192.168.254.0/24 address space of my external-LAN, with the 10.0.0.0/8 range chosen to allow design flexibility and later trimmed down to the 10.10.0.0/17 space for ease of memory while also fitting my designed addressing schema, see the "Design Schema" section below.

All VLANs and subnets use a 1:1 mapping. All subnets use their default gateway as their primary DNS, with 1.1.1.1 (Cloudflare) and 8.8.8.8 (Google) as backup DNS.

Currently, all address assignments are statically assigned. Dynamic address assignments are expected to used with simulated hosts in the Labs subnet later.

## Design Schema

My homelab internal addressing scheme is designed to provide a holistic, predictable relationship between VMID allocation, VLAN assignment, and IPv4 assignment on a basis of granular functionality. 

**VMIDs** are the broadest categorized functional domains, grouped in hundred-wide ranges intended to only range from 100 to 999. These ranges are chosen to give ample room to any future added VMs no matter the functional domain. VMID ranges and specific assignments are mostly designed to follow sequential in operational importance and startup sequence upon Proxmox boot. Non-critical VMs are typically not assigned to boot automatically.

**VLANs** are more granular categorizations within their parent VMID domain directly matching the leading two digits of their relevant VMID (i.e VLAN 13 = VMIDs 130 - 139). There are 2 exceptions to this: 1) If the interface is an internal default gateway; 2) If the interface resides in the external Home LAN.
1) All internal default gateways are subinterfaces on OPNsense, relating to their subnet instead of the parent VMID.
2) All external Home LAN interfaces use the arbitrary Native VLAN = 99.

Additionally, all VLANs and associated subnets are designed to follow the principle of least privilege for damage containment from a hypothetical system compromisation.

**IPv4 addresses** reflect the most granular scopes of node functionality. Internal addresses define their 3rd octet mirroring their respective VLAN; their 4th octet categorizes functionally/identity-equivalent hosts within the same 10-wide ranges (i.e. AP1 = 10.10.10.80 and AP2 = 10.10.10.81), intended to only range from 10-99.
There are 3 exceptions to this: 1) If the interface is a default gateway; 2) If the interface resides in the external home LAN.
1) All default gateways use .254 as the 4th octet. 
2) External home LAN interfaces follow reverse sequential numbering from the default gateway assignment, and matching 4th octet assignment when possible to congruent hosts within the internal homelab network (i.e. vmbr0 = 192.168.254.253 and vmbr1 = 10.10.10.253).

## VMID Design

VMID Primary Range: 100-999

VMID Sub-Ranges:
- 100-199: Administrative
- 200-299: Labs
- 300-399: DMZ
- 900-999: Restoration Testing

## VLAN/Subnet Design

### Management Subnet (VLAN 10)

The Management subnet exists to host critically necessary services and administratively-accessable interfaces to those services for internal-LAN functionality. Hosts within this subnet are given unrestricted access to most other nodes within the internal-LAN for required operational purposes, and are almost entirely network nodes that boot before anything else.

### Infrastructure Subnet (VLAN 11)

The Infrastructure subnet exists to host services less critically necessary than those within the Management subnet, but are still very fundamentally important to internal-LAN operation. They more so manage the required services than rank amongst them and thus comparatively require lesser operational freedom, partially due to being collected stacks of lighter-weight services under an Ubuntu OS umbrella.

### Storage Subnet (VLAN 12)

The Storage subnet exists to exclusively serve the storage services and those in direct contact with them. This is due to storage traffic requiring high bandwidth, being latency sensitive, and utmost confidentiality in handling the data of the entire internal-LAN. 

### Backups Subnet (VLAN 13)

The Backups subnet exists to exclusively serve the backup services and those in direct contact with them. Similar to hosts within the storage subnet, backups are the last line of defense for a network handling old and current data of the entire internal-LAN and should be secured with utmost confidentiality.

### Labs Subnet (VLAN 20)

The Labs subnet exists to host services only reachable from within the internal-LAN, largely with the intention for learning and experimenting with services for professional development, rather the operational necessity within the internal-LAN. These services are modified frequently, may intentionally break, and may run less-than-stable software.

### DMZ Subnet (VLAN 30)

The DMZ subnet exists to host services intended to be publicly and internally reachable, while maintaining heavily restricted access to the internal-LAN resources required to do so.

### Out-of-Band/External-LAN (VLAN 99)

The Out-of-Band/External-LAN subnet exists to serve clients on my residential network, provide Internet access to my internal-LAN edge interfaces, and provide access to pre-initialized internal-LAN-critical hardware management interfaces.

### Native (VLAN 999)

The Native VLAN serves as a catch-all for otherwise untagged traffic, usually acting as a blackhole. It does not have an associated subnet, default gateway, or broadcast address.

### Internal-LAN IP Ranges

**Primary range**:

- 10.10.0.0/17 (10.10.0.0 - 10.10.127.255)

**Subnet ranges**: 

- 10.10.(VLAN).(10-99)/24 (10.10.10.10 - 10.10.99.99)*

**VLAN-Subnet Associations**:

- VLAN 10 (Management): 10.10.10.0/24
- VLAN 11 (Infrastructure): 10.10.11.0/24
- VLAN 12 (Storage): 10.10.12.0/24
- VLAN 13 (Backups): 10.10.13.0/24
- VLAN 20 (Labs): 10.10.20.0/24
- VLAN 30 (DMZ): 10.10.30.0/24
- VLAN 999 (Native): None

**Exceptions*: Default gateways and broadcast addresses.

### External-LAN IP Ranges

**Network range**:

- 192.168.254.0/24 (192.168.254.0 - 192.168.254.255)

**VLAN-Subnet Associations**:

- VLAN 99 (Out-of-Band/External-LAN): 192.168.254.0/24
- VLAN 999 (Native): None
