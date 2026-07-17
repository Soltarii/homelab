# Virtual Machine Inventory

The following documentation is intended to be an authoritative source of all virtual machines excluding IP assignments (see addressing.md).

## Virtual Machine Matrix

| VMID | Name | OS | Role | Network Zone |
|------|------|----|------|--------------|
| 100 | OPNsense | FreeBSD | Router/Firewall | Management |
| 110 | Automation | Ubuntu | Git/Ansible | Infrastructure |
| 111 | Monitoring | Ubuntu | Monitoring | Infrastructure |
| 112 | Security | Ubuntu | Security tooling | Infrastructure |
| 120 | TrueNAS | TrueNAS SCALE | Storage | Storage |
| 130 | PBS-Primary | Debian | Backup server | Infrastructure |
| 131 | PBS-Secondary | Debian | Backup replica | Infrastructure |
| 200 | Windows-Server-2025 | Windows Server 2025 | Windows services | Internal |
| 300 | Wiki.js | Ubuntu | Documentation | DMZ |
| 301 | Minecraft | Ubuntu | Game server | DMZ |

## Virtual Machine Breakdown

### OPNsense

Purpose:
Security Zone: Management

#### General Configuration

Node: r640
VMID: 100
Resource Pool: None
High-Availability?: No
Start at Boot?: Yes
Start/Shutdown Order: 1
Startup Delay: 30
Shutdown Timeout: None
Boot Order: virtio0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: No
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=1340c7e0-9431-4660-9bb7-94df2f51a113
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: vm-bulk-nfs
ISO Image: OPNsense-26.1.2-dvd-amd64.iso

Guest OS: Other
Version: n/a

EFI Disk: vm-100-disk-0.qcow2
- Storage: Local
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: No
TPM?: No

#### Disk Configuration

Disk: virtio0
- Image:vm-100-disk-1.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: Local
- Disk Size: 40GiB
- Format: QEMU (qcow2)
- Cache: Writeback
- Discard?: Yes
- IO Thread?: No
- SSD Emulation?: No
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 1
Cores: 6
Type: Host
Total Cores: 6

VCPUs: 6
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags:
- AES: On

#### Memory Configuration

Memory: 8192MiB (8GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0
- Bridge: vmbr0
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:DD:76:7E
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 2

Network Device: net1
- Bridge: vmbr1
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:0B:78:6A
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 2

Network Device: net2 (To be deprecated)
- Bridge: vmbr2
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:FC:CE:BD
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 2

Network Device: net3 (To be deprecated)
- Bridge: vmbr3
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:4E:1E:41
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 2

### Automation

Purpose:
Security Zone: Infrastructure

#### General Configuration

Node: r640
VMID: 110
Resource Pool: None
High-Availability?: No
Start at Boot?: Yes
Start/Shutdown Order: 5
Startup Delay: 30
Shutdown Timeout: None
Boot Order: scsi0, net0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=a47db050-6c39-4e56-a20d-96cddec5edfe
Protection?: Yes
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: vm-bulk-nfs
ISO Image: ubuntu-26.04-live-server-amd64.iso

Guest OS: Linux
Version: 7.x - 2.6 Kernel

EFI Disk: vm-110-disk-1.qcow2
- Storage: Local
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: Yes
TPM?: No

#### Disk Configuration

Disk: scsi0
- Image: vm-110-disk-0.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: vm-fast-nfs
- Disk Size: 40GiB
- Format: QEMU (qcow2)
- Cache: Default (none)
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 1
Cores: 2
Type: Host
Total Cores: 2

VCPUs: 2
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 4096MiB (4GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0
- Bridge: vmbr1
- VLAN Tag: None
- Firewall?: Yes
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:7B:54:2F
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 2

### Monitoring

Purpose:
Security Zone: Infrastructure

### Security

Purpose:
Security Zone: Infrastructure

### TrueNAS

Purpose:
Security Zone: Storage

#### General Configuration

Node: r640
VMID: 120
Resource Pool: None
High-Availability?: No
Start at Boot?: Yes
Start/Shutdown Order: 2
Startup Delay: 120
Shutdown Timeout: None
Boot Order: scsi0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=3128bb05-b8cf-47fd-bc44-df6f6e119e2e
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: Removed
ISO Image: Removed

Guest OS: Linux
Version: 7.x - 2.6 Kernel

EFI Disk: vm-120-disk-0.qcow2
- Storage: Local
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI
QEMU Agent?: No
TPM?: No

PCI Device: hostpci0
- Device: 0000:1a:00.0
- All Functions?: Yes
- Primary GPU?: No
- ROM-BAR?: Yes
- PCI-Express?: Yes
- Vendor ID: From Device
- Device ID: From Device
- Sub-Vendor ID: From Device
- Sub-Device ID: From Device


#### Disk Configuration

Disk: scsi0
- Image: vm-120-disk-1.qcow2
- SCSI Controller: VirtIO SCSI
- Storage: vm-fast-nfs
- Disk Size: 40GiB
- Format: QEMU (qcow2)
- Cache: Writeback
- Discard?: Yes
- IO Thread?: No
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: io_uring

#### CPU Configuration

Sockets: 1
Cores: 16
Type: Host
Total Cores: 16

VCPUs: 16
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 32768MiB (32GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0 (To be deprecated)
- Bridge: vmbr0
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:A3:AE:DC
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 8

Network Device: net1
- Bridge: vmbr1
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:DB:86:8C
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 8

### PBS-Primary

Purpose:
Security Zone: Backups

#### General Configuration

Node: r640
VMID: 130
Resource Pool: None
High-Availability?: No
Start at Boot?: Yes
Start/Shutdown Order: 3
Startup Delay: None
Shutdown Timeout: None
Boot Order: scsi0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=db42b037-a9dc-41c4-812a-4bfc1077f908
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: Removed
ISO Image: Removed

Guest OS: Linux
Version: 7.x - 2.6 Kernel

EFI Disk: vm-130-disk-0.qcow2
- Storage: vm-bulk-nfs
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: Yes
TPM?: No

#### Disk Configuration

Disk: scsi0
- Image: vm-130-disk-0.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: vm-fast-nfs
- Disk Size: 100GiB
- Format: QEMU (qcow2)
- Cache: None
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

Disk: scsi1
- Image: vm-130-disk-0.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: vm-backup-nfs
- Disk Size: 2TiB
- Format: QEMU (qcow2)
- Cache: None
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: No
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 1
Cores: 4
Type: Host
Total Cores: 4

VCPUs: 4
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 8192MiB (8GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0
- Bridge: vmbr1
- VLAN Tag: None
- Firewall?: Yes
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:F2:B3:8D
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 4

### PBS-Secondary

Purpose:
Security Zone: Backups

#### General Configuration

Node: r640
VMID: 131
Resource Pool: None
High-Availability?: No
Start at Boot?: Yes
Start/Shutdown Order: 4
Startup Delay: 90
Shutdown Timeout: None
Boot Order: scsi0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=0823c372-d1fd-4e5b-a86e-8220c29a914c
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: Removed
ISO Image: Removed

Guest OS: Linux
Version: 7.x - 2.6 Kernel

EFI Disk: vm-131-disk-0.qcow2
- Storage: external-T7
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: Yes
TPM?: No

#### Disk Configuration

Disk: scsi0
- Image: vm-131-disk-1.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: external-T7
- Disk Size: 100GiB
- Format: QEMU (qcow2)
- Cache: None
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

Disk: scsi1
- Image: vm-130-disk-2.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: external-T7
- Disk Size: 2TiB
- Format: QEMU (qcow2)
- Cache: None
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: No
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 1
Cores: 4
Type: Host
Total Cores: 4

VCPUs: 4
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 8192MiB (8GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0
- Bridge: vmbr1
- VLAN Tag: None
- Firewall?: Yes
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:6A:FC:F7
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 4

### Windows-Server-2025

Purpose:
Security Zone: Labs

#### General Configuration

Node: r640
VMID: 200
Resource Pool: None
High-Availability?: No
Start at Boot?: No
Start/Shutdown Order: Any
Startup Delay: None
Shutdown Timeout: None
Boot Order: scsi0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=12648ca7-483b-49da-a0cb-33dd5ae2ce00
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: vm-bulk-nfs
ISO Image: 26100.32230.260111-0550.lt\_release_svc\_refresh_SERVER\_EVAL_x64FRE_en-us.iso

Guest OS: Microsoft Windows
Version: 11/2022/2025

EFI Disk: vm-200-disk-0.qcow2
- Storage: vm-fast-nfs
- EFI Type: 4m
- Size: 528K
- MS-Cert: 2023w
- Pre-Enrolled Keys?: Yes

#### System Configuration

Graphics Card: VirtIO-GPU (virtio)
Machine: pc-q35-10.1
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: Yes
TPM?: Yes
- TPM State: vm-200-disk-0.qcow2
- Storage: vm-bulk-nfs
- Size: 4M
- Version: v2.0

USB Device: usb0
- Host: 067b:2303

#### Disk Configuration

Disk: scsi0
- Image: vm-200-disk-1.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: vm-fast-nfs
- Disk Size: 80GiB
- Format: QEMU (qcow2)
- Cache: Writeback
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 2
Cores: 4
Type: Host
Total Cores: 8

VCPUs: 8
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 8192MiB (8GiB)

Minimum Memory?: 8192MiB (8GiB)
Ballooning?: Yes
Allow KSM?: Yes

#### Network Configuration

Network Device: net0 (To be converted)
- Bridge: vmbr2
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:77:C2:89
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 4

### Wiki.js

Purpose:
Security Zone: DMZ

#### General Configuration

Node: r640
VMID: 300
Resource Pool: None
High-Availability?: No
Start at Boot?: No
Start/Shutdown Order: Any
Startup Delay: None
Shutdown Timeout: None
Boot Order: scsi0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=2e49a840-7b7f-4bbb-8b57-47833b1d67e7
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: vm-bulk-nfs
ISO Image: ubuntu-26.04-live-server-amd64.iso

Guest OS: Linux
Version: 7.x - 2.6 Kernel

EFI Disk: vm-300-disk-0.qcow2
- Storage: vm-bulk-nfs
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: Yes
TPM?: No

#### Disk Configuration

Disk: scsi0
- Image: vm-300-disk-0.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: vm-fast-nfs
- Disk Size: 40GiB
- Format: QEMU (qcow2)
- Cache: Writeback
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: No
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 1
Cores: 2
Type: Host
Total Cores: 2

VCPUs: 2
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 4096MiB (4GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0
- Bridge: vmbr1
- VLAN Tag: None
- Firewall?: Yes
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:39:2A:08
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 2

### Minecraft

Purpose:
Security Zone: DMZ

#### General Configuration

Node: r640
VMID: 301
Resource Pool: None
High-Availability?: No
Start at Boot?: No
Start/Shutdown Order: Any
Startup Delay: No
Shutdown Timeout: None
Boot Order: scsi0
vCPU Architecture: Host
Tags:

Tablet for Pointer?: Yes
Hotplugs: Disk, Network, USB
ACPI Support?: Yes
KVM Hardware Virtualization?: Yes
Freeze CPU at Startup?: No
Use Local Time for RTC: Default (Enabled for Windows)
RTC Start Date: Now
SMBIOS Settings (Type 1): UUID=9cc89830-70b3-4a7c-b4c3-d22f39f0ad6a
Protection?: No
Spice Enhancements: None
VM State Storage: Automatic
AMD SEV: Default (Disabled)
Intel TDX: Default (Disabled)

#### OS Configuration

Type: ISO
Storage: vm-bulk-nfs
ISO Image: ubuntu-26.04-live-server-amd64.iso

Guest OS: Linux
Version: 7.x - 2.6 Kernel

EFI Disk: vm-301-disk-0.qcow2
- Storage: vm-fast-nfs
- EFI Type: 4m
- Size: 528K

#### System Configuration

Graphics Card: SPICE (qxl)
Machine: q35
BIOS: OVMF (UEFI)
SCSI Controller: VirtIO SCSI Single
QEMU Agent?: Yes
TPM?: No

#### Disk Configuration

Disk: scsi0
- Image: vm-301-disk-1.qcow2
- SCSI Controller: VirtIO SCSI Single
- Storage: vm-fast-nfs
- Disk Size: 100GiB
- Format: QEMU (qcow2)
- Cache: Writeback
- Discard?: Yes
- IO Thread?: Yes
- SSD Emulation?: Yes
- Backup Target?: Yes
- Read-Only?: No
- Skip Replication?: Yes
- Async IO: Default (io_uring)

#### CPU Configuration

Sockets: 1
Cores: 8
Type: Host
Total Cores: 8

VCPUs: 8
CPU Limit: Unlimited
CPU Affinity: All Cores
CPU Units: 100
Enable NUMA?: No

Extra CPU Flags: No

#### Memory Configuration

Memory: 16384MiB (16GiB)

Minimum Memory?: n/a
Ballooning?: No
Allow KSM?: No

#### Network Configuration

Network Device: net0 (To be converted)
- Bridge: vmbr3
- VLAN Tag: None
- Firewall?: No
- Model: VirtIO (paravirtualized)
- MAC Address: BC:24:11:EB:4B:4E
- Disconnect?: No
- MTU: Same as Bridge
- Rate Limit (MB/s): Unlimited
- Multiqueue: 8
