# 04 — Active Directory Setup

## 1. Overview

This document describes the deployment and configuration of **Active Directory Domain Services (AD DS)** for the Small Office Infrastructure Lab.

Active Directory provides:
- Centralized identity management
- Authentication and authorization
- DNS for internal name resolution
- Group Policy enforcement

The domain environment supports both **Windows and Linux systems**, enabling centralized user and system management.


## 2. Domain Design

### 2.1 Domain Name

| Item        | Value        |
|------------|--------------|
| Domain FQDN| `lab.local`  |
| NetBIOS    | `LAB`        |

> A private internal domain name is used for this lab environment.

### 2.2 Server Details

| Role              | Value              |
|------------------|--------------------|
| OS               | Windows Server 2022 |
| Hostname         | `SRV-DC01`          |
| IP Address       | 10.10.10.10         |
| VLAN             | Server (VLAN 10)    |

The domain controller uses a **static IP address** and resides in the Server VLAN.


## 3. Base Server Configuration

### 3.1 Initial Configuration

Performed prior to AD installation:
- Set static IP address
- Configure DNS to point to itself
- Rename server
- Install latest Windows updates
- Harden local administrator password


### 3.2 Time Synchronization

- Domain controller is the authoritative time source
- Clients sync time automatically via domain hierarchy

Time consistency is critical for Kerberos authentication.


## 4. Installing Active Directory Domain Services

### 4.1 Role Installation

Installed roles:
- Active Directory Domain Services
- DNS Server

Installation method:
- Server Manager → Add Roles and Features


### 4.2 Domain Promotion

Steps:
1. Promote server to domain controller
2. Create new forest
3. Specify domain name (`lab.local`)
4. Configure DSRM password
5. Install DNS
6. Reboot server

After reboot, the domain is fully operational.


## 5. DNS Configuration

### 5.1 DNS Role

DNS is integrated with Active Directory and provides:
- Secure dynamic updates
- Internal name resolution
- Service location (SRV) records


### 5.2 Forwarders

DNS forwarders are configured to:
- pfSense
- Public resolvers (optional)

This allows resolution of external domains while maintaining internal DNS control.


## 6. Organizational Unit (OU) Design

### 6.1 OU Structure

The OU design follows **departmental and functional separation**.

LAB.LOCAL
├── Servers
├── Workstations
├── Users
│ ├── IT
│ ├── Staff
│ ├── Finance
│ └── Guest
└── Groups


### 6.2 Rationale

- Clear separation of users and systems
- Easier GPO targeting
- Simplified administration and scaling


## 7. User and Group Management

### 7.1 Security Groups

Role-based security groups are created:

- `IT-Admins`
- `Staff-Users`
- `Finance-Users`
- `Guest-Users`
- `FileServer-Access-*`

Groups are used to:
- Assign permissions
- Control access to resources
- Apply Group Policies


### 7.2 User Accounts

- Users are created per department
- Users are assigned to appropriate security groups
- Naming convention:
    Example: afird


## 8. Group Policy Configuration

### 8.1 Baseline Domain Policies

Configured policies include:
- Password complexity and length
- Account lockout thresholds
- Kerberos policy defaults


### 8.2 Department-Level GPOs

Applied per OU:
- Drive mappings
- Desktop restrictions
- Control Panel access
- Login scripts


### 8.3 Security Hardening GPOs

- Disable local administrator login for users
- Restrict PowerShell and CMD access (non-IT)
- Disable removable storage (USB)
- Enforce Windows Firewall


## 9. Client Domain Join

### 9.1 Windows Clients

Steps:
1. Set DNS to AD DNS server
2. Join domain (`lab.local`)
3. Place computer in correct OU
4. Apply Group Policies


### 9.2 Linux Integration

Linux servers are joined using:
- Kerberos
- SSSD
- Realmd

This allows:
- Domain-based authentication
- Centralized user management
- Unified identity across platforms


## 10. Validation and Testing

Tests performed:
- User login from multiple VLANs
- Group Policy application
- DNS resolution (internal and external)
- Linux domain authentication
- Access control based on group membership


## 11. Security Considerations

- Least-privilege access enforced via groups
- Admin accounts separated from standard users
- GPOs used to reduce attack surface
- Domain controller isolated in Server VLAN

