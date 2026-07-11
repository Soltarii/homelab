# Network Design

## Address Space

Primary range:

10.10.0.0/17 (10.10.0.1 - 10.10.127.254)

## VLAN Design

| VLAN | Purpose | Address Range |
|------|---------|--------|
| 10 | Management | 10.10.10.0 - 10.10.10.255 |
| 11 | Infrastructure | 10.10.11.0 - 10.10.11.255 |
| 12 | Storage | 10.10.12.0 - 10.10.12.255 |
| 13 | Backup | 10.10.13.0 - 10.10.13.255 |
| 20 | Internal Services | 10.10.20.0 - 10.10.20.255 |
| 30 | DMZ | 10.10.30.0 - 10.10.30.255 |
| 99 | Native | n/a |
