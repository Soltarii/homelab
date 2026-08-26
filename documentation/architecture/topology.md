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

Nodes will be categorized and described as within the External-LAN, Internal-LAN, or between them. Nodes will be roughly ordered by data flow/boot dependency.

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
- Dependencies Connection Type: Fiber Optic (FttP)
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

Note: Eventually switch power source to Homelab Power Strip / Surge Protector & dependents strictly to External-LAN Switch.

### Homelab Power Strip / Surge Protector

Dedicated Home Circuit -> Homelab Power Strip / Surge Protector -> External-LAN Switch + Server PSUs + Access Switch + Core/Distribution Switch + ASA Firewall + WLC + AP1 + AP2

- Node Type: Physical
- Power Source: Dedicated Home Circuit
- Dependencies: Dedicated Home Circuit
- Dependencies Connection Type: Power Cables
- Dependents: External-LAN Switch, Server PSUs, Access Switch, Core/Distribution Switch, ASA Firewall, WLC, AP1, AP2
- Dependents Connection Type: Power Cables

Note: Eventually change dependents to only nodes not powered by the UPS.

### PLANNED: External-LAN Switch

SOHO Router -> External-LAN Switch -> Workstation PC NIC + Server iDRAC + Server NIC A Port + UPS

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: External-LAN (SOHO) Router
- Dependencies Connection Type: Copper Ethernet (UTP)
- Dependents: Workstation PC NIC, Server iDRAC, Server NIC A, UPS
- Dependents Connection Type: Copper Ethernet (UTP)

### Personal Power Strip / Surge Protector

Shared Home Circuit -> Power Strip / Surge Protector -> Workstation PC (+ Workstation Monitors + Workstation DAC + Workstation Sound Mixer)

- Node Type: Physical
- Power Source: Shared Home Circuit
- Dependencies: Shared Home Circuit
- Dependencies Connection Type: Power Cables
- Dependents: Workstation PC (+ Workstation Monitors + Workstation DAC + Workstation Sound Mixer)
- Dependents Connection Type: Power Cables

### Workstation PC NIC

SOHO Router + Workstation PC -> Workstation PC NIC -> Workstation PC, Access Switch (Intermittent) / Core/Distribution Switch (Intermittent)

- Node Type: Physical
- Power Source: Workstation PC
- Dependencies: Workstation PC, External-LAN (SOHO) Router
- Dependencies Connection Type: Copper Ethernet (UTP)
- Dependents: Workstation PC, Access Switch (Intermittent), Core/Distribution Switch (Intermittent)
- Dependents Connection Type: Internal, Console (Serial, USB-A to DB9 Connector)

Note: Eventually change SOHO Router dependency to External-LAN Switch.

### PLANNED: Uninterruptible Power Supply (UPS)

Dedicated Home Circuit + External-LAN Switch -> UPS -> Server PSUs + Access Switch + Core/Distribution Switch + ASA Firewall + WLC + AP1 + AP2

- Node Type: Physical
- Power Source: Dedicated Home Circuit
- Dependencies: Dedicated Home Circuit, External-LAN Switch
- Dependencies Connection Type: Power Cables, Copper Ethernet (UTP)
- Dependents: Server PSUs, Access Switch, Core/Distribution Switch, ASA Firewall, WLC, AP1, AP2
- Dependents Connection Type: Power Cables

### Server - PSUs

Homelab Power Strip / Surge Protector -> Server PSUs -> Server Motherboard

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector
- Dependencies Connection Type: Power Cables
- Dependents: Server Motherboard
- Dependents Connection Type: Internal

Note: Eventually change power source / dependency from Homelab Power Strip / Surge Protector to UPS.

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
- Power Source: Hypervisor
- Dependencies: Server NIC A, Hypervisor
- Dependencies Connection Types: Internal, Virtual
- Dependents: Router/Firewall VM WAN Interface
- Dependents Connection Type: Virtual

### Router/Firewall VM WAN Interface

Hypervisor External-LAN Virtual Bridge + Router/Firewall VM -> Router/Firewall VM WAN Interface -> All VMs and Internal Services

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor External-LAN Virtual Bridge, Router/Firewall VM
- Dependents: All VMs and Internal Services (Requiring Internet Access)
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

Server -> Hypervisor -> Hypervisor Virtual Bridges (Internal-LAN, External-LAN) + Hypervisor LACP/Bond Interface + Hypervisor VLAN Interfaces (Infrastructure, Storage, Backups, DMZ) + Hypervisor NFS Datastores (Performance, Bulk, Backups)

- Node Type: Virtual
- Power Source: Server PSUs
- Dependencies: Server
- Dependencies Connection Type: Virtual
- Dependents: Hypervisor Virtual Bridges (Internal-LAN, External-LAN), Hypervisor LACP/Bond Interface, Hypervisor VLAN Interfaces (Infrastructure, Storage, Backups, DMZ), Hypervisor NFS Datastores (Performance, Bulk, Backups)
- Dependents Connection Type: Virtual

### External SSD

Server -> External SSD -> Secondary Backups VM

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server
- Dependencies Connection Type: Physical
- Dependents: Secondary Backups VM
- Dependents Connection Type: Virtual

## Internal LAN Nodes

### Server Network Interface Card (NIC) B

Server -> Server NIC B -> Hypervisor LACP/Bond Interface + Core/Distribution Switch

- Node Type: Physical
- Power Source: Server PSUs
- Dependencies: Server
- Dependencies Connection Type: Internal
- Dependents: Hypervisor LACP/Bond Interface, Core/Distribution Switch
- Dependents Connection Type: Virtual, Fiber Optic Cables

### Hypervisor Internal-LAN Virtual Bridge

Hypervisor -> Hypervisor Internal-LAN Virtual Bridge -> Hypervisor VLAN Interfaces + Hypervisor LACP/Bond Interface + Router/Firewall VM LAN Interface + All VMs

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor
- Dependencies Connection Type: Virtual, Trunk
- Dependents: Hypervisor VLAN Interfaces (Management Infrastructure, Storage, Backups, Labs, DMZ), Hypervisor LACP/Bond Interface, Router/Firewall VM LAN Interface, All VMs
- Dependents Connection Type: Virtual

### Hypervisor Infrastructure VLAN Interface

Hypervisor Internal-LAN Virtual Bridge -> Hypervisor Infrastructure VLAN Interface -> Infrastructure VMs (Automation, Monitoring, Security)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge
- Dependencies Connection Type: Virtual
- Dependents: Infrastructure VMs (Automation, Monitoring, Security)
- Dependents Connection Type: Virtual

### Hypervisor Storage VLAN Interface

Hypervisor Internal-LAN Virtual Bridge -> Hypervisor Storage VLAN Interface -> Storage VM

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge
- Dependencies Connection Type: Virtual
- Dependents: Storage VM
- Dependents Connection Type: Virtual

### Hypervisor Backups VLAN Interface

Hypervisor Internal-LAN Virtual Bridge -> Hypervisor Backups VLAN Interface -> Backups VMs (Primary, Secondary)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge
- Dependencies Connection Type: Virtual
- Dependents: Backups VMs (Primary, Secondary)
- Dependents Connection Type: Virtual

### Hypervisor DMZ VLAN Interface

Hypervisor Internal-LAN Virtual Bridge -> Hypervisor DMZ VLAN Interface -> DMZ VMs

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge
- Dependencies Connection Type: Virtual
- Dependents: DMZ VMs
- Dependents Connection Type: Virtual

### Hypervisor LACP/Bond Interface

Hypervisor + Server NIC B -> Hypervisor LACP/Bond Interface -> Core/Distribution Switch

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor, Server NIC B
- Dependencies Connection Type: Virtual
- Dependents: Core/Distribution Switch
- Dependents Connection Type: Physical

Note: This node can be argued to sit either in front or behind the associated NIC. I have chosen the stance it sits in front for clearer dependency-chaining.

### Router/Firewall VM

Local Storage + Hypervisor Virtual Bridges (Internal-LAN, External-LAN) -> Router/Firewall VM -> Router/Firewall VM Interfaces (WAN, LAN) + Router/Firewall VM VLAN Interfaces (Management, Infrastructure, Storage, Backups, Labs, DMZ)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Local Storage, Hypervisor Virtual Bridges (Internal-LAN, External-LAN)
- Dependencies Connection Type: Virtual
- Dependents: Router/Firewall Interfaces (WAN, LAN), Router/Firewall VLAN Interfaces (Management, Infrastructure, Storage, Backups, Labs, DMZ)
- Dependents Connection Type: Virtual

### Router/Firewall VM WAN Interface

Router/Firewall VM + Hypervisor External-LAN Virtual Bridge -> Router/Firewall VM WAN Interface -> All VMs (Requiring Internet Access)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM, Hypervisor External-LAN Virtual Bridge
- Dependencies Connection Type: Virtual
- Dependents: All VMs (Requiring Internet Access)
- Dependents Connection Type: Virtual

### Router/Firewall VM LAN Interface

Router/Firewall VM + Hypervisor Internal-LAN Virtual Bridge -> Router/Firewall VM LAN Interface -> Router/Firewall VM VLAN Interfaces (Management, Infrastructure, Storage, Backups, Labs, DMZ)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM, Hypervisor Internal-LAN Virtual Bridge
- Dependencies Connection Type: Virtual
- Dependents: Router/Firewall VM VLAN Interfaces (Management, Infrastructure, Storage, Backups, Labs, DMZ)
- Dependents Connection Type: Virtual

### Router/Firewall VM Management VLAN Interface

Router/Firewall VM LAN Interface -> Router/Firewall VM Management VLAN Interface -> All Management Interfaces (Storage VM, Access Switch, C/D Switch, ASA Firewall, Firepower Firewall, WLC, AP1, AP2, Hypervisor Internal-LAN Virtual Bridge)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM LAN Interface
- Dependencies Connection Type: Virtual
- Dependents: All Management Interfaces (Storage VM, Access Switch, C/D Switch, ASA Firewall, Firepower Firewall, WLC, AP1, AP2, Hypervisor Internal-LAN Virtual Bridge)
- Dependents Connection Type: Virtual

### Router/Firewall VM Infrastructure VLAN Interface

Router/Firewall VM LAN Interface -> Router/Firewall VM Infrastructure VLAN Interface -> All Infrastructure Interfaces (Automation VM, Monitoring VM, Security VM, Hypervisor Infrastructure VLAN Interface)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM LAN Interface
- Dependencies Connection Type: Virtual
- Dependents: All Infrastructure Interfaces (Automation VM, Monitoring VM, Security VM, Hypervisor Infrastructure VLAN Interface)
- Dependents Connection Type: Virtual

### Router/Firewall VM Storage VLAN Interface

Router/Firewall VM LAN Interface -> Router/Firewall VM Storage VLAN Interface -> All Storage Interfaces (Storage VM, Hypervisor Storage VLAN Interface)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM LAN Interface
- Dependencies Connection Type: Virtual
- Dependents: All Storage Interfaces (Storage VM, Hypervisor Storage VLAN Interface)
- Dependents Connection Type: Virtual

### Router/Firewall VM Backups VLAN Interface

Router/Firewall VM LAN Interface -> Router/Firewall VM Backups VLAN Interface -> All Backup Interfaces (Primary VM, Secondary VM, Hypervisor Backups VLAN Interface)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM LAN Interface
- Dependencies Connection Type: Virtual
- Dependents: All Storage Interfaces (Primary VM, Secondary VM, Hypervisor Backups VLAN Interface)
- Dependents Connection Type: Virtual

### Router/Firewall VM Labs VLAN Interface

Router/Firewall VM LAN Interface -> Router/Firewall VM Labs VLAN Interface -> All Labs Interfaces (Windows Server VM, Simulated Hosts VMs)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM LAN Interface
- Dependencies Connection Type: Virtual
- Dependents: All Labs Interfaces (Windows Server VM, Simulated Hosts VM)
- Dependents Connection Type: Virtual

### Router/Firewall VM DMZ VLAN Interface

Router/Firewall VM LAN Interface -> Router/Firewall VM DMZ VLAN Interface -> All DMZ Interfaces (Wiki VM, Gaming Server VM Hypervisor VM VLAN Interface)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM LAN Interface
- Dependencies Connection Type: Virtual
- Dependents: All Storage Interfaces (Wiki VM, Gaming Server VM, Hypervisor DMZ VLAN Interface)
- Dependents Connection Type: Virtual

### Storage VM

Router/Firewall VM Storage VLAN Interface + Local Storage + Server Storage Controller -> Storage VM -> Storage VM Datastores (Performance, Bulk, Backups)

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM Storage VLAN Interface, Local Storage, Server Storage Controller
- Dependencies Connection Type: Virtual
- Dependents: Storage VM Datastores (Performance, Bulk, Backups)
- Dependents Connection Type: Virtual

### Storage VM Performance Datastore

Storage VM -> Storage VM Performance Datastore -> Hypervisor NFS Performance Datastore

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Storage VM
- Dependencies Connection Type: Virtual
- Dependents: Hypervisor NFS Performance Datastore
- Dependents Connection Type: Virtual

### Storage VM Bulk Datastore

Storage VM -> Storage VM Bulk Datastore -> Hypervisor NFS Bulk Datastore

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Storage VM
- Dependencies Connection Type: Virtual
- Dependents: Hypervisor NFS Bulk Datastore
- Dependents Connection Type: Virtual

### Storage VM Backups Datastore

Storage VM -> Storage VM Backups Datastore -> Hypervisor NFS Backups Datastore

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Storage VM
- Dependencies Connection Type: Virtual
- Dependents: Hypervisor NFS Backups Datastore
- Dependents Connection Type: Virtual

### Hypervisor NFS Performance Datastore

Storage VM Performance Datastore -> Hypervisor NFS Performance Datastore -> Automation VM + Primary Backups VM + Windows Server VM + Wiki VM + Gaming Server VM

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Storage VM Performance Datastore
- Dependencies Connection Type: Virtual
- Dependents: Automation VM, Primary Backups VM, Windows Server VM, Wiki VM, Gaming Server VM
- Dependents Connection Type: Virtual

### Hypervisor NFS Bulk Datastore

Storage VM Bulk Datastore -> Hypervisor NFS Bulk Datastore -> Primary Backups VM + Windows Server VM + Wiki VM

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Storage VM Bulk Datastore
- Dependencies Connection Type: Virtual
- Dependents: Primary Backups VM, Windows Server VM, Wiki VM
- Dependents Connection Type: Virtual

### Hypervisor NFS Backups Datastore

Storage VM Backups Datastore -> Hypervisor NFS Backups Datastore -> Primary Backups VM

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Storage VM Backups Datastore
- Dependencies Connection Type: Virtual
- Dependents: Primary Backups VM
- Dependents Connection Type: Virtual

### Primary Backups VM

Router/Firewall VM Backups VLAN Interface + Hypervisor NFS Datastores (Performance, Bulk, Backups) -> Primary Backups VM -> Secondary Backups VM

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Router/Firewall VM Backups VLAN Interface, Hypervisor NFS Datastores (Performance, Bulk, Backups)
- Dependencies Connection Type: Virtual
- Dependents: Secondary Backups VM
- Dependents Connection Type: Virtual

### Secondary Backups VM

External SSD + Router/Firewall VM Backups VLAN Interface + Primary Backups VM -> Secondary Backups VM -> Hypervisor

- Node Type: Virtual
- Power Source: External SSD
- Dependencies: External SSD, Router/Firewall VM Backups VLAN, Primary Backups VM
- Dependencies Connection Type: Virtual
- Dependents: Hypervisor
- Dependents Connection Type: Virtual

### Automation VM

Hypervisor Internal-LAN Virtual Bridge + Hypervisor Infrastructure VLAN Interface + Router/Firewall Infrastructure VLAN Interface + Hypervisor NFS Performance Datastore -> Automation VM -> All Critical Infrastructure (Management VLAN) Nodes

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge, Hypervisor Infrastructure VLAN Interface, Router/Firewall Infrastructure VLAN Interface, Hypervisor NFS Performance Datastore
- Dependencies Connection Type: Virtual
- Dependents: All Critical Infrastructure (Management VLAN) Nodes
- Dependents Connection Type: Virtual

### Monitoring VM

Hypervisor Internal-LAN Virtual Bridge + Hypervisor Infrastructure VLAN Interface + Router/Firewall Infrastructure VLAN Interface + Hypervisor NFS Performance Datastore -> Monitoring VM -> All Critical Infrastructure (Management VLAN) Nodes

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge, Hypervisor Infrastructure VLAN Interface, Router/Firewall Infrastructure VLAN Interface, Hypervisor NFS Performance Datastore
- Dependencies Connection Type: Virtual
- Dependents: All Critical Infrastructure (Management VLAN) Nodes
- Dependents Connection Type: Virtual

### Security VM

Hypervisor Internal-LAN Virtual Bridge + Hypervisor Infrastructure VLAN Interface + Router/Firewall Infrastructure VLAN Interface + Hypervisor NFS Performance Datastore -> Security VM -> All Critical Infrastructure (Management VLAN) Nodes

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge, Hypervisor Infrastructure VLAN Interface, Router/Firewall Infrastructure VLAN Interface, Hypervisor NFS Performance Datastore
- Dependencies Connection Type: Virtual
- Dependents: All Critical Infrastructure (Management VLAN) Nodes
- Dependents Connection Type: Virtual

### Core/Distribution (C/D) Switch

Homelab Power Strip / Surge Protector + Hypervisor LACP/Bond Interface + Router/Firewall Management VLAN Interface -> Core/Distribution (C/D) Switch -> Access Switch

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector, Hypervisor LACP/Bond Interface, Router/Firewall Management VLAN Interface
- Dependencies Connection Types: Power Cables, Virtual
- Dependents: Access Switch
- Dependents Connection Type: Physical

Note: Eventually change Homelab Power Strip / Surge Protector power source / dependency to UPS.

### Access Switch

Homelab Power Strip / Surge Protector + Router/Firewall Management VLAN Interface + Core/Distribution (C/D) Switch -> Access Switch -> ASA Firewall + Firepower Firewall + WLC + AP1 + AP2

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector, Router/Firewall Management VLAN Interface, C/D Switch
- Dependencies Connection Types: Power Cables, Virtual, Copper Ethernet (UTP)
- Dependents: ASA Firewall, Firepower Firewall, WLC, AP1, AP2
- Dependents Connection Type: Copper Ethernet (UTP)

Note: Eventually change Homelab Power Strip / Surge Protector power source / dependency to UPS.

### Adaptive Security Appliance (ASA) Firewall

Homelab Power Strip / Surge Protector + Router/Firewall Management VLAN Interface + Access Switch -> ASA Firewall -> ?

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector, Router/Firewall VM Management VLAN Interface, Access Switch
- Dependencies Connection Types: Power Cables, Virtual, Copper Ethernet (UTP)
- Dependents: Undecided
- Dependents Connection Type: Copper Ethernet (UTP)

Note: Eventually change Homelab Power Strip / Surge Protector power source / dependency to UPS.

### Wireless LAN Controller (WLC)

Homelab Power Strip / Surge Protector + Router/Firewall Management VLAN Interface + Access Switch -> WLC -> AP1 + AP2

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector, Router/Firewall VM Management VLAN Interface, Access Switch
- Dependencies Connection Types: Power Cables, Virtual, Copper Ethernet (UTP)
- Dependents: AP1, AP2
- Dependents Connection Type: Copper Ethernet (UTP)

Note: Eventually change Homelab Power Strip / Surge Protector power source / dependency to UPS.

### Access Point 1 (AP1)

Homelab Power Strip / Surge Protector + Router/Firewall Management VLAN Interface + Access Switch + WLC -> AP1 -> Workstation PC, Phone

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector, Router/Firewall VM Management VLAN Interface, Access Switch, WLC
- Dependencies Connection Types: Power Cables, Virtual, Copper Ethernet (UTP)
- Dependents: Workstation PC, Phone
- Dependents Connection Type: Wireless Ethernet

Note: Eventually change Homelab Power Strip / Surge Protector power source / dependency to UPS.

### Access Point 2 (AP2)

Homelab Power Strip / Surge Protector + Router/Firewall Management VLAN Interface + Access Switch + WLC -> AP2 -> Workstation PC, Phone

- Node Type: Physical
- Power Source: Homelab Power Strip / Surge Protector
- Dependencies: Homelab Power Strip / Surge Protector, Router/Firewall VM Management VLAN Interface, Access Switch, WLC
- Dependencies Connection Types: Power Cables, Virtual, Copper Ethernet (UTP)
- Dependents: Workstation PC, Phone
- Dependents Connection Type: Wireless Ethernet

Note: Eventually change Homelab Power Strip / Surge Protector power source / dependency to UPS.

### Labs VMs

Hypervisor Internal-LAN Virtual Bridge + Router/Firewall Labs VLAN Interface + Hypervisor NFS Datastores (Performance, Bulk) -> Lab VMs -> Lab VMs

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge, Router/Firewall Labs VLAN Interface, Hypervisor NFS Datastores (Performance, Bulk)
- Dependencies Connection Type: Virtual
- Dependents: Lab VMs
- Dependents Connection Type: Virtual

### DMZ VMs

Hypervisor Internal-LAN Virtual Bridge + Hypervisor DMZ VLAN Interface + Router/Firewall DMZ VLAN Interface + Hypervisor NFS Datastores (Performance, Bulk) -> DMZ VMs

- Node Type: Virtual
- Power Source: Hypervisor
- Dependencies: Hypervisor Internal-LAN Virtual Bridge, Hypervisor DMZ VLAN Interface, Router/Firewall VM DMZ VLAN Interface, Hypervisor NFS Datastores (Performance, Bulk)
- Dependencies Connection Type: Virtual
- Dependents: Inbounding-Connecting Internet Nodes
- Dependents Connection Type: Virtual
