# VLAN Plan

## Design Goals

The network is segmented using VLANs rather than separate physical networks.

Primary objectives:

- Security isolation
- Clear traffic boundaries
- Simplified firewall policy
- Scalable infrastructure growth

---

# VLAN Inventory

| VLAN ID | Name | Purpose | Security Zone |
|---------|------|---------|---------------|
| TBD | Management | Network/device administration | Management |
| TBD | Infrastructure | Proxmox, PBS, automation | Internal |
| TBD | Servers | Internal applications | Internal |
| TBD | Storage | TrueNAS/NFS/iSCSI | Restricted |
| TBD | DMZ | Public-facing services | Untrusted |
| TBD | Clients | User devices | Internal |
| TBD | IoT | Smart devices | Restricted |
