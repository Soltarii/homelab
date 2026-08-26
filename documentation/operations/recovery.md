# Recovery

This following documentation is intended to detail the disaster recovery process for my Internal-LAN, broken into sections for General VM, Proxmox, OPNsense, TrueNAS, Catalyst Switch, and Nexus Switch. Take care to ALWAYS document before, during, and after an incident to help identify causes, return to a baseline within troubleshooting, and help remediate future occurences.

TO-DO:
- Decide whether to finish Phase descriptions or scrap them entirely
- Finish General VM Recovery section

## General VM

In the event of operational failure within or to a general VM, proceed as follows:



## Proxmox Recovery

In the event of operational failure within or to Proxmox, proceed as follows:

**Phase 1 - Attempt Local Console Recovery**
Determine whether the Proxmox host is running and recoverable without using remote management.

1.1) First attempt to access the Proxmox device CLI by plugging in the backup mouse, keyboard, and monitor into the Dell R640. The front face of the R640 includes 1 USB-A slot, and 1 VGA slot (beneath the faceplate) for easy access. If this fails, proceed to Phase 2.
1.2) Login to the root account when prompted in the Proxmox CLI.
1.3) Verify host health by via command "uptime", checking how long the host has been running and if the load average appears reasonable.
1.4) Check the health of the BOSS RAID boot pool via "zpool status" for degradation or corruption.
1.5) Check the mounted filesystems via "df -h", confirming if root (/) exists, storage mountpoints as present, and if enough free space is available.
1.6) Check network configuration via "ip addr", confirming if virtual bridges (vmbr0 & vmbr1) and critical VLAN interfaces (vmbr1.10, vmbr1.12, vmbr1.13) exist and have expected IP addresses.
1.7) Check routing via "ip route", confirming the correct default gateway and VLAN routes.
1.8) Check bridge status via "bridge link" and "bridge vlan show", confirming state and configured VLAN tag(s).
1.9) Check service statuses via "systemctl status pveproxy", "systemctl status pvedaemon", and "systemctl status pveproxy", checking state and log history. If needed, restart via "systemctl restart pveproxy" or "systemctl restart pvedaemon".
1.10) Check VM statuses via "qm list", confirm expected VMs exist and their expected state (running or stopped).
1.11) Check storage via "pvesm status", confirming existence, state, and capacity usage of Local, NFS pools, and PBS datastores.
1.12) Test internal connectivity between expected allowed interfaces via ping, such as "ping 10.10.10.254", "ping 10.10.12.10", and "ping 192.168.254.254".

**Phase 2 - Recover using iDRAC**
Attempt to regain access if no video output or local console are available.

2.1) Proceed to attempt to access the R640 local console / UEFI via the iDRAC port on the rear face of the R640 using a known-good Ethernet cable with RJ-45 connector, connecting to a trusted admin workstation.
2.2) Access the iDRAC console via navigating to the iDRAC IP in a web browser (https://), or SSH in a terminal (ssh root@).
2.3) In iDRAC, verify system health, confirming no critical hardware alerts, fans healthy, PSU healthy, CPU healthy, and memory healthy.
2.4) In iDRAC, verify storage, confirming the BOSS RAID is present, drives are detected, and there are no egraded virtual disks.
2.5) In iDRAC, review logs (Lifecycle Log & System Event Log), looking for memory errors, RAID errors, PSU failures, thermal shutdowns, or otherwise anything that sticks out.
2.6) In iDRAC, launch the virtual / HTML5 console, verifying that POST completes, BIOS completes, GRUB appears, and Proxmox boots.
2.7) If the host is hung, perform a power cycle via Graceful Shutdown (preferred) or Forced Reboot (if necessary).

**Phase 3 - BIOS / UEFI Verification**
Checking pre-OS environment only if the machine will not boot.

3.1) Within iDRAC or direct peripheral connection, press F2 -> System Setup, verifying the boot mode is UEFI.
3.2) Within UEFI, check the boot order confirming that "BOSS-S1" appears first.
3.3) Within UEFI, check the RAID configuration, verifying that the BOSS RAID appears and is healthy.
3.4) Within UEFI, check the PCIe configuration, confirming that the LSI HBA and Broadcom NICs are detected.
3.5) Within UEFI, check the memory configuration, verifying all 128GBs are detected and there are no failed DIMMs.
3.6) Within UEFI, check CPU configuration, confirming both Xeon Gold 5520 CPUs are detected.

**Phase 4 - Emergency Recovery**
If Proxmox still will not boot.

4.1) Attempt to boot the previously working kernel from GRUB.
4.2) Attempt to boot Proxmox Installer in Rescue Mode.
4.3) Import pools manually.
4.4) Restore configurations from backups.
4.5) Restore VMs from PBS.

## OPNsense Recovery

In the event of operational failure within or to OPNsense, proceed as follows:

**Phase 0 - Check Previous Issues**

OPNsense has been encountered to have previous issues in the past that were eventually resolved, including Tailscale, VLAN migration, accessing the Management interface, DNS, and NAT. Check to see if any of these previously documented issues match the scenario for a more specific recovery breakdown. 

**Phase 1 - Determine VM State**
Verify that the OPNsense VM is running and reachable.

1.1) From Proxmox shell, do "qm list" to confirm VM 100 (OPNsense) is running, not locked, and using expected resources.
1.2) Open the OPNsense console either via web UI or "qm terminal 100".
1.3) Verify the interfaces within OPNsense that they all exist, are active/up, have the correct VLAN IDs, and have the expected IP address.

**Phase 2 - Verify Routing**
Verify connectivity to external services.

2.1) Check the OPNsense routing table via "netstat -rn", confirming the default route (0.0.0.0/0 -> 192.168.254.254) and that all internal VLANs appear as directly connected.
2.2) Verify WAN connectivity by pinging the WAN gateway, a stable public IP address, and a domain name: "ping 192.168.254.254", "ping 1.1.1.1", "ping google.com". If the IP pings work but the domain name ping doesn't, that points to a DNS problem. If neither works, that points to a WAN routing problem.

**Phase 3 - Verify DNS**
Verify that domain names are being resolved properly.

3.1) Check that the DNS resolver is working using the drill or host commands, such as via "drill google.com" or "host google.com" which should successfully resolve to an IP address.
3.2) Check the Unbound DNS service via "service unbound status", and restart using "service unbound restart" if necessary.

**Phase 4 - Verify Firewalls**
Verify all firewalls are present and functioning properly.

4.1) Display all firewall rules via "pfctl -sr", confirming all interface rules are loaded and there are no unexpected block rules causing issues.
4.2) Display all connectivity states via "pfctl -ss", checking for any hanging TCP connections or state exhaustion.
4.3) Temporarily test disabling all firewalls via "pfctl -d", looking for changes in connectivity that could point to a firewall issue. Remember to re-enable afterwards with "pfctl -e".

**Phase 5 - Verify NAT**
Verify all NAT configurations are present and functioning properly.

5.1) Display all NAT rules via "pfctl -sn" and detailed NAT statistics with "pfctl -vvs nat", confirming all NAT rules exist, port forwards are present, and NAT Reflection is configured if required.

**Phase 6 - Verify DHCP**
Verify if improper dynamic IP assignment is the issue.

6.1) If VM or service using dynamic assignment is having issues, check to make sure DHCP is running via "service isc-dhcpd status" and restart via "service isc-dhcpd restart" if necessary.

**Phase 7 - Verify VPN**
Verify if Tailscale is causing the issue.

7.1) Confirm Tailscale is using the correct routes for advertised internal networks, if the R640 or admin workstation is configured as an exit node, or if any ACL entries may be messing up Tailscale operation by checking the admin panel at "console.tailscale.com/admin/machines". This is unlikely to be an issue since IP addressing schema has been finalized and implemented, but possible.

**Phase 8 - Verify Web GUI**

8.1) Confirm if Lighttpd (the socket that hosts and listens for connections to the web GUI) is running via "sockstat -4 -l | grep 443", expecting to see "*.443", and restart via "service lighttpd restart" if necessary.

**Phase 9 - Connectivity Testing**
Seeing what works and what doesn't.

9.1) Ping every gateway, confirming operation via "ping 10.10.10.254", "ping 10.10.11.254", "ping 10.10.12.254", "ping 10.10.13.254", "ping 10.10.20.254", "ping 10.10.30.254". Any failures should point to a probable misconfiguration within OPNsense.
9.2) Ping core subnet VMs, testing critical inter-VLAN routing such as "ping 10.10.12.10", "ping 10.10.11.10", or "ping 10.10.30.10".

**Phase 10 - Configuration Restore**
If everything fails and OPNsense is unrecoverable.

10.1) If configuration is unrecoverable, restore from PBS backups via navigating to Proxmox -> OPNsense -> Backup and choose the last known good backup to restore.
10.2) If for whatever reason PBS backups aren't available, try the latest Proxmox snapshot of VM 100.

## TrueNAS Recovery

In the event of operational failure within or to TrueNAS, proceed as follows:

**Phase 0 - Check Previous Issues**

TrueNAS has been encountered to have previous issues in the past that were eventually resolved, including misconfigured NFS, ACL errors, missing mountpoints, physically-dead drives, and incorrect PCI passthrough. Check to see if any of these previously documented issues match the scenario for a more specific recovery breakdown.

**Phase 1 - Determine VM State**
Verify if TrueNAS is running and reachable.

1.1) Confirm VM status from Proxmox shell via "qm list", checking if VM 120 (TrueNAS) is running, not locked, and using expected resources.
1.2) Open the TrueNAS console either via web GUI or "qm terminal 120".

**Phase 2 - Verify Network**

2.1) Within TrueNAS, check interfaces with "ip addr" verifying existence, state, and IP assignment of the Management interface (ens18) and Storage interface (ens19).
2.2) Verify routing by checking "ip route", confirming the default gateway as 10.10.10.254.
2.3) Verify connectivity by pinging both Management and Storage interfaces, as well as the relevant Proxmox interfaces via "ping 10.10.10.254", "ping 10.10.12.254", "ping 10.10.10.253", and "ping 10.10.12.253".

**Phase 3 - Verify Storage Pools**

3.1) Confirm expected storage pools to exist with "zpool list".
3.2) List all datasets via "zfs list", confirming mountpoints exist and datasets are mounted properly.
3.3) Display the pool statuses via "zpool status", verifying all pools are online, and that there are no degradations or faults.
3.4) Check the available space within the storage pools via "zfs list -o name,used,avail", verifying nothing has overloaded capacity.

**Phase 4 - Verify NFS**

4.1) Confirm NFS is active and correctly pointing to Proxmox via "systemctl status nfs-server".
4.2) Confirm NFS exports via "showmount -e localhost", with the expected exports of /mnt/vm-fast, /mnt/vm-bulk, and /mnt/vm-backup.
4.3) From Proxmox, verify access to the NFS exports via "showmount -e 10.10.12.10".
4.4) From Proxmox, verify mounted shares via "pvesm status", with all NFS stores be marked as Active.

**Phase 5 - Verify HBA Passthrough**
TrueNAS requires HBA Passthrough for control of the physical drive bays.

5.1) From TrueNAS console, verify the detected PCI devices with "lspci", SAS3008 should be detected.
5.2) Verify access to the physical drives via "lsblk" or "camcontrol devlist", confirm all HGST SSDs and Intel SSDs are present.

**Phase 6 - Verify Web GUI**
Can I access the TrueNAS portal?

6.1) Confirm middleware (the service exposing the web GUI) is running via "systemctl status middlewared", and restart via "systemctl restart middlewared" if necessary.
6.2) Verify the HTTPS session status via "ss -ltn", confirming TrueNAS is listening on the port: "*:443".

**Phase 7 - Verify Disk Health**

7.1) Check the SMART report data of the physical disks via TrueNAS GUI or "smartctl -a /dev/sdX" (where X is the proper drive lettering). Check for reallocated sectors, pending sectors, wear level, and media errors.

**Phase 8 - Verify Replication / Backups**
It may be time to restore to a known-good state.

8.1) If PBS datasets exists and are accessable, check to confirm replication tasks were successful, the latest snapshot timestamp, and snapshot retention.
8.2) If storage configuration becomes unrecoverable, restore the latest TrueNAS configuration backup.
8.3) If standard configuration backup is unavailable, import pools manually via "zpool import (pool name)". Repeat for each pool.

## Catalyst Recovery

In the event of operational failure within or to the Catalyst 3750 switch, proceed as follows:

**Phase 1 - Physical Verification**

1.1) Confirm the switch is powered on: System LEDs should be solid green, fan operating, and no orange fault LEDs are present.
1.2) Confirm the switch is plugged into the corresponding Nexus ports and the uplink lights are green.
1.3) Confirm the Nexus switch is plugged into the corresponding slave ports for bond0 and their uplink lights are green.

**Phase 2 - Verify Reachability**

2.1) Attempt to SSH into the Management SVI via "ssh admin@10.10.10.20" from a trusted admin workstation. Due to old technology, the remote host must accept diffie-hellman-group1-sha1 as a key exchange method.
2.2) If unable to SSH in, check SVI reachability by pinging via "ping 10.10.10.20" from within the Internal-LAN.
2.3) If still unreachable, check SVI reachability to the Nexus switch directly up the chain via "ping 10.10.10.30" from within the Internal-LAN. If the Nexus is unreachable, the problem is likely higher up the chain.
2.4) Check routing configuration within OPNsense as well as Proxmox, pinging the Management Gateway and vmbr1 via "ping 10.10.10.254" and "ping 10.10.10.253".

**Phase 3 - Console Access**

3.1) If SSH is unavailable, connect to the Catalyst 3750 directly via console connection. Connect a Cisco-certified console cable into the serial port on the rear face of the switch to a trusted administrative workstation.
3.2) Use a virtual terminal software, such as PuTTY, from the connected workstation using the following settings: 9600 baud, 8 data bits, 1 stop bits, no parity, no flow control, and connected to the correct COM port (see system Device Manager).
3.3) Once connected, sign into the Catalyst 3750 with registered admin credentials.
3.4) If appliance credentials have been lost, the switch will have to proceed with account recovery. If this does not apply, skip to the next phase.
3.5) For account recovery / password bypass ...

**Phase 4 - Verify Interfaces**

4.1) Once inside the CLI, verify interfaces from Privileged Exec mode via "show interfaces status", confirm uplinks are connected, expected ports are UP, and no interfaces are error-disabled.
4.2) If interface statuses are fine, check vlan configurations via "show vlan brief", confirming all VLANs are defined and assigned: 10 - Management, 11 - Infrastructure, 20 - Labs, 30 - DMZ, 999 - Native.
4.3) Check port-channels via "show etherchannel summary", confirming that all member ports are bundled and no interfaces are suspended.
4.4) Check interface trunks are correctly configured via "show interfaces trunk", confirming the Native VLAN is 999, and allowed VLANs are set (10, 11, 20, 30, 999).

**Phase 5 - Verify Protocols**

5.1) Check the MAC address table via "show mac address-table", verifying all expected entries (but especially Proxmox, OPNsense, and TrueNAS) all appear and on expected ports and addresses.
5.2) Check STP via "show spanning-tree", confirming the root bridge reports as expected, and there are no unexpected blocked ports.

**Phase 6 - After Resolution**

Following the correction to any misconfigurations causing issues, remember to document changes and especially to save them to the startup-config to be persistent via "copy run start" from Privileged Exec mode.

## Nexus Recovery

In the event of operational failure within or to the Nexus 3064-X switch, proceed as follows:

**Phase 0 - Check Previous Issues**

The Nexus 3064-X has been encountered to have previous issues in the past that were eventually resolved, including misconfigured management interfaces, link port configuration, port-channel configurations, and physically-incompatible SFP adapters. Check to see if any of these previously documented issues match the scenario for a more specific recovery breakdown.

**Phase 1 - Physical Verification**

1.1) Confirm the switch is powered on: System LEDs should light up solid orange, rear PSU LEDs should be green, fans should be audibly spinning.
1.2) Confirm the switch is plugged into the corresponding R640 NIC ports (Fiber links to nic4 & nic5) and the uplink lights are green.
1.3) Confirm the switch links to the Catalyst 3750 are plugged in and uplink lights are green (if downstream appliance access is the issue).

**Phase 2 - Verify Reachability**

2.1) Attempt to SSH into the Management SVI via "ssh admin@10.10.10.30" from a trusted admin workstation.
2.2) If unable to SSH in, check SVI reachability by pinging via "ping 10.10.10.30" from within the Internal-LAN.
2.3) If still unreachable, check Management VLAN gateway and Proxmox interface reachability via "ping 10.10.10.254" and "ping 10.10.10.253".
2.4) Check routing configuration and firewall rules within OPNsense, verifying there are no rules blocking expected switch access.

**Phase 3 - Console Access**

3.1) If SSH is unavailable, connect to either dedicated Management Port (MGMT0 / MGMT1) on the rear face of the Nexus 3064-X with a known-good Ethernet cable to a trusted admin workstation.
3.2) If Ethernet connection fails, attempt connection to either Management Port or dedicated Serial Port via console connection. Connect a Cisco-certified console cable into any of the aforementioned ports on the rear face of the switch to a trusted admin workstation.
3.3) Use a virtual terminal software, such as PuTTY, from the connected workstatin using the folowing settings (if console connected): 9600 baud, 8 data bits, 1 stop bits, no parity, no flow control, and connected to the correct COM port (see system Device Manager).
3.4) Once connected, sign into the Nexus 3064-X with registered admin credentials.
3.5) If appliance credentials have been lost, the switch will have to proceed with account recovery. If this does not apply, skip to the next phase.
3.6) For account recovery / password bypass ...

**Phase 4 - Verify Interfaces**

4.1) Once inside the CLI, verify interfaces from Privileged Exec mode via "show interface status", confirming all uplinks and downlinks are connected, expected ports are UP, and no interfaces are error-disabled.
4.2) Check port-channels via "show port-channel summary", confirming all port-channels existence, status, and memberships as expected.
4.3) Check LACP via "show lacp neighbor", confirming the R640 and Catalyst 3750 are recognized neighbors with System ID is present.
4.4) Check trunk interfaces via "show interface port-channel1 switchport" and "show interface port-channel2 switchport", confirming the mode is trunk, Native VLAN is 999, and allowed vlans are set (10, 11, 20, 30 999).

**Phase 5 - Verify Protocols**

5.1) Check VLAN database via "show vlan brief", confirming all VLANs exist (10, 11, 20, 30, 999).
5.2) Check MAC address table via "show mac address-table", verifying all expected entries (but especially Proxmox, OPNsense, and TrueNAS) all appear and on expected ports and addresses.
5.3) Check LLDP via "show lldp neighbors", confirming the R640/Proxmox and Catalyst 3750 show up.
5.4) Check switch logs via "show logging last 50" or so, looking for link flaps, STP events, port-channel failures, LACP failures, hardware faults, or anything else that might indicate a cause of issue.

**Phase 6 - After Resolution**

Following the correction to any misconfigurations causing issues, remember to document changes and especially to save them to the startup-config to be persistent via "copy run start" from Privileged Exec mode.
