# Homelab Infrastructure

A documentation, configuration, and infrastructure-as-code provisioning repository for my self-hosted homelab environment.

This repository is intended to document not only what is deployed, but where services reside, how individual systems depend upon one another and can be recovered, and why the infrastructure is designed as such. This is my journey to cultivate habits of life-long learning through research and hands-on experience for personal growth and professional development. Throughout my documentation, I use several terms to refer to segments of infrastructure:

- External-LAN: My residential network that connects the standard consumer appliances of myself and family members.
- Internal-LAN: My separated network that connects the enterprise equipment that hosts nearly all of my homelab services, only connecting to my External-LAN for Internet connectivity.

## Overview

My homelab is built around a Dell PowerEdge R640 running Proxmox VE as the virtualization platform. Network infrastructure is mixed between virtualized services such as OPNsense and TrueNAS, as well as physical appliances such as Cisco switches and access points for well-rounded experience.

This primary goals of the environment are:

- Self-hosted infrastructure and services
- Enterprise-oriented networking and virtualization practice
- Centralized storage with redundancy 
- Network segmentation and security with mind for scalability
- Reproducible configuration and documentation
- Disaster recovery and operational resilience

## Critical Infrastructure Overview

### Compute

**Dell PowerEdge R640**

- 2x Intel Xeon Gold 5220 processors
- 36 physical cores / 72 threads
- 128GB DDR4 ECC RDIMM memory
- BOSS-S1 mirrored M.2 boot storage
- LSI/Broadcom SAS3008 HBA
- 1 GbE and 10/25 GbE networking controllers
- Proxmox VE bare-metal hypervisor

Proxmox provides the primary virtualization layer for the homelab.

### Virtualization

**Proxmox VE** hosts the primary virtualized infrastructure, including:

- OPNsense
- Ubuntu-hosted Automation services stack
- Ubuntu-hosted Monitoring services stack
- Ubuntu-hosted Security services stack
- TrueNAS
- Windows Server
- Wiki.js knowledge base
- Gaming server
- Other laboratory and application workloads

The virtual machines are segmented into the appropriate network zones according to their function and security requirements.

### Network

**OPNsense** provides routing, firewalling, NAT, DNS, and inter-vlan connectivity.

**Cisco Nexus** provides high-speed switching and the 10/25 GbE connectivity used by the R640.

**Cisco Catalyst** provides 1 GbE access connectivity and legacy network connectivity.

The R640's high-speed interfaces are configured as an LACP bond and connect to the Nexus as a port-channel.

### Network Architecture

The Internal-LAN uses VLAN-based segmentation to separate different trust and operational zones.

| VLAN | Network | Purpose |
|------|---------|---------|
| 10 | 10.10.10.0/24 | Management |
| 11 | 10.10.11.0/24 | Infrastructure |
| 12 | 10.10.12.0/24 | Storage |
| 13 | 10.10.13.0/24 | Backups |
| 20 | 10.10.20.0/24 | Labs |
| 30 | 10.10.30.0/24 | DMZ |
| 999 | - | Native / Untagged infrastructure VLAN |

OPNsense provides the default gateway for the internal VLANs.

The network design, addressing plan, firewall zones, and ACLs are documented separately.

### Storage

**TrueNAS** runs as a virtual machine under Proxmox with direct PCI passthrough of the SAS3008 HBA.

Storage is provided using ZFS aggregated into functional pools exposed to Proxmox through NFS.

Storage is separated from management and other network traffic using dedicated VLANs.

### Storage Architecture

TrueNAS provides ZFS storage to Proxmox through the dedicated Storage VLAN.

The storage architecture uses mirrored ZFS vdevs with:

- ashift=12
- LZ4 compression
- atime=off
- Workload-appropriate record sizes

Proxmox uses NFS-backed storage for virtual machine workloads.

Boot storage (Local) for Proxmox is independent of the TrueNAS storage infrastructure.

## Repository Structure

homelab/
|
|- ansible/
|
|- documentation/
|  |- architecture/
|  |  |- boot-order.md
|  |  |- firewall-zones.md
|  |  |- network.md
|  |  |- network-topology.svg
|  |  |- network-topology.xml
|  |  |- nodes.md
|  |  |- recovery.md
|  |  |- topology.md
|  |
|  |- inventory/
|  |  |- addressing.md
|  |  |- connection-topology.svg
|  |  |- connection-Topology.xml
|  |  |- firewall-acl.md
|  |  |- hardware.md
|  |  |- virtual-machines.md
|  |
|  |- operations/
|  |  |- boot-order.md
|  |  |- recovery.md
|  |
|  |- projects/
|
|- network/
|
|- opnsense/
|
|- pbs/
|
|- proxmox/
|
|- scripts/
|
|- terraform/
|
|- truenas/
|
|- .gitignore
|- LICENSE
|- README.md

## Repository Structure Overview

### Ansible

> homelab/ansible/

How do I automatically configure these systems?

**FOR**: Automation/configuration management.

### Documentation

> homelab/documentation/

Documentation is divided into four areas: Architecture, Inventory, Operations, and Projects.

#### Architecture

> homelab/documentation/architecture/

How is the infrastructure generally designed, and why is it designed that way?

**FOR**: Explaining the design and operational architecture of the environment.

- firewall-zones.md - Why are the networks segmented into these security zones, and what does each zone represent?
- network.md - How does the network work? 
- network-topology.svg / .xml - How is the overall network physically/logically arranged?
- nodes.md - What are the infrastructure nodes and what role does each play?
- topology.md - How do the major infrastructure components relate to one another?

#### Inventory

> homelab/documentation/inventory/

What exactly exists, where is it, and what are its identifiers/specifications?

**FOR**: Containing the detailed infrastructure inventory and configuration references.

- connection-topology.svg / .xml - Where specifically does each node connect to?
- addressing.md - Where does each node live and what are their specific identifiers/specifications?
- firewall-acl.md - What traffic is actually permitted or denied?
- hardware.md - What physical hardware do I have?
- virtual-machines.md - What virtual machines exist and what are they for?

#### Operations

> homelab/documentation/operations/

How do I operate, recover, and restart the infrastructure?

**FOR**: Containing the documentation of how to perform various procedures for deploying and managing my infrastructures/services.

- boot-order.md - The entire homelab is powered off, in what order should I start things and why?
- recovery.md - The infrastructure is broken, how do I get it back?

#### Projects

> homelab/documentation/projects/

What am I currently building, changing, experimenting with, or planning?

**FOR**: Containing temporary/project-oriented documentation.

### Network

> homelab/network/

What are the actual network-device configurations?

**FOR**: Network-device configuration or configuration artifacts that aren't specifically OPNsense/Proxmox.

### OPNsense

> homelab/opnsense/

What files allow me to configure/manage OPNsense?

**FOR**: OPNsense-specific configuration/automation artifacts.

### PBS

> homelab/pbs/

What belongs to the PBS implementations?

**FOR**: Proxmox Backup Server configuration/automation.

### Proxmox

> homelab/proxmox/

What files configure/manage the Proxmox host?

**FOR**: Proxmox-specific configuration, automation, or scripts.

### Scripts

> homelab/scripts/

What miscellaneous tooling have I written to operate this environment?

**FOR**: Standalone utility scripts.

### Terraform

> homelab/terraform/

What infrastructure can I declaratively provision/manage?

**FOR**: Infrastructure-as-code.

### TrueNAS

> homelab/truenas/

What belongs to the TrueNAS implementation?

**FOR**: TrueNAS-specific configuration/automation artifacts.

## Current State

The core infrastructure and network migration are operational.

Current completed work includes:

- Proxmox virtualization environment
- OPNsense firewall and inter-VLAN routing
- VLAN-based network segmentation
- TrueNAS ZFS storage
- NFS storage integration with Proxmox
- Cisco Catalyst infrastructure
- Cisco Nexus high-speed switching
- LACP connectivity between the R640 and Nexus
- Dedicated storage and backup network segments
- VM network migration to the finalized addressing scheme
- Infrastructure and recovery documentation

## Roadmap

- Monitoring services stack VM
- Security services stack VM
- Active Directory experimentation
- Wiki.js LDAP integration with AD domain
- Automation with Ansible & Terraform for AD host deployment
- Further network and security hardening with TLS, certificates, SSH, MFA, zero-trust, AAA, RADIUS/TACACS+
- Lab dynamic routing protocols using virtualized hosts
- Continue reviewing, documenting, and transferring off-platform notes (Anki flashcards, Google Docs, etc.)
