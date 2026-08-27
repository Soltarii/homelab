# Boot Order

The following documentation is intended to explain what sequence every major node should take to properly power on and operate and briefly explain why. The inverse order should be followed for node shutdown. This document closely aligns to the relations and dependencies of every node, which is described in more detail in "homelab/documentation/architecture/topology.md". For a detailed explanation of the purpose of every node, see "homelab/documentation/architecture/nodes.md" instead.

Nodes denoted with an asterisk (*) should be assumed as permanently online, but are ordered in boot sequence in case they are not.

In general, all hardware nodes should be powered on before software nodes. Not included are my Workstation PC and associated Personal Power Strip / Surge Protector that exclusively serves it. The Workstation PC is my admin terminal for management access into the Internal-LAN and can considered flexible anywhere within the list after Phase 1.

## Phase 1 - Power and WAN

1) Homelab Power Strip / Surge Protector

	Any External-LAN hardware infrastructure that does not plug directly into either the Shared Home Circuit or Dedicated Home Circuit will require this node as an intermediary for power.

2) Optical Network Transceiver (ONT) *

	The ONT is required for the External-LAN (SOHO) router to communicate with the ISP for Internet connectivity.

3) External-LAN (SOHO) Router *

	The External-LAN (SOHO) Router is the default gateway for all External-LAN devices as well as the intermediary of Internal-LAN nodes for Internet connectivity. 

4) External-LAN Switch (Planned) *

	The (planned) External-LAN Switch will expand port availability for all nodes requiring physical connection to the SOHO Router.

5) Uninterruptible Power Supply (UPS) (Planned)

	The (planned) UPS will control power for all Internal-LAN nodes, ideally connected to the External-LAN Switch for WoL remote toggling control.

## Phase 2 - Physical Switching

The C/D and Access Switches will be connected to the same outlet group that powers on simultaneously, and thus share the 6th & 7th positions.

6-7) Core/Distribution (C/D) Switch (Nexus 3064-X)

	The C/D Switch is functional and accessible without the Homelab Server despite tight integration. It acts as the primary aggregation center for all other Internal-LAN hardware nodes before the server comes online.

6-7) Access Switch (Catalyst 3750)

	The Access Switch is functional and accessible without the Homelab Server despite tight integration. It directly connects all other Internal-LAN hardware nodes, eventually passing control to the C/D Switch when the Homelab Server comes online.

## Phase 3 - Physical Firewalls and Wireless

The hardware firewalls are functionally non-critical, but aim to serve the WLC and subsequent APs and thus contest positions 8-9. The same can be said of AP1 and AP2, but both require the WLC booting first.

8-9) Firepower Firewall

	The newer of the two hardware firewalls, both are considered parallel infrastructure for labbing practice while the Router/Firewall VM handles the real work.

8-9) Adaptive Security Appliance (ASA) Firewall

	The older of the two hardware firewalls, both are considered parallel infrastructure for labbing practice while the Router/Firewall VM handles the real work.

10) Wireless LAN Controller (WLC)

	In theory, the WLC is integrated into the labbing with the two hardware firewalls and should come after their initialization.

11-12) Access Point 1

	Access Point 1 will require the WLC to initialize first to obtain operational parameters before functioning.

11-12) Access Point 2

	Access Point 2 will require the WLC to initialize first to obtain operational parameters before functioning.

## Phase 4 - Homelab Core

13) Server (R640) / Hypervisor (Proxmox)

	The Server (and directly connected Hypervisor) could be argued to boot as early as position 6 as the core of the Internal-LAN. It is required prior to all software nodes and specific functional control of all hardware nodes mentioned in Phases 2 & 3.

## Phase 5 - Disaster Recovery Failsafe

14) External-SSD / Secondary Backups VM (PBS-Secondary)

	This node is entirely self-contained within an externally-attached SSD and initializes quickly after the hypervisor. It comes before all other critical infrastructure in the event of total loss of homelab control / operation as the failsafe for disaster recovery.

## Phase 6 - Critical Virtual Infrastructure

15) Router/Firewall VM (OPNsense)

	Necessary first for Internal-LAN Layer 3 routing and firewalling, effectively every Internal-LAN node relies on this.

16) Storage VM (TrueNAS)

	The primary storage infrastructure nearly all Internal-LAN nodes (except critical infrastructure), it is the next most important thing to initialize after baseline inter-node communication control is established.

17) Primary Backups VM (PBS-Primary)

	The primary node for Internal-LAN node data safekeeping, it is used for general recovery utilizing dedicated storage infrastructure previously constructed and copies data snapshots to the Secondary Backups VM in case of total failure.

## Phase 7 - Infrastructure Services

18) Monitoring VM

	Of the non-critical infrastructure, the Monitoring VM comes first to capture as many metrics as possible to diagnose and remediate any internal issues.

19) Automation VM

	The Automation VM is the next most-important infrastructure services stack, largely accounted to the primary responsibility of infrastructure configuration management, but also becoming of file tracking and version control.

20) Security VM

	The last of the infrastructure services stack to boot, mostly due to the nature of detecting threats requiring pattern analysis over time.

## Phase 8 - Other Services

21) Wiki VM

	The greatest importance beyond internal infrastructure as the repository of all my learnings throughout my IT studying.

22) Windows Server VM

	The primary lab for exploring critical Windows topics such as Active Directory, and required for simulated hosts to test implementation of those topics.

23) Other Labs VMs

	For now, the scope of these VMs are primarily subjects for the Windows Server VM as previously mentioned.

24) Other DMZ VMs

	For now, this exclusively includes my Gaming Server VM which is currently unused.
