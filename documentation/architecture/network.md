# Network Design

This document is designed to explain the general "how" and "why" to the design decisions of all independent network nodes and their overarching concepts, including: VMID assignment, VLAN assignment, IP address assignment, port configurations, inter-node communication, and node functional purpose. Inter-node diagramming is to be found in topology.md. Subsequent sections will proceed in the nature of traffic flow through my network originating from the Internet, with broad conceptualizations preceding granular explanations.

## Domain Spaces

My holistic environment encompasses 2 primary network domains: My "internal" private /17 homelab LAN, and my "external" private /24 home LAN shared between my family members. The "internal" homelab LAN address range is further subnetted by node functionality as described below. The internal-LAN address range was chosen to utilize a private IP address space different from the already allocated 192.168.254.0/24 address space of my external-LAN, with the 10.0.0.0/8 range chosen to allow design flexibility and later trimmed down to the 10.10.0.0/17 space for ease of memory while also fitting my designed addressing schema, see the "Design Schema" section below.

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

Proxmox Backup Server (PBS-Primary) is the 3rd VM to initialize upon Proxmox boot, responsible for orchestrating backup management include verifying file integrity, pruning snapshots exceeding grouped limits, garbage-collecting memory chunks no longer being associated with any snapshots, and syncing the entire backup repository with PBS-Secondary (and eventually Github). PBS-Primary is the crux of my homelab's disaster-recovery operations and version control restoration. PBS restoration has also proved an invaluable resource in Proxmox VMID management.

### Proxmox Backup Server (PBS-Secondary)

Proxmox Backup Server (PBS-Secondary) is the 4th VM to initialize upon Proxmox boot, responsible as an off-site backup option in the event PBS-Primary fails. PBS-Secondary is a direct copy of the last sync job from PBS-Primary, and does not receive a unique backup job directly from Proxmox. As a secondary storage repository, PBS-Secondary also performs file integrity verification and garbage-collection jobs to reclaim unused memory chunks, but it does not include prune jobs due to utilizing the sync option "Remove vanished" when copying from PBS-Primary. PBS-Secondary notably lives completely on an external-SSD (QEMU disk and datastore) connected to my R640 via USB. The external-SSD is partitioned with 2 volumes: 1) A volume directly exposed to Proxmox upon Proxmox boot, containing the PBS-Secondary QEMU image; 2) A volume exposed to Proxmox only after PBS-Secondary initializes, containing the replica datastore of PBS-Primary. This offsite contain is crucial in disaster-recovery when experiencing PBS-Primary and/or TrueNAS failure, acting as a second restoration target directly from Proxmox > Storage > PBS-Secondary. Additionally, since the QEMU image lives on the external-SSD with the datastore drive, the device can be used as a theorethical remote restoration device for my entire homelab in a different environment with different equipment if necessary.

### Automation (Ubuntu-Automation)

Ubuntu-Automation, simply named "Automation" in Proxmox, is the 5th VM to initialize upon Proxmox boot, responsible for acting as the control plane of my homelab and hosting infrastructure automation services. Like Ubuntu-Monitoring and Ubuntu-Security, these services (while comparable in discipline) are independent and lighter-weight in comparison to the infrastructure VMs previously mentioned (OPNsense, TrueNAS, PBS). Because of this, these services are group-hosted under the same Ubuntu server VM to be managed as a centralized "Automation Stack". Together, these services expedite routine tasks and operations with intrinsic documentation for improved system flexibility and reliability. The following hosted services here currently include: Git, Ansible, Terraform, SSH management, and automation scripts. 

Git tracks filesystem changes pertaining to the documentation and configuration of every node within my internal-LAN and saves them as a local repository, which is then cloned to Github as an additional off-site copy for version control and disaster-recovery. 

Ansible is responsible for configuration management and deployment, primarily for existing systems, with stepped-operations defined in playbooks. 

Terraform is responsible for scalable provisioning and initialization of hosts as IaC, primarily for new systems.

### PLANNED: Monitoring (Ubuntu-Monitoring)

Ubuntu-Monitoring, simply named "Monitoring" in Proxmox, is the 6th VM to initialize upon Proxmox boot, responsible for absolute observation of every possible node within the internal-LAN via infrastructure monitoring services. Like Ubuntu-Automation and Ubuntu-Security, these services (while comparable in discipline) are independent and lighter-weight in comparison to the infrastructure VMs previously mentioned (OPNsense, TrueNAS, PBS). Because of this, these services are group-hosted under the same Ubuntu server VM to be managed as a centralized "Monitoring Stack". Together, these services improve administrative efficiency, control, and capability by centralizing system information, processing it into meaningful insights, and reporting it to actionable authorities. The following hosted services here will include: Prometheus, Node Exporter, SNMP Exporter, Blackbox Exporter, Grafana, Alertmanager, Loki (possibly), and Syslog ingestion.

Prometheus imports metrics from exposed nodes into a centralized database supporting flexible querying.

Node Exporter exposes metrics from nodes, specifically focusing on hardware and OS metrics like resource usage and environmental conditions.

SNMP Exporter exposes metrics from nodes, specifically focusing on network hardware supporting SNMP for traffic and event cataloging.

Blackbox Exporter exposes metrics from external nodes, specifically targetting systems and services beyond immediate control via protocols like HTTP/HTTPS/DNS/TCP/ICMP/etc.

Grafana creates customizable dashboards from imported metrics, allowing visualization for informed management of infrastructure nodes, commonly paired with Prometheus.

Alertmanager manages alerts generated by Prometheus including tasks like filtering, grouping, deduplicating, and routing notifications to administrative channels for rapid response-time management of infrastructure systems.

Loki aggregates logs from targetted nodes for storage and querying, specifically focusing on these functions by means of indexed metadata.

Syslog ingestion includes services that aggregate system metrics and events specifically reported in the Syslog computing standard.

### PLANNED: Security (Ubuntu-Security)

Ubuntu-Security, simply named "Security" in Proxmox, is the 6th VM to initialize upon Proxmox boot, responsible for testing, monitoring, and auditing system metrics from an angle of asset protection rather than resource allocation or efficiency. Like Ubuntu-Automation and Ubuntu-Monitoring, these services (while comparable in discipline) are independent and lighter-weight in comparison to the infrastructure VMs previously mentioned (OPNsense, TrueNAS, PBS). Because of this, these services are group-hosted under the same Ubuntu server VM to be managed as a centralized "Security Stack". Together, these services identify and safeguard against threats and attacks to system assets, improving confidentiality, integrity, and accountability. The following hosted services here will include: Wazuh, OSSEC agents, vulnerability scanning, audit logging, Fail2Ban, log analysis, threat detection, OpenVAS (possibly), Suricata dashboard (possibly), and YARA (possibly).

### PLANNED: Catalyst 3750

My Catalyst 3750 is a hardware switching appliance acquired for physical experience with Cisco equipment configuration and management that I might expect to experiment with and learn commutable skills for professional environments. It serves as an access-layer switch to solidify my learning from studying for CCNA certification, and will be the network uplink for planned hardware projects including exploring an ASA firewall, Firepower firewall, and WLC + APs setup into a WLAN. 

The Catalyst 3750 uplinks to my collapsed-core/distribution layer Nexus switch using quad-banded 1Gb Ethernet cables utilizing GLC-T transceivers on both ends of each link. The Catalyst 3750 also uplinks to the Dell R640 server utilizing dual-banded Ethernet cables as a fallback as well as for proper STP configuration testing.

### PLANNED: Nexus 3064

My Nexus 3064 is a hardware switching appliance acquired for physical experience with advanced Cisco equipment configuration and management I might expect to experiment with and learn commutable skills for professional environments. It serves as a collapsed-core/distribution layer switch to learn configuring for NX-OS and compare it to the simpler Cisco IOS. Better optimized for virtualization environments like data-centers, the Nexus 3064 features much higher throughput capabilities with less familiar hardware technologies including fiber-optic cabling and SFP+ ports requiring transceivers to convert between cable-type. 

The Nexus 3064 is an uplink for the Catalyst 3750 access-layer switch using quad-banded 1Gb Ethernet cables, and uplinks to the Dell R640 server using dual-banded 10Gb fiber-optic cables. The Nexus 3064 features 2 currently unutilized management ports, with planned connection contingent on Ethernet-port access-expansion to my external-LAN. Nothing else connects directly to or from this device.

### PLANNED: Meraki MS220

The Meraki MS220 is a hardware switching apppliance acquired for free, and frankly subsequently forgotten about. Possible plans include potentially implementing and configuring it as a access-layer switch for my external-LAN to solve my access-port storage problem, but this may require an active smart-licensing subscription from Cisco I will not be buying if so.

### PLANNED: ASA 5520

The ASA 5520 is a hardware security appliance acquired for free that I might use to gain physical experience configuring and managing for learning traditional firewall deployment and capabilities, possibly for commutable skills in a professional environment (but is already very outdated, and guaranteed to become less relevant over time).

The ASA 5520 is planned to be connected to my Catalyst 3750 switch for access and experimentation in my internal-LAN, but will not replace my OPNsense firewall.

### PLANNED: Firepower 1010

The Firepower 1010 is a hardware security appliance acquired for free that I might use to gain physical experience configuring and managing for learning more modern firewall deployment and capabilities, possibly for commutable skills in a professional environment (but is already outdated as a very early family-series model, although less so than the ASA 5520).

The Firepower 1010 is planned to be connected to my Catalyst 3750 switch for access and experimentation in my internal-LAN, but will not replace my OPNsense firewall.

### PLANNED: WLAN 2504 Controller

The WLAN 2504 Controller is a hardware WLAN appliance I acquired for free that I might use to gain physical experience configuring and managing for learning WLAN deployment and capabilities, which will definitely be a commutable skillset in professional environments despite using old hardware. The WLC is planned to be used with, at minimum, 2 AP I acquired.

The WLC is planned to uplink to my Catalyst 3750 switch for access and experimentation expanding my internal-LAN as a WLAN, and it will likely connect to the downstream APs using wired Ethernet.

### PLANNED: AP1

Access-Point-1 is a hardware WLAN appliance I acquired for free that I might use to gain physical experience configuring and managing for learning WLAN deployment and capabilities, which will definitely be a commutable skillset in a professional environment despite using old hardware. While the specific model I will use has not been chosen, it will be used in conjunction with the WLAN 2504 Controller and another AP to experiment with proper WLAN deployment, such learning configuration for proper device roaming.

AP1 is planned to uplink to my WLC likely via wired Ethernet, and may potentially connect to the other AP(s) as well in testing various WLAN deployment topologies. It will serve my phone wirelessly for testing purposes when the time comes.

### PLANNED: AP2

Access-Point-2 is a hardware WLAN appliance I acquired for free that I might use to gain physical experience configuring and managing for learning WLAN deployment and capabilities, which will definitely be a commutable skillset in a professional environment despite using old hardware. While the specific model I will use has not been chosen, it will be used in conjunction with the WLAN 2504 Controller and another AP to experiment with proper WLAN deployment, such learning configuration for proper device roaming.

AP2 is planned to uplink to my WLC likely via wired Ethernet, and may potentially connect to the other AP(s) as well in testing various WLAN deployment topologies. It will serve my phone wirelessly for testing purposes when the time comes.

### Windows Server 2025

"Windows-Server-2025", as named in Proxmox, is a Windows Server VM currently unused. It is planned to be used for learning Active Directory and Windows system tools for troubleshooting and maintenance of Windows hosts. The Active Directory service is planned to serve quickly-provisioned Windows hosts, simulating a real enterprise environment where I will learn to implement and maintain user access control via GPOs, ACLs, etc. 

This Windows Server is possibly planned to act as the AAA server for my internal-LAN infrastructure, but this is yet undecided. As such, this VM is placed within the Lab subnet for aforementioned quick-provisioned lab hosts.

### Wiki.js

"Wiki.js", as named in Proxmox, is an Ubuntu server VM responsible for documentation beyond the scope and depth covered in Ubuntu-Automation. Specifically, the VM hosts a Wiki.js service utilizing PostgreSQL, with possible future services including reverse proxy and authentication. The Wiki.js instance is currently restricted to internal-access only, but is planned to be publicly exposed with routable FQDN. Accessed via web GUI, Wiki.js serves as a personal consolidated knowledge base hosting notes in Markdown format about anything related to IT. Currently, my Wiki.js features 3 encompassing sections: 1) Compendium; 2) Courses; 3) Homelab.

The Compendium section features an extensive glossary of every IT-related term I have come across, including translating acronyms and personal notetaking when I research the related term. Each term is planned to be tagged for grouping into broader categories, and hyperlinked to my Compendium Index page for rapid reference and access. Additionally, the Compendium section features a Q&A page for questions, confused concepts, and key principles to look back on for general study and understanding.

The Courses section is designed to host my personally crafted notes taken during courses on a specific topic, typically a software or certification. These notes are deeper in exploration than listed on the topic's corresponding Compendium page. Currently, this section is largely barren due to previous course notes taken in a private Google Doc sheet, and the majority of it needs to be transferred over and formatted.

The Homelab section is designed to host all the documentation relating to my homelab and projects contained. This is planned to largely mirror my Github repository, but featuring daily notes taken as I explore provisioning and configuring services, including steps taken, questions asked, things that went wrong, troubleshooting, and thinking through concepts and future plans. Currently, this section contains a mostly complete inventory of all of my hardware, an incomplete section of diagrams of my homelab topology, and a documentary directory containing the daily notes.

Overall, my Wiki.js is designed to be a singular source of truth for everything that has/will happen in my self-guided journey of learning IT. I eventually intend to use it for professional development, pointing to it as proof of the extensive work I have taken over the past few years in hope of breaking into a career in IT and maintaining a habit of lifelong learning.

### Minecraft

Ubuntu-Minecraft, named "Minecraft" in Proxmox, is currently an unused Ubuntu server VM. It was a project exploring the capability and process of hosting a modded Minecraft gaming server on my homelab, and was my first real exercise in using Linux. The major concepts explored in this lab featured port exposure and forwarding; internal routing, NAT, and firewall control; and file management and configuration. Due to the inbound connectivity and security requirements, this was my first DMZ VM, but was later migrated behind my Wiki.js VM due to perceived importance.

## Design Schema

My homelab internal addressing scheme is designed to provide a holistic, predictable relationship between VMID allocation, VLAN assignment, and IPv4 assignment on a basis of granular functionality. 

VMIDs are the broadest categorized functional domains, grouped in hundred-wide ranges intended to only range from 100 to 999.

VLANs are more granular categorizations within their parent VMID domain directly matching the leading two digits of their relevant VMID (i.e VLAN 13 = VMIDs 130 - 139).
There are 2 exceptions to this: 1) If the interface is an internal default gateway; 2) If the interface resides in the external Home LAN.
1) All internal default gateways are subinterfaces on OPNsense, relating to their subnet instead of the parent VMID.
2) All external Home LAN interfaces use the arbitrary Native VLAN = 99.
Additionally, all VLANs and associated subnets are designed to follow the principle of least privilege for damage containment from a hypothetical system compromisation.

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

The Management subnet exists to host critically necessary services and administratively-accessable interfaces to those services for internal-LAN functionality. Hosts within this subnet are given unrestricted access to most other nodes within the internal-LAN for required operational purposes, and are almost entirely network nodes that boot before anything else.

The Management Subnet currently contains:
- Proxmox Host
- OPNsense Management Interface
- TrueNAS Management Interface
- Catalyst 3750 Management Interface
- Nexus 3064 Management Interface

The Management Subnet is planned to contain:
- Meraki MS220 Management Interface
- ASA 5520
- Firepower 1010
- WLAN 2504 Controller
- AP Management Interfaces

The Management Subnet should only be accessed by:
- Automation VM
- Monitoring VM
- Security VM
- Administrative workstations (via VPN)

### Infrastructure Subnet

The Infrastructure subnet exists to host services less critically necessary than those within the Management subnet, but are still very fundamentally important to internal-LAN operation. They more so manage the required services than rank amongst them and thus comparatively require lesser operational freedom, partially due to being collected stacks of lighter-weight services under an Ubuntu OS umbrella.

The Infrastructure Subnet currently contains:
- Automation VM

The Infrastructure Subnet is planned to contain:
- Monitoring VM
- Security VM

### Storage Subnet

The Storage subnet exists to exclusively serve the storage services and those in direct contact with them. This is due to storage traffic requiring high bandwidth, being latency sensitive, and utmost confidentiality in handling the data of the entire internal-LAN. 

The Storage Subnet currently contains:
- TrueNAS

The Storage Subnet is planned to contain:
- NFS
- SMB
- iSCSI

The Storage Subnet should only be accessed by:
- PBS
- Proxmox
- Windows Server (potentially)

### Backups Subnet

The Backups subnet exists to exclusively serve the backup services and those in direct contact with them. Similar to hosts within the storage subnet, backups are the last line of defense for a network handling old and current data of the entire internal-LAN and should be secured with utmost confidentiality.

The Backups Subnet currently contains:
- PBS-Primary
- PBS-Secondary

The Backups Subnet should only be accessed by:
- Proxmox
- TrueNAS
- Automation VM
- Monitoring VM

### Labs Subnet

The Labs subnet exists to host services only reachable from within the internal-LAN, largely with the intention for learning and experimenting with services for professional development, rather the operational necessity within the internal-LAN. These services are modified frequently, may intentionally break, and may run less-than-stable software.

The Labs Subnet currently contains:
- Windows Server 2025

The Labs Subnet is planned to contain:
- Client VMs

The Labs Subnet should only be accessed by:
- Storage (limited)
- Windows updates
- Domain services
- Internally-requested Internet services

### DMZ Subnet

The DMZ subnet exists to host services intended to be publicly and internally reachable, while maintaining heavily restricted access to the internal-LAN resources required to do so.

The DMZ Subnet currently contains:
- Wiki.js
- Minecraft VM

The DMZ Subnet is planned to contain:
- Reverse Proxy
- Web services

The DMZ Subnet should only be accessed by:
- Internet
- Reverse Proxy

### Out-of-Band/External-LAN

The Out-of-Band/External-LAN subnet exists to serve clients on my residential network, provide Internet access to my internal-LAN edge interfaces, and provide access to pre-initialized internal-LAN-critical hardware management interfaces.

The Out-of-Band/External-LAN Subnet currently contains:
- Workstation PC
- Nokia Beacon Router
- Proxmox WAN Bridge 
- OPNsense WAN Interface

The Out-of-Band/External-LAN Subnet is planned to contain:
- iDRAC
- UPS Management Interface
- Meraki MS220 (potentially)

The Out-of-Band/External-LAN Subnet should only be accessed by:
- Outbound Internet traffic
- Inbound Internet DMZ traffic
