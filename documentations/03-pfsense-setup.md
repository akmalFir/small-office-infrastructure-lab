# 03 — pfSense Setup

## 1. Overview

This document details the installation and configuration of **pfSense** as the central firewall, router, and network security device for the Small Office Infrastructure Lab.

pfSense is responsible for:
- Routing between VLANs
- Enforcing firewall segmentation
- Providing DHCP or DHCP relay services
- Forwarding DNS queries
- Providing VPN access for remote administration

The pfSense firewall runs as a virtual machine on **KVM/QEMU**, managed via **libvirt**.


## 2. Virtual Machine Configuration

### 2.1 VM Specifications

| Resource | Value              |
|--------|--------------------|
| vCPU   | 2                  |
| RAM    | 2–4 GB              |
| Disk   | 20 GB               |
| NICs   | 2 (WAN, LAN_TRUNK) |


### 2.2 Network Interfaces (libvirt)

| pfSense Interface | libvirt Network | Purpose                    |
|------------------|-----------------|----------------------------|
| WAN              | `wan-net` (NAT) | Internet connectivity      |
| LAN_TRUNK        | `trunk-net`     | VLAN trunk (internal LANs) |

- The WAN interface uses libvirt NAT for outbound internet access
- The LAN interface is VLAN-aware and carries all internal VLAN traffic


## 3. pfSense Installation

1. Create a new VM using the pfSense ISO
2. Boot the VM and select:
   - Install pfSense
   - Default keymap
   - UFS file system
3. Complete installation and reboot
4. Assign interfaces during initial console setup:
   - `WAN` → NAT interface
   - `LAN` → Trunk interface

Once complete, access the web interface via the LAN IP.


## 4. Interface Assignment and VLAN Configuration

### 4.1 Physical Interface Setup

- The LAN interface is treated as a **VLAN trunk**
- No IP address is assigned directly to the parent interface
- All IP addressing is done on VLAN sub-interfaces


### 4.2 VLAN Creation

Navigate to: Interfaces → Assignments → VLANs


Create the following VLANs on the trunk interface:

| VLAN ID | Description  |
|--------:|--------------|
| 10      | Server VLAN  |
| 20      | Management VLAN |
| 30      | Staff VLAN   |
| 40      | Finance VLAN |
| 50      | Guest VLAN   |


### 4.3 VLAN Interface Assignment

Assign each VLAN as a separate interface:

| Interface Name | VLAN ID | Subnet         |
|---------------|--------:|----------------|
| SERVER        | 10      | 10.10.10.1/24  |
| MGMT          | 20      | 10.10.20.1/24  |
| STAFF         | 30      | 10.10.30.1/24  |
| FINANCE       | 40      | 10.10.40.1/24  |
| GUEST         | 50      | 10.10.50.1/24  |

Each interface:
- Is enabled
- Uses static IPv4 configuration
- Acts as the default gateway for its VLAN


## 5. DHCP Configuration

### 5.1 DHCP Options

Two DHCP models are supported:

- **Option A: pfSense DHCP Server**
- **Option B: Windows Server DHCP with relay**

This lab supports both, depending on learning goals.


### 5.2 pfSense DHCP Server (Default)

Navigate to: Services → DHCP Server


For each VLAN:
- Enable DHCP
- Define a lease range (e.g. `.100 – .199`)
- Set DNS server to Active Directory DNS
- Set default gateway to pfSense interface IP


### 5.3 DHCP Relay (Optional)

If using Windows DHCP:

- Disable DHCP servers on VLAN interfaces
- Enable DHCP Relay: Services → DHCP Relay

- Point relay to Windows DHCP server IP
- Enable relay on all internal VLAN interfaces


## 6. DNS Configuration

### 6.1 DNS Resolver / Forwarder

pfSense does **not** act as an authoritative DNS server.

Configuration:
- Enable DNS Resolver or DNS Forwarder
- Forward queries to:
- Active Directory DNS servers

This ensures:
- AD domain resolution works correctly
- Clients use centralized DNS

## 7. Firewall Rules

### 7.1 Default Behavior

pfSense applies firewall rules **per interface**.

Policy:
- Deny all inter-VLAN traffic by default
- Explicit allow rules only where required


### 7.2 Example Firewall Rules

#### STAFF VLAN
- Allow DNS → AD DNS servers
- Allow SMB/HTTP/HTTPS → Server VLAN
- Allow Internet access
- Block access to Management VLAN

#### FINANCE VLAN
- Allow DNS → AD DNS servers
- Allow SMB → Finance shares only
- Allow Internet access

#### MANAGEMENT VLAN
- Allow access to all internal VLANs
- Allow management protocols (RDP, SSH, HTTPS)

#### GUEST VLAN
- Allow Internet access only
- Block all access to internal networks


### 7.3 Logging

- Block rules have logging enabled
- Logs are forwarded to centralized logging (Graylog / Wazuh)


## 8. NAT Configuration

- Outbound NAT is enabled on the WAN interface
- Internal VLANs use automatic outbound NAT
- No inbound NAT or port forwarding is configured

This reflects environments behind CGNAT or ISP-managed networks.


## 9. VPN Configuration (WireGuard)

WireGuard is configured for:
- Secure remote administrative access
- Management VLAN access only

Configuration includes:
- Tunnel interface
- Peer configuration
- Firewall rules limiting VPN access scope


## 10. Hardening and Best Practices

- Disable unnecessary services
- Restrict GUI access to Management VLAN
- Change default admin credentials
- Enable automatic configuration backups
- Keep pfSense updated


## 11. Validation and Testing

Tests performed:
- Internet access from all VLANs
- Inter-VLAN access only where allowed
- DHCP assignment per VLAN
- DNS resolution via AD
- VPN connectivity and access control







