# IP Address Plan

## Address Space

Primary range:

10.10.0.0/17


## Addressing Philosophy

The third octet corresponds to the functional network segment.

Examples:

10.10.X.0/24


## Current Assignments

| Device | VMID | IP | Purpose |
|--------|------|----|---------|
| OPNsense | 100 | TBD | Firewall |
| TrueNAS | 101 | TBD | Storage |
| PBS-Primary | 102 | TBD | Backup |
| PBS-Secondary | 103 | TBD | Backup Replica |
| Automation | 110 | 10.10.254.110 | Git/Ansible |
| Monitoring | 111 | TBD | Monitoring |
| Security | 112 | TBD | Security |
