# 02-network-architecture.md

## Network Architecture
The lab network is segmented into VLANs for security and traffic isolation.

### VLAN & IP Schema
| VLAN | Purpose | Subnet | DHCP Scope |
|------|---------|--------|------------|
| 10 | Management | 10.10.10.0/24 | 10.10.10.50–200 |
| 20 | Servers | 10.10.20.0/24 | Static only |
| 30 | Users | 10.10.30.0/24 | 10.10.30.50–200 |
| 40 | Guests | 10.10.40.0/24 | 10.10.40.50–200 |
| 50 | Monitoring | 10.10.50.0/24 | Static only |

pfSense manages the routing, DHCP, inter-VLAN access, and firewall rules.

### Inter-VLAN Firewall Rules
| Source | Destination | Ports | Purpose |
|--------|-------------|-------|---------|
| Users VLAN | Servers VLAN | 445, 389, 88, 53 | File server & AD access |
| Users VLAN | Internet | Any | Web browsing & email |
| Guests VLAN | Internal VLANs | None | Isolate guests |
| Management VLAN | All VLANs | RDP, SSH, WebUI | Admin access |
| Monitoring VLAN | Servers | ICMP, Zabbix Agent | Health monitoring |

### Network Diagram
- See `diagrams/network-diagram.mermaid` and `diagrams/final-diagram.png` for topology.
