# 02 — Network Architecture

## 1. Overview

This document describes the **network architecture** used in the Small Office Infrastructure Lab.  
The design focuses on **segmentation, security, and manageability**, similar to real-world SME environments.

The network is fully virtualized using **KVM/QEMU with libvirt**, with **pfSense acting as the central firewall and router**.  
Multiple VLANs are used to separate traffic by function and department.


## 2. Design Goals

The network architecture was designed with the following goals:

- Logical separation of users and services
- Reduced attack surface through VLAN segmentation
- Centralized routing and firewall enforcement
- Simple scalability for future expansion
- Clear and predictable IP addressing


## 3. Virtual Networking (KVM/QEMU)

### 3.1 libvirt Network Model

The lab uses **libvirt virtual networks** to simulate real physical switching behavior.

Key concepts:
- One **VLAN-aware trunk network** connects pfSense and all VLAN-capable VMs
- pfSense performs **inter-VLAN routing**
- Firewall rules control traffic between VLANs

This design mirrors a **router-on-a-stick** topology commonly used in small offices.


### 3.2 Virtual Network Types Used

| Network Name | Type        | Purpose                                   |
|-------------|------------|-------------------------------------------|
| `wan-net`   | NAT        | Internet access for pfSense WAN interface |
| `trunk-net` | Bridge/Open| VLAN trunk carrying internal VLANs        |

> The WAN network uses NAT for outbound internet access.  
> The internal network is VLAN-aware and does not perform NAT.


## 4. VLAN Design

### 4.1 VLAN Overview

The internal network is segmented using VLANs based on function and department.

| VLAN ID | Name        | Subnet         | Description                              |
|--------:|-------------|----------------|------------------------------------------|
| 10      | Server      | 10.10.10.0/24  | Core infrastructure servers              |
| 20      | Management  | 10.10.20.0/24  | Admin and management access              |
| 30      | Staff       | 10.10.30.0/24  | General employee devices                 |
| 40      | Finance     | 10.10.40.0/24  | Restricted finance department            |
| 50      | Guest       | 10.10.50.0/24  | Internet-only guest network              |

Each VLAN has its own **IP subnet, DHCP scope, and firewall policy**.


### 4.2 VLAN Rationale

- **Server VLAN (10)**  
  Isolates critical infrastructure from user networks.

- **Management VLAN (20)**  
  Restricts administrative access to servers and network devices.

- **Staff VLAN (30)**  
  Standard user network with controlled access to services.

- **Finance VLAN (40)**  
  Additional isolation for sensitive systems and data.

- **Guest VLAN (50)**  
  No access to internal resources, internet-only access.


## 5. pfSense Network Role

pfSense is deployed with **multiple interfaces**:

| Interface | Role        | Notes                                  |
|----------|-------------|----------------------------------------|
| WAN      | Internet    | Connected to NAT network (`wan-net`)   |
| LAN_TRUNK| Internal    | VLAN trunk carrying all internal VLANs |

### 5.1 VLAN Interfaces on pfSense

pfSense creates sub-interfaces on the trunk interface:

- `LAN_TRUNK.10` → Server VLAN  
- `LAN_TRUNK.20` → Management VLAN  
- `LAN_TRUNK.30` → Staff VLAN  
- `LAN_TRUNK.40` → Finance VLAN  
- `LAN_TRUNK.50` → Guest VLAN  

pfSense acts as the **default gateway** for all VLANs.


## 6. IP Addressing Strategy

### 6.1 Gateway Addressing

Each VLAN uses the `.1` address as its gateway:

| VLAN | Gateway        |
|------|----------------|
| 10   | 10.10.10.1     |
| 20   | 10.10.20.1     |
| 30   | 10.10.30.1     |
| 40   | 10.10.40.1     |
| 50   | 10.10.50.1     |

This ensures consistency and easy troubleshooting.


### 6.2 DHCP Design

- DHCP services are provided by:
  - pfSense **or**
  - Windows Server (via DHCP relay)

- Each VLAN has:
  - Separate DHCP scope
  - Dedicated DNS settings
  - Controlled lease ranges

Static IPs are reserved for servers and infrastructure components.


## 7. Firewall Segmentation Strategy

Firewall rules are enforced **on pfSense**, not at the VM level.

### 7.1 Default Policy

- **Deny inter-VLAN traffic by default**
- Explicit allow rules are created where required


### 7.2 Inter-VLAN Access Examples

| Source VLAN | Destination | Allowed Services              |
|------------|-------------|-------------------------------|
| Staff      | Server      | DNS, SMB, HTTP/HTTPS          |
| Finance   | Server      | DNS, SMB (restricted shares) |
| Management| All VLANs   | Admin and management access  |
| Guest     | Any Internal| ❌ Blocked                    |
| Any VLAN  | Internet    | ✅ Allowed                    |

This approach limits lateral movement and enforces least privilege.


## 8. DNS and Routing Flow

- All clients use **Active Directory DNS**
- pfSense forwards DNS queries to the AD DNS servers
- pfSense handles routing between VLANs
- Default route points from pfSense to the NAT WAN network


## 9. Traffic Flow Example

**Staff workstation accessing file server:**

1. Client in VLAN 30 sends request to file server in VLAN 10
2. Traffic hits pfSense gateway (10.10.30.1)
3. Firewall rule allows SMB traffic to Server VLAN
4. File server responds via pfSense
5. Access is further controlled by AD authentication and NTFS permissions


## 10. Security Considerations

- No direct VLAN-to-VLAN access without explicit rules
- Management access restricted to Management VLAN
- Guest VLAN isolated from internal networks
- Logging enabled for blocked traffic
- All routing and filtering centralized on pfSense


## 11. Diagram Reference

A full network diagram is available in:

diagrams/network-architecture.png

The diagram illustrates:
- VLAN trunking
- pfSense routing
- Server and client placement
