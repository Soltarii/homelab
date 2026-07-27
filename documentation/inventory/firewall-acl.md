# Firewall ACLs

This following documentation is intended to be an authoritative source of the access-control entries within each access-control list used by my firewalls in my Internal-LAN.

## OPNsense Firewalls

## Outbound NAT

Count: 2

Entry ID: 1
Type: Automatic
Interface: WAN
Source Networks: Backups networks, DMZ networks, Infrastructure networks, Labs networks, Loopback networks, Management networks, Storage networks, 127.0.0.0/8
Source Port: Any
Destination: Any
Destination Port: 500
NAT Address: WAN
NAT Port: Any
Static Port: Yes
Description: Auto created rule for ISAKMP

Entry ID: 2
Type: Automatic
Interface: WAN
Source Networks: Backups networks, DMZ networks, Infrastructure networks, Labs networks, Loopback networks, Management networks, Storage networks, 127.0.0.0/8
Source Port: Any
Destination: Any
Destination Port: Any
NAT Address: WAN
NAT Port: Any
Static Port: No
Description: Auto created rule

### Floating

Count: 12

Entry ID: 1
Type: Automatic
Action: Block
State: Active
Direction: In
Applies: Last Match
Protocols: IPv4+6 Any
Source: Any
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Default deny / state violation rule

Entry ID: 2
Type: Automatic
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv6 IPV6-ICMP
Source: Any
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: IPv6 RFC4890 requirements (ICMP)

Entry ID: 3
Type: Automatic
Action: Allow
State: Active
Direction: Out
Applies: First Match
Protocols: IPv6 IPV6-ICMP
Source: (self)
Source Port: Any
Destination: fe80::/10, ff02::/16
Destination Port: Any
Gateway: Any
Schedule: Any
Description: IPv6 RFC4890 requirements (ICMP)

Entry ID: 4
Type: Automatic
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv6 IPV6-ICMP
Source: fe80::/10
Source Port: Any
Destination: fe80::/10, ff02::/16
Destination Port: Any
Gateway: Any
Schedule: Any
Description: IPv6 RFC4890 requirements (ICMP)

Entry ID: 5
Type: Automatic
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv6 IPV6-ICMP
Source: ff02::/16
Source Port: Any
Destination: fe80::/10
Destination Port: Any
Gateway: Any
Schedule: Any
Description: IPv6 RFC4890 requirements (ICMP)

Entry ID: 6
Type: Automatic
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv6 IPV6-ICMP
Source: ::
Source Port: Any
Destination: ff02::/16
Destination Port: Any
Gateway: Any
Schedule: Any
Description: IPv6 RFC4890 requirements (ICMP)

Entry ID: 7
Type: Automatic
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP/UDP
Source: Any
Source Port: 0
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: block all targeting port 0

Entry ID: 8
Type: Automatic
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP/UDP
Source: Any
Source Port: Any
Destination: Any
Destination Port: 0
Gateway: Any
Schedule: Any
Description: block all targeting port 0

Entry ID: 9
Type: Automatic
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP
Source: \<sshlockout>
Source Port: Any
Destination: (self)
Destination Port: SSH (22)
Gateway: Any
Schedule: Any
Description: sshlockout

Entry ID: 10
Type: Automatic
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP
Source: \<sshlockout>
Source Port: Any
Destination: (self)
Destination Port: HTTPS (443)
Gateway: Any
Schedule: Any
Description: sshlockout

Entry ID: 11
Type: Automatic
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: \<virusprot>
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: virusprot overload table

Entry ID: 12
Type: Automatic
Action: Allow
State: Active
Direction: Out
Applies: Last Match
Protocols: IPv4+6 Any
Source: Any
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: let out anything from firewall host itself

### Internal

Count: 15

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Automatic
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP
Source: Any
Source Port: Any
Destination: (self)
Destination Port: HTTP (80)
Gateway: Any
Schedule: Any
Description: anti-lockout rule

Entry ID: 14
Type: Automatic
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP
Source: Any
Source Port: Any
Destination: (self)
Destination Port: HTTPS (443)
Gateway: Any
Schedule: Any
Description: anti-lockout rule

Entry ID: 15
Type: Manual
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Any
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Block any untagged traffic from anywhere to anywhere.

### Management

Count: 13

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Management Network
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Management Network to anywhere.

### Infrastructure

Count: 20

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: Management Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Infrastructure Network to Management Network.

Entry ID: 14
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: Infrastructure Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Infrastructure Network to Infrastructure Network.

Entry ID: 15
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: Storage Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Infrastructure Network to Storage Network.

Entry ID: 16
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: Labs Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Infrastructure Network to Labs Network.

Entry ID: 17
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: DMZ Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Infrastructure Network to DMZ Network.

Entry ID: 18
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: Backups Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Infrastructure Network to Backups Network.

Entry ID: 19
Type: Manual
Action: Reject
State: Inactive
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: WAN Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Infrastructure Network to External-LAN Network.

Entry ID: 20
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Infrastructure Network
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Infrastructure Network to anywhere (Internet).

### Storage

Count: 19

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: Management Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Storage Network to Management Network.

Entry ID: 14
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: Storage Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Storage Network to Storage Network.

Entry ID: 15
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: Backups Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Storage Network to Backups Network.

Entry ID: 16
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: Infrastructure Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Storage Network to Infrastructure Network.

Entry ID: 17
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: Labs Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Storage Network to Labs Network.

Entry ID: 18
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: DMZ Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Storage Network to DMZ Network.

Entry ID: 19
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Storage Network
Source Port: Any
Destination: WAN Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Storage Network to External-LAN Network.

### Backups

Count: 13

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Backups Network
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from Backups Network to anywhere.

### Labs

Count: 22

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Labs Network
Source Port: Any
Destination: Management Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Labs Network to Management Network.

Entry ID: 14
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Lab Network
Source Port: Any
Destination: Infrastructure Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Labs Network to Infrastructure Network.

Entry ID: 15
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Lab Network
Source Port: Any
Destination: Storage Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Labs Network to Storage Network.

Entry ID: 16
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Lab Network
Source Port: Any
Destination: Backups Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Labs Network to Backups Network.

Entry ID: 17
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Lab Network
Source Port: Any
Destination: DMZ Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Labs Network to DMZ Network.

Entry ID: 18
Type: Manual
Action: Reject
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Lab Network
Source Port: Any
Destination: WAN Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Reject any traffic from Labs Network to WAN Network.

Entry ID: 19
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 TCP/UDP
Source: Labs Network
Source Port: Any
Destination: This Firewall
Destination Port: DOMAIN (53)
Gateway: Any
Schedule: Any
Description: Allow any IPv4 DNS traffic from Labs Network to OPNsense Port 53.

Entry ID: 20
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4 TCP/UDP
Source: Labs Network
Source Port: Any
Destination: Any
Destination Port: HTTPS (443)
Gateway: Any
Schedule: Any
Description: Allow any IPv4 TCP/UDP traffic from Labs Network to anywhere Port 443 (Internet).

Entry ID: 21
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4 TCP/UDP
Source: Labs Network
Source Port: Any
Destination: Any
Destination Port: HTTP (80)
Gateway: Any
Schedule: Any
Description: Allow any IPv4 TCP/UDP traffic from Labs Network to anywhere Port 80 (Internet).

Entry ID: 22
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4 TCP/UDP
Source: Lab Network
Source Port: Any
Destination: Any
Destination Port: NTP (123)
Gateway: Any
Schedule: Any
Description: Allow any IPv4 TCP/UDP traffic from Labs Network to anywhere Port 123 (Internet).

### DMZ

Count: 19

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4 TCP
Source: Any
Source Port: Any
Destination: 10.10.30.11
Destination Port: Any - 25565
Gateway: Any
Schedule: Any
Description: Allow any IPv4 TCP traffic from anywhere to Minecraft VM Port 25565.

Entry ID: 14
Type: Manual
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: DMZ Network
Source Port: Any
Destination: Management Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Block any traffic from DMZ Network to Management Network.

Entry ID: 15
Type: Manual
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: DMZ Network
Source Port: Any
Destination: Infrastructure Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Block any traffic from DMZ Network to Infrastructure Network.

Entry ID: 16
Type: Manual
Action: Block
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: DMZ Network
Source Port: Any
Destination: Any
Destination Port: Storage Network
Gateway: Any
Schedule: Any
Description: Block any traffic from DMZ Network to Storage Network.

Entry ID: 17
Type: Manual
Action: Block
State: Inactive
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: DMZ Network
Source Port: Any
Destination: Backups Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Block any traffic from DMZ Network to Backups Network.

Entry ID: 18
Type: Manual
Action: Block
State: Inactive
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: DMZ Network
Source Port: Any
Destination: Labs Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Block any traffic from DMZ Network to Labs Network.

Entry ID: 19
Type: Manual
Action: Allow
State: Inactive
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: DMZ Network
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from DMZ Network to anywhere (Internet).

### WAN

Count: 15

Entry ID: 1-12
Floating Rules

Entry ID: 13
Type: Automatic (Manual from Destination NAT)
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4 TCP
Source: Any
Source Port: Any
Destination: 10.10.30.11
Destination Port: 25565
Gateway: Any
Schedule: Any
Description: Redirect IPv4 TCP Port 25565 traffic from WAN interface to Minecraft VM.

Entry ID: 14
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4 Any
Source: 192.168.254.100
Source Port: Any
Destination: Any
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any IPv4 traffic from Workstation PC to anywhere.

Entry ID: 15
Type: Manual
Action: Allow
State: Active
Direction: In
Applies: First Match
Protocols: IPv4+6 Any
Source: Any
Source Port: Any
Destination: DMZ Network
Destination Port: Any
Gateway: Any
Schedule: Any
Description: Allow any traffic from anywhere to DMZ Network.
