# Topology

The following documentation is intended to explain how each node in my environment relate to one another, describing relationships and dependencies rather than individual port numbers or interfaces. For specific details, see "homelab/documentation/inventory/hardware.md". For general information regarding what each node is and the role it plays, see "homelab/documentation/architecture/nodes.md" instead.

## Node List

- Optical Network Transceiver (ONT)
- SOHO Router
- External-LAN Switch (Planned)
- Personal Power Strip / Surge Protector
- Homelab Power Strip / Surge Protector
- Workstation PC
	- Workstation PC Network Interface Card (NIC)
- Uninterruptible Power Supply (UPS) (Planned)
- Server
	- Server Chassis
	- Server Motherboard
	- Server CPUs
	- Server Memory
	- iDRAC
	- Server Boot Adapter
		- Local Storage
		- Hypervisor
			- Hypervisor External-LAN Virtual Bridge
			- Hypervisor Internal-LAN Virtual Bridge
			- Hypervisor Bond Interface
			- Hypervisor Infrastructure VLAN Interface
			- Hypervisor Storage VLAN Interface
			- Hypervisor Backups VLAN Interface
			- Hypervisor DMZ VLAN Interface
	- Server Backplane
	- Server Storage Controller
	- Server Storage Drives
	- Server Network Interface Card (NIC) A
	- Server Network Interface Card (NIC) B
- Router/Firewall VM
	- Router/Firewall VM WAN Interface
	- Router/Firewall VM LAN Interface
	- Router/Firewall VM Management VLAN Interface
	- Router/Firewall VM Infrastructure VLAN Interface
	- Router/Firewall VM Storage VLAN Interface
	- Router/Firewall VM Backups VLAN Interface
	- Router/Firewall VM Labs VLAN Interface
	- Router/Firewall VM DMZ VLAN Interface
- Storage VM
	- Storage VM Performance Datastore
	- Storage VM Bulk Datastore
	- Storage VM Backups Datastore
- Primary Backups VM
- Secondary Backups VM
- Automation VM
- Monitoring VM (Planned)
- Security VM (Planned)
- Labs VMs
- DMZ VMs
- Access Switch
- Core/Distribution (C/D) Switch
- Adaptive Security Appliance (ASA) Firewall (Planned)
- Wireless LAN Controller (WLC) (Planned)
- Access Point 1 (AP1)
- Access Point 2 (AP2)

### Domain Spaces - External-LAN & Internal-LAN

The domain spaces comprising my homelab journey comprise what I refer to throughout my documentation as my "External-LAN" and "Internal-LAN". My External-LAN is my residential home network that existed before my IT journey, servicing my workstation PC, phone, and all other devices used by my family members (not covered here). My Internal-LAN is my isolated homelab network that services all of my enterprise networking hardware and virtualized hosts.

Nodes will be categorized and described as within the External-LAN, Internal-LAN, or between them. Nodes will be roughly ordered by data flow dependency.

### Structure

PARENT NODE (if applicable) - NODE

Dependent on -> NODE -> Dependent to

- Node Type: Is this node classified as physical or virtual?
- Power Source: Where does this node receive its power from?
- Dependencies: What is required for this node to start and/or function?
- Dependencies Connection Type: What connection type does this node use to reach services required for this node's operation?
- Dependents: What subsequent nodes rely on this one to start and/or function?
- Dependents Connection Type: What connection type does this node use to distribute its hosted services?

## External LAN Nodes

### Optical Network Transceiver (ONT)

ISP -> ONT -> External-LAN (SOHO) Router

- Node Type: Physical
- Power Source: Shared Home Circuit
- Dependencies: ISP
- Dependencies Connection Type: Fiber Ethernet (FttP)
- Dependents: External-LAN (SOHO) Router
- Dependents Connection Type: Copper Ethernet (Flat)

### SOHO Router

ONT -> SOHO Router -> Workstation PC + Server NIC Port A

- Node Type: Physical
- Power Source: Dedicated Home Circuit
- Dependencies: ONT
- Dependencies Connection Type: Copper Ethernet (Flat)
- Dependents: Workstation PC, Server NIC Port A
- Dependents Connection Type: Copper Ethernet (UTP)

### Homelab Power Strip / Surge Protector

Dedicated Home Circuit -> Homelab Power Strip / Surge Protector -> External-LAN Switch + Server PSUs + Access Switch + Core/Distribution Switch + ASA Firewall + WLC + AP1 + AP2

- Node Type: Physical
- Power Source: Dedicated Home Circuit
- Dependencies: Dedicated Home Circuit
- Dependencies Connection Type: Power Cables
- Dependents: External-LAN Switch, Server PSUs, Access Switch, Core/Distribution Switch, ASA Firewall, WLC, AP1, AP2
- Dependents Connection Type: Power Cables

### PLANNED: External-LAN Switch

SOHO Router -> External-LAN Switch -> Workstation PC NIC + Server iDRAC + Server NIC A Port + UPS

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: External-LAN (SOHO) Router
- Dependencies Connection Type: Copper Ethernet (UTP)
- Dependents: Workstation PC NIC, Server iDRAC, Server NIC A, UPS
- Dependents Connection Type: Copper Ethernet (UTP)

### Personal Power Strip / Surge Protector

Shared Home Circuit -> Power Strip / Surge Protector -> Workstation PC (+ Workstation Monitors + Workstation DAC + Workstation Sound Mixer) + External-LAN Switch

- Node Type: Physical
- Power Source: Shared Home Circuit
- Dependencies: Shared Home Circuit
- Dependencies Connection Type: Power Cables
- Dependents: Workstation PC (+ Workstation Monitors + Workstation DAC + Workstation Sound Mixer)
- Dependents Connection Type: Power Cables

### Workstation PC NIC

SOHO Router + Workstation PC -> Workstation PC NIC ->  Access Switch (Intermittent) / Core/Distribution Switch (Intermittent)

- Node Type: Physical
- Power Source: Workstation PC
- Dependencies: Workstation PC, External-LAN (SOHO) Router
- Dependencies Connection Type: Copper Ethernet (UTP)
- Dependents: Access Switch (Intermittent), Core/Distribution Switch (Intermittent)
- Dependents Connection Type: Console (Serial, USB-A to DB9 Connector)

### PLANNED: Uninterruptible Power Supply (UPS)

Dedicated Home Circuit + External-LAN Switch -> UPS -> Server PSUs + Access Switch + Core/Distribution Switch + ASA Firewall + WLC + AP1 + AP2

- Node Type: Physical
- Power Source: Dedicated Home Circuit
- Dependencies: Dedicated Home Circuit, External-LAN Switch
- Dependencies Connection Type: Power Cables, Copper Ethernet (UTP)
- Dependents: Server PSUs, Access Switch, Core/Distribution Switch, ASA Firewall, WLC, AP1, AP2
- Dependents Connection Type: Power Cables

### Server - PSUs

UPS -> Server PSUs -> Server Motherboard

- Node Type: Physical
- Power Source: UPS
- Dependencies: Dedicated Home Circuit, UPS
- Dependencies Connection Type: Power Cables
- Dependents: Server Motherboard
- Dependents Connection Type: Internal

### PLANNED: Server - iDRAC 

External-LAN Switch + Server Motherboard -> iDRAC -> Server

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: External-LAN Switch, Server Motherboard
- Dependencies Connection Type: Copper Ethernet (UTP), Internal
- Dependents: Server
- Dependents Connection Type: Internal

### Hypervisor External-LAN Virtual Bridge

Server NIC A + Hypervisor -> Hypervisor External-LAN Virtual Bridge -> Router/Firewall VM WAN Interface

- Node Type: Virtual
- Power Source: Server PSUs
- Dependencies: Server NIC A, Hypervisor
- Dependencies Connection Types: Internal, Virtual
- Dependents: Router/Firewall VM WAN Interface
- Dependents Connection Type: Virtual

### Router/Firewall VM WAN Interface

Hypervisor External-LAN Virtual Bridge + Router/Firewall VM -> Router/Firewall VM WAN Interface -> All VMs and Internal Services

- Node Type: Virtual
- Power Source: Server PSUs
- Dependencies: Hypervisor External-LAN Virtual Bridge, Router/Firewall VM
- Dependents: All VMs and Internal Services
- Dependencies Connection Type: Virtual
- Dependents Connection Type: Virtual

## Between Nodes

### Workstation PC

Personal Power Strip / Surge Protector + VPN -> Workstation PC -> Workstation PC NIC

- Node Type: Physical
- Power Source: Personal Power Strip / Surge Protector
- Dependencies: Personal Power Strip / Surge Protector, VPN
- Dependencies Connection Types: Power Cables, Virtual
- Dependents: Workstation PC NIC
- Dependents Connection Type: Internal

### Server - Motherboard

Server PSUs -> Server Motherboard -> Server Memory + Server CPUs + Server Storage Controller + Server Backplane + Server Storage Drives + Server Boot Storage Controller

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs
- Dependencies Connection Type: Internal
- Dependents: Server Memory, Server CPUs, Server Storage Controller, Server Backplane, Server Storage Drives, Server Boot Storage Controller
- Dependents Connection Type: Internal

### Server - Memory

Server PSUs + Server Motherboard -> Server Memory -> Server CPUs

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard
- Dependencies Connection Type: Internal
- Dependents: Server CPUs
- Dependents Connection Type: Internal

### Server - CPUs

Server PSUs + Server Motherboard + Server Memory -> Server CPUs -> Server Storage Controller, Server Backplane, Server Boot Storage Controller, Server NICs (A, B)

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard, Server Memory
- Dependencies Connection Type: Internal
- Dependents: Server Storage Controller, Server Backplane, Server Boot Storage Controller, Server NICs (A, B)
- Dependents Connection Type: Internal

### Server - Storage Controller

Server PSUs + Server Motherboard + Server Memory + Server CPUs -> Server Storage Controller -> Server Backplane

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard, Server Memory, Server CPUs
- Dependencies Connection Type: Internal
- Dependents: Server Backplane
- Dependents Connection Type: Internal

### Server - Backplane

Server PSUs + Server Motherboard + Server Memory + Server CPUs + Server Storage Controller -> Server Backplane -> Server Storage Drives

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard, Server Memory, Server CPUs, Server Storage Controller
- Dependencies Connection Type: Internal
- Dependents: Server Storage Drives
- Dependents Connection Type: Internal

### Server - Boot Storage Controller

Server PSUs + Server Motherboard + Server Memory + Server CPUs -> Server Boot Storage Controller -> Storage Drives

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard, Server Memory, Server CPUs
- Dependencies Connection Type: Internal
- Dependents: Storage Drives
- Dependents Connection Type: Internal

### Server - Storage Drives

Server PSUs + Server Motherboard + Server Memory + Server CPUs + Server Storage Controller + Server Backplane + Server Boot Storage Controller -> Storage Drives -> Local Storage + Storage VM Datastores (Performance, Bulk, Backups)

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard, Server Memory, Server CPUs, Server Storage Controller, Server Backplane, Server Boot Storage Controller
- Dependencies Connection Type: Internal
- Dependents: Local Storage, Storage VM Datastores (Performance, Bulk, Backups)

### Server

Server PSUs + Server Motherboard + Server Memory + Server CPUs + Server Storage Controller + Server Backplane + Server Storage Drives + iDRAC + Server Boot Storage Controller -> Server -> Server NICs (A, B) + Local Storage + Hypervisor

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server PSUs, Server Motherboard, Server Memory, Server CPUs, Server Storage Controller, Server Backplane, Server Storage Drives, iDRAC, Server Boot Storage Controller
- Dependencies Connection Types: Power Cables, Internal, Virtual
- Dependents: Server NICs (A, B), Local Storage, Hypervisor
- Dependents Connection Type: Virtual

### Server - Network Interface Card (NIC) A

SOHO Router + Server -> Server NIC A -> Hypervisor External-LAN Virtual Bridge

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: External-LAN (SOHO) Router, Server
- Dependencies Connection Type: Copper Ethernet (UTP), Internal
- Dependents: Hypervisor External-LAN Virtual Bridge
- Dependents Connection Type: Virtual

### Server - Local Storage

Server Boot Storage Controller + Server Storage Drives -> Local Storage -> Hypervisor + Router/Firewall VM + Storage VM

- Node Type: Virtual
- Power Source: Server PSUs
- Dependencies: Server Boot Storage Controller, Server Storage Drives
- Dependencies Connection Type: Internal
- Dependents: Hypervisor, Router/Firewall VM, Storage VM
- Dependents Connection Type: Virtual

### Hypervisor

Server -> Hypervisor -> Hypervisor Virtual Bridges (Internal-LAN, External-LAN) + Hypervisor Bond Interface + Hypervisor VLAN Interfaces (Infrastructure, Storage, Backups, DMZ) + Hypervisor NFS Datastores (Performance, Bulk, Backups)

- Node Type: Virtual
- Power Source: Server PSUs
- Dependencies: Server
- Dependencies Connection Type: Virtual
- Dependents: Hypervisor Virtual Bridges (Internal-LAN, External-LAN), Hypervisor Bond Interface, Hypervisor VLAN Interfaces (Infrastructure, Storage, Backups, DMZ), Hypervisor NFS Datastores (Performance, Bulk, Backups)
- Dependents Connection Type: Virtual

## Internal LAN Nodes

### Hypervisor Internal-LAN Virtual Bridge

Hypervisor -> Hypervisor Internal-LAN Virtual Bridge -> Hypervisor VLAN Interfaces + Hypervisor LACP/Bond Interface + Router/Firewall VM LAN Interface

- Node Type: Virtual
- Dependent on: Hypervisor
- Dependent to: Hypervisor VLAN Interfaces (Management Infrastructure, Storage, Backups, Labs, DMZ), Hypervisor LACP/Bond Interface, Router/Firewall VM LAN Interface
- Upstream Connection Type: Virtual Trunk
- Downstream Connection Type: Virtual
- Power Source: Internal-LAN Server

### Hypervisor Management VLAN Interface

Hypervisor Internal-LAN Virtual Bridge \<-> Hypervisor Management VLAN Interface -> Hypervisor Internal-LAN Virtual Bridge + Hypervisor

- Node Type: Virtual
- Dependent on: Hypervisor Internal-LAN Virtual Bridge

### Router/Firewall VM

Server + Hypervisor Virtual Bridges -> Router/Firewall VM -> Router/Firewall VM Interfaces + Router/Firewall VM VLAN Interfaces

- Node Type: Virtual
- Dependent on: Server (Local Storage), Hypervisor Virtual Bridges (Internal-LAN, External-LAN)
- Dependent to: Router/Firewall Interfaces (LAN, WAN), Router/Firewall VLAN Interfaces (Management, Infrastructure, Storage, Backups, Labs, DMZ)

### Router/Firewall VM LAN Interface

Router/Firewall VM VLAN Interfaces (Management, Infrastructure, Storage, Backups, Labs, DMZ) -> Router/Firewall VM LAN Interface -> Hypervisor Internal-LAN Virtual Bridge

- Node Type: Virtual
- Dependent on: 



### TrueNAS

TrueNAS is the 2nd VM to initialize automatically upon Proxmox boot, responsible for managing the the physical hard drives directly attached to the R640 and provided to TrueNAS via HBA passthrough. TrueNAS connects to Proxmox vmbr1 (Layer-2), which uplinks to vtnet1 (Layer-3). TrueNAS converges the exposed storage drives into separate storage pools, grouped by function, to serve other hosts. The storage pools are then exposed back to Proxmox via NFS to be accessed and utilized by other hosts. While (currently) most other Proxmox hosts require TrueNAS storage pools to be exposed for creation and initialization, OPNsense, TrueNAS, and Ubuntu-Automation share the "Local" storage pool with the Proxmox OS. 

Unlike most other hosts (except OPNsense), TrueNAS has 2 interfaces: 1) In the Management VLAN/subnet; 2) In the dedicated Storage VLAN/subnet.
1) The Management interface allows dashboard access for administrative purposes, optimized for trust;
2) The Storage interface allows dedicated access for trusted-services to storage-access channels, optimized for performance.

### Proxmox Backup Server (PBS-Primary)

Proxmox Backup Server (PBS-Primary) is the 3rd VM to initialize upon Proxmox boot, responsible for orchestrating backup management include verifying file integrity, pruning snapshots exceeding grouped limits, garbage-collecting memory chunks no longer being associated with any snapshots, and syncing the entire backup repository with PBS-Secondary (and eventually Github). PBS-Primary is the crux of my homelab's disaster-recovery operations and version control restoration. PBS restoration has also proved an invaluable resource in Proxmox VMID management.

### Proxmox Backup Server (PBS-Secondary)

Proxmox Backup Server (PBS-Secondary) is the 4th VM to initialize upon Proxmox boot, responsible as an off-site backup option in the event PBS-Primary fails. PBS-Secondary is a direct copy of the last sync job from PBS-Primary, and does not receive a unique backup job directly from Proxmox. As a secondary storage repository, PBS-Secondary also performs file integrity verification and garbage-collection jobs to reclaim unused memory chunks, but it does not include prune jobs due to utilizing the sync option "Remove vanished" when copying from PBS-Primary. PBS-Secondary notably lives completely on an external-SSD (QEMU disk and datastore) connected to my R640 via USB. The external-SSD is partitioned with 2 volumes: 1) A volume directly exposed to Proxmox upon Proxmox boot, containing the PBS-Secondary QEMU image; 2) A volume exposed to Proxmox only after PBS-Secondary initializes, containing the replica datastore of PBS-Primary. This offsite contain is crucial in disaster-recovery when experiencing PBS-Primary and/or TrueNAS failure, acting as a second restoration target directly from Proxmox > Storage > PBS-Secondary. Additionally, since the QEMU image lives on the external-SSD with the datastore drive, the device can be used as a theorethical remote restoration device for my entire homelab in a different environment with different equipment if necessary.

### Automation (Ubuntu-Automation)

Ubuntu-Automation, simply named "Automation" in Proxmox, is the 5th VM to initialize upon Proxmox boot, responsible for acting as the control plane of my homelab and hosting infrastructure automation services. Like Ubuntu-Monitoring and Ubuntu-Security, these services (while comparable in discipline) are independent and lighter-weight in comparison to the infrastructure VMs previously mentioned (OPNsense, TrueNAS, PBS). Because of this, these services are group-hosted under the same Ubuntu server VM to be managed as a centralized "Automation Stack". Together, these services expedite routine tasks and operations with intrinsic documentation for improved system flexibility and reliability. The following hosted services here currently include: Git, Ansible, Terraform, SSH management, and automation scripts. 

- Git tracks filesystem changes pertaining to the documentation and configuration of every node within my internal-LAN and saves them as a local repository, which is then cloned to Github as an additional off-site copy for version control and disaster-recovery. 

- Ansible is responsible for configuration management and deployment, primarily for existing systems, with stepped-operations defined in playbooks. 

- Terraform is responsible for scalable provisioning and initialization of hosts as IaC, primarily for new systems.

### PLANNED: Monitoring (Ubuntu-Monitoring)

Ubuntu-Monitoring, simply named "Monitoring" in Proxmox, is the 6th VM to initialize upon Proxmox boot, responsible for absolute observation of every possible node within the internal-LAN via infrastructure monitoring services. Like Ubuntu-Automation and Ubuntu-Security, these services (while comparable in discipline) are independent and lighter-weight in comparison to the infrastructure VMs previously mentioned (OPNsense, TrueNAS, PBS). Because of this, these services are group-hosted under the same Ubuntu server VM to be managed as a centralized "Monitoring Stack". Together, these services improve administrative efficiency, control, and capability by centralizing system information, processing it into meaningful insights, and reporting it to actionable authorities. The following hosted services here will include: Prometheus, Node Exporter, SNMP Exporter, Blackbox Exporter, Grafana, Alertmanager, Loki (possibly), and Syslog ingestion.

- Prometheus imports metrics from exposed nodes into a centralized database supporting flexible querying.

- Node Exporter exposes metrics from nodes, specifically focusing on hardware and OS metrics like resource usage and environmental conditions.

- SNMP Exporter exposes metrics from nodes, specifically focusing on network hardware supporting SNMP for traffic and event cataloging.

- Blackbox Exporter exposes metrics from external nodes, specifically targetting systems and services beyond immediate control via protocols like HTTP/HTTPS/DNS/TCP/ICMP/etc.

- Grafana creates customizable dashboards from imported metrics, allowing visualization for informed management of infrastructure nodes, commonly paired with Prometheus.

- Alertmanager manages alerts generated by Prometheus including tasks like filtering, grouping, deduplicating, and routing notifications to administrative channels for rapid response-time management of infrastructure systems.

- Loki aggregates logs from targetted nodes for storage and querying, specifically focusing on these functions by means of indexed metadata.

- Syslog ingestion includes services that aggregate system metrics and events specifically reported in the Syslog computing standard.

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
