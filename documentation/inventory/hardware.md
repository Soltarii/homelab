# Hardware

The following documentation is intended to be an authoritative source of all information pertaining to the hardware utilized within my External-LAN and Internal-LAN excluding virtually-assigned specifications (see addressing.md).

TO-DO:
- Determine missing server component information from iDRAC 

## External-LAN Hardware

### ONT

Manufacturer: Adtran
Model: 411 Micro GPON Indoor Optical Network Transceiver
Ports: 1 RJ-45 Ethernet, 1 RJ-11 Voice, 1 GPON
Throughput: 10/100/1000Mbps
Dimensions: 1" (H) x 2.8" (W) x 2.8" (D)
Features: SFU/SBU/MDU Deployment Support, POTS Support, VoIP via SIP or MGCP, IPTV, IGMP Snooping, Traffic Policing and Shaping.
MAC Address: 38:F8:F6:1F:6A:1C
P/N: 1287787F1
S/N: ADTN23069212
CLEI: BVMDT00DRA

### Router

Manufacturer: Nokia / ALCL
Model: WiFi Beacon 6
Ports: 3 RJ-45 Ethernet
Throughput: 10/100/1000Mbps
Dimensions: 8.1" (H) x 6.3" (W) x 2.4" (D)
Weight: 2.9lbs
Features: Web GUI, WiFi 2.4GHz, WiFi 5GHz-low, WiFi 5GHz-high, DHCP, DNS, QoS, Firewall, Log, Diagnostics, Port Forwarding, Port Triggering, DDNS, UPnP, DLNA
Current Firmware: 3FE49062KJLJ45
Newest Firmware: Automatic, ISP-Specific
SSID: KINECTIC_9b5cf1
MAC Address: 28:74:F5:9B:5C:F1
P/N: HA-0336G-A
S/N: ALCLEB386E50


### Workstation PC

## Internal-LAN Hardware

### Rack - 10U Server Cabinet

Manufacturer:
Dimensions: 31.5" (D) x 23.5" (W) x 25.5" (H)
Weight:
Capacity: 
Features: Vented Doors, Caster Wheels, Cabinet Fan

Accessory: 1U Dell Static Ready Side Rail A8 Kit
Accessory P/N: D419MY819K053D7M
Accessory S/N: 

### UNUSED - Rack - 8U AxcessAbles Homelab Server Rack

Manufacturer: AxcessAbles
Dimensions: 18" (D) x 20" (W) x 20" (H)
Weight: 31lbs
Capacity: 440 lbs
Features: Caster Wheels, Open Front, Open Back
UPC: 810037260403
ASIN: B08XX7WL68

### UPS - APC Smart-UPS X

Manufacturer: APC (American Power Conversion)
Model: Smart-UPS X 1500VA
Dimensions: 19.3" (D) x 17" (W) x 3.5" (H)
Weight: 55lbs
Current Firmware: 02.0 - 1031
Newest Firmware: 15.5 - 1031
Features: SmartConnect, Alarm, NEMA 5-15P Input, 
Surge Rating: 645J
Power Rating: 1350W/1440VA
Output Voltage: 120V AC 1 Phase
MAC Address:
P/N: SMX1500RM2UC
S/N: AS2152161104
Product Key: 32EB6C-B6B9-B8FF
Security Code: 31V7TDBLWEE9QWLNM4Y1ZRGM

Accessory: Battery
Type: Lead-Acid
Voltage: 48V
Charging Power: 103W
Accessory P/N: APCRBC115
Accessory S/N:

### Switch - Cisco Catalyst 3750 v05 L3 Switch, 48-Port POE

Manufacturer: Cisco
Model: Catalyst 3750
Ports: 48-Port RJ-45, 4-Port SFP
Throughput: 10Gbps/100Gbps, 1Gbps
RAM: 128MB DRAM
Storage: 16MB Flash
Dimensions: 1.73" (H) x 17.5" (W) x 14.8" (D)
Weight: 13lbs
Current Firmware: 12.2(55)SE10
Newest Firmware:
Features: Console Port, StackWise
MAC Address: 00:16:46:1D:E1:00
PIDVID: WS-C3750-48PS-S V05
S/N: CAT0946R243
Motherboard S/N: CAT09460RYG
Power Supply S/N: DTH09459KNZ
SFP Module S/N: CAT09460Z2Z
HECI/CLEI: COM1W00ARB


### Switch - Core/Distribution

Manufacturer: Cisco
Model: Nexus 3064-X
Ports: 48-Port SFP+, 4-Port QSFP+
Throughput: 100Mbps/1Gbps/10Gbps, 10Gbps/40Gbps
RAM: 4GB
Storage: 2GB Flash
Dimensions: 1.72" (H) x 17.3" (W) x 19.7" (D)
Weight: 20.5lbs
Current Firmware: 9.3(10)
Newest Firmware:
Features: Control Port, 2 Management Ports, vPC, POAP, EEM,
MAC Address: 
PIDVID: N3K-C3064PQ-10GX V01
Power Supply 1 PIDVID: N2200-PAC-400W V05
Power Supply 2 PIDVID: N2200-PAC-400W V06
Fan PIDVID: N3K-C3064-FAN-F V00
S/N: FOC1916R2BU
Port Supervisor S/N: F0C19224LAH
Power Supply 1 S/N: LIT21204G2S
Power Supply 2 S/N: LIT222126X2
UPC: 011110632531

### Connectors - Adapters

Manufacturer: Cisco
Model: GLC-TE SFP Adapter
Form Factor: 1000BASE-T RJ45 to SFP
Throughput: 1Gbps
Range: 100m
Count: 1
Inventory ID: 1
P/N: 30-1475-03
S/N: ACW25301KS9 V03
Mystery Side ID?: CMUIAGBCAC
Location: Nexus Port 35

Manufacturer: Cisco
Model: GLC-T SFP Adapter
Form Factor: 1000BASE-T RJ45 to SFP
Throughput: 1Gbps
Range: 100m
Count: 7
Inventory ID: 2-5
P/N: 30-1410-03
S/N: 2) MTC1116T045; 3) MTC15110GQL; 4) MTC15190F67; 5) MTC1704026V
Mystery Side ID?: CNS8TUTAAB
Location: 2) Catalyst SFP Port 1; 3) Nexus Port 33; 4) Catalyst SFP Port 2; 5) Nexus Port 34

Manufacturer: Cisco
Model: GLC-T SFP Adapter
Form Factor: 1000BASE-T RJ45 to SFP
Throughput: 1Gbps
Range: 100m
Count: 1
Inventory ID: 6
P/N: 30-1410-04
S/N: MTC2033029P
Mystery Side ID?: CNS8TUTAAC
Location: Catalyst SFP Port 3

Manufacturer: Cisco
Model: GLC-T SFP Adapter
Form Factor: 1000BASE-T RJ45 to SFP
Throughput: 1Gbps
Range: 100m
Count: 1
Inventory ID: 7-8
P/N: 30-1410-02
S/N: 7) MTC110601F0; 8) MTC082103P4
Mystery Side ID?: CNS8TUTAAA
Location: 7) Catalyst SFP Port 4; 8) Nexus Port 36

Manufacturer: Cisco
Model: GLC-T SFP Adapter
Form Factor: 1000Base-T RJ45 to SFP
Throughput: 1Gbps
Range: 100m
Count: 1
Inventory ID: 9
P/N: 30-1423-02
S/N: GR110303416
Location: Currently Unused

Manufacturer: LongLine
Model: GLC-T-LL
Form Factor: 1000BASE-T RJ45 to SFP
Throughput: 1Gbps
Inventory ID: 10
P/N: EC1303270672
S/N: GR1304078640
Location: Currently Unused

### WLC

Manufacturer: Cisco
Model: WLAN 2500 Series Controller (Model 2504)
Ports: 4 RJ45 Ethernet, 1 RJ45 Console
MAC Address: 44:03:A7:31:9B:C0
PIDVID: AIR-CT2504-K9 V01
P/N: 74-7363-03
S/N: PSZ17020K6U
Location: Catalyst Fe2/0/5

### APs

Manufacturer: Cisco
Model: Aironet 1130AG Series
Ports: 1 RJ45 Ethernet, 1 RJ45 Console
Throughput: 54Mbps
Count: 1
MAC Address: 00:12:D9:2B:35:98
PIDVID: AIR-AP1131AG-A-K9 V01
P/N: 47-16546-02 Rev A0
S/N: FTX0906T08B
Location: Currently Unused

Manufacturer: Cisco
Model: Aironet 3500 Series (Model 3502)
Ports: 1 RJ45 Ethernet, 1 RJ45 Console
Throughput:
Count: 1
MAC Address: 70:CA:9B:99:41:E0
PIDVID: AIR-CAP3502E-A-K9 V01
P/N: 47-22552-01 Rev A0
S/N: FTX1550E61L
Location: Currently Unused

Manufacturer: Cisco
Model: Aironet 3500 Series (Model 3502)
Ports: 1 RJ45 Ethernet, 1 RJ45 Console
Throughput:
Count: 1
MAC Address: 70:81:05:51:31:DE
PIDVID: AIR-CAP3502I-T-K9 V01
P/N: 47-22587-01 Rev B0
S/N: FGL1533S2W5
Location: Currently Unused

### Server - Dell PowerEdge R640 E39S Rack-Mountable 1U Server

Manufacturer: Dell
Model: PowerEdge R640
Dimensions:
Weight:
Current Firmware: 2.25.0
Newest Firmware:
Features: iDRAC,
P/N:
S/N: 91MT643
Components: (1) Backplane, (1) Backplane Expansion Board, (2) CPUs, (1) Mini RAID Controller, (1) Storage Controller Card, (2) Ethernet Controllers, (2) PSUs, (4) RAM RDIMMs, (8) Enterprise SSDs

#### Motherboard

Manufacturer: Dell
Model: PowerEdge R640 System Board
P/N: 06DKY5 v.A02
S/N: .91MT643.CNCMU00053004B

#### 10x 2.5" Bay Dell Backplane

Manufacturer: Dell
Model:
Throughput:
Features:
P/N:
S/N:

#### Expansion Board - Dell Backplane Expansion Board

Manufacturer: Dell
Model:
Features:
P/N:
S/N:

#### Mini RAID Controller

Manufacturer: Dell
Model: HBA330 Mini Raid Controller
Chipset: LSI SAS3008
Form Factor: 
Throughput: 12Gbps
Features:
PCI ID: 1a:00.0
P/N:
S/N: 

#### Storage Controller Card

Manufacturer:
Model: BOSS-S1 Boot Optimized Server Storage Controller Card
Throughput:
Current Firmware: MV.R00-0
Newest Firmware:
Features: Dual M.2 SSD Slots, RAID 1 Configuration, ACHI
PCI ID: 3b:00.0
P/N:
S/N: 0913f101dbd50010

#### RDMA Ethernet Controller

Manufacturer:
Model: BCM57414
Throughput: 10Gbps/25Gbps
Features: 2 NIC Ports
MAC Addresses:
PCI ID: 5e:00.0, 5e:00.1
P/N: 024GFD
S/N: 

#### Ethernet Controller

Manufacturer:
Model: BCM5720
Throughput:
Features: 4 NIC Ports
MAC Addresses:
PCI ID: 18:00.0, 18:00.1, 19:00.0, 19:00.1
P/N: 0FM487
S/N: 

#### PSUs

Manufacturer: Dell
Model:L750E-S0
Capacity: 750W
P/N:
S/N: 

#### CPUs

Manufacturer: Intel
Model: Xeon Gold 5520
Architecture: x86_64
Socket Type: LGA2011
Sockets: 2
Cores: 18 (36 Total)
Threads: 36 (72 Total)
Frequency: 2.2 - 4GHz
Features:
P/N:
S/N: 

#### RAM

Manufacturer: Samsung
Model: PC4-2666V
Type: RDIMM
Class: DDR4
Capacity: 32GB (128GB Total)
Speed: 2666 MT/s
Features: ECC
P/N: M393A4K40BB2-CTD7Y
S/N: 123D42B6, 123D25D0, 123D3709, 123D3E78
Asset Tag: 03192451

#### Storage Controller Card SSDs

Manufacturer: Intel
Model: D3-4510
Form Factor: M.2 2280
Bus Interface: SATA 3
Capacity: 240GB (480GB Total)
Throughout: 500Mbps
Features: TLC
P/N: SSDSCKKB240G8
S/N: 

#### SSD

Manufacturer: HGST
Model: Ultrastar SS200
Form Factor: 2.5"
Bus Interface: SAS 3
Capacity: 800GB / 745.21GiB (3.2TB / 2.98TiB Total, 1.6TB / 1.49TiB Pools)
Throughput: 12Gbps
Configuration: (2) RAID 1 (Mirror)
Bay Slot: 1, 2, 3, 4
P/N: SXHLLL800GB - SDLL1DLR800GCDA1 
S/N: A030FB0C, A039B041, A0442A1D, A04242BA

#### SSD

Manufacturer: HGST
Model: Ultrastar SS200
Form Factor: 2.5"
Bus Interface: SAS
Capacity: 1.92TB / 1.75TiB (3.84TB / 3.5TiB Total)
Throughput: 12Gbps
Configuration: RAID 1 (Mirror)
Bay Slot: 5, 6
P/N: SDLL1CLR020TCDA1
S/N: A040DAB8, A0459F4A

#### SSD

Manufacturer: Intel
Model: D3-4510
Form Factor: 2.5"
Bus Interface: SATA
Capacity: 1.92TB / 1.75TiB
Throughput: 6Gbps
Configuration: Stripe
Bay Slot: 7
P/N: SSDSC2KB019T8
S/N: PHYF918601KK1P9DGN

#### SSD

Manufacturer: Intel
Model: D3-4510
Form Factor: 2.5"
Bus Interface:
Capacity: 480GB / 447.13GiB
Throughput: 6Gbps
Configuration: Stripe
Bay Slot: 8
P/N: SSDSC2kB480G8L_01PE325D7A09673LEN
S/N: PHYF8330081K480BGN
