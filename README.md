# Homelab Infrastructure

Infrastructure-as-code and documentation repository for my homelab environment.

## Overview

This repository contains:

- Infrastructure documentation
- Network configurations
- Proxmox VM configuration
- Proxmox Backup Server configuration
- Storage documentation
- Automation tooling

The goal of this repository is to maintain a reproducible, documented, and recoverable homelab environment.

---

# Infrastructure

## Hypervisor

### Proxmox VE

Primary virtualization host:

- Hardware:
- CPU:
- Memory:
- Storage:

Responsibilities:

- Virtual machine hosting
- Container hosting
- Network virtualization
- Backup integration

---

# Virtual Machines

| VMID | Name | Purpose |
|------|------|---------|
| 100 | OPNsense | Firewall/router |
| 101 | Windows Server | Windows services |
| 102 | PBS-Primary | Primary backup server |
| 103 | PBS-Secondary | Secondary backup replica |
| 110 | Automation | Git/Ansible management |
| 111 | Monitoring | Monitoring stack |
| 112 | Security | Security tooling |
| 150 | Wiki.js | Documentation platform |
| 200 | TrueNAS | Storage services |
| 300 | Minecraft | DMZ game server |

---

# Storage

## Proxmox Storage

| Storage | Purpose |
|---------|---------|
| vm-fast-nfs | VM operating disks |
| vm-backup-nfs | PBS datastore |
| vm-bulk-nfs | Bulk storage |

## Backup

Primary:

PBS-Primary

Secondary:

PBS-Secondary

Replication:

PBS sync jobs

---

# Networking

Current network: 10.10.0.0/17

VLAN design:

(To be finalized)

---

# Automation

Planned automation:

- Ansible
- Terraform
- Proxmox API automation
- Network device configuration backup
- Configuration validation

---

# Disaster Recovery

Recovery objectives:

- Restore Proxmox host
- Restore PBS
- Restore network configuration
- Restore virtual machines

---

# Repository Structure

ansible/
network/
opnsense/
pbs/
proxmox/
scripts/
terraform/
truenas/
documentation/
