# 06 — Ubuntu Server Active Directory Integration

## 1. Overview

This document describes the integration of **Ubuntu Server** into the Active Directory domain used in the Small Office Infrastructure Lab.

The goal of this setup is to:
- Enable centralized authentication for Linux systems
- Allow domain users to log in using AD credentials
- Maintain consistent identity management across Windows and Linux systems
- Reduce local account sprawl on Linux servers

Authentication is handled using **Kerberos** and **SSSD**, which is a common approach in enterprise Linux environments.


## 2. Server Details

| Item        | Value               |
|------------|---------------------|
| OS         | Ubuntu Server (LTS) |
| Hostname   | `srv-ubu01`         |
| IP Address | 10.10.10.30         |
| VLAN       | Server (VLAN 10)    |
| Role       | Linux application / utility server |

The server uses a **static IP address** and resides in the Server VLAN.


## 3. Prerequisites

Before joining the domain, the following conditions must be met:

- Active Directory domain is operational
- DNS resolution to AD DNS servers is working
- Time synchronization with the domain controller
- Network connectivity to the Domain Controller (Kerberos, LDAP, DNS ports)

DNS on the Ubuntu server is configured to point to the **AD DNS server**.


## 4. Required Packages

The following packages are installed:

- `realmd`
- `sssd`
- `sssd-tools`
- `adcli`
- `krb5-user`
- `oddjob`
- `oddjob-mkhomedir`
- `packagekit`

Installation command:

```
sudo apt update
sudo apt install -y realmd sssd sssd-tools adcli krb5-user oddjob oddjob-mkhomedir packagekit
```

## 5. Domain Discovery

Verify domain discovery using:

`realm discover lab.local`

Expected result:

- Domain is discovered
- Domain controllers are listed
- Kerberos realm is identified

This confirms DNS and network configuration are correct.


## 6. Joining the Domain

Join the domain using a domain administrator account:

`sudo realm join lab.local -U Administrator`

After joining:

- Computer object is created in Active Directory
- Kerberos keytab is generated
- SSSD configuration is automatically created


## 7. SSSD Configuration

### 7.1 Configuration File

SSSD configuration is stored at:

`/etc/sssd/sssd.conf`

Key configuration settings include:

- AD provider enabled
- Kerberos authentication
- Home directory auto-creation
- Fully Qualified Domain Names (FQDN) disabled for usability
- Permissions are restricted

### 7.2 Enable Home Directory Creation

Ensure the following service is enabled:

`sudo systemctl enable --now oddjobd`

This allows automatic home directory creation on first login.


## 8. Access Control

### 8.1 Allowing Domain Users

Limit login access to specific AD groups:

`sudo realm permit -g IT-Admins`

This ensures:

- Only authorized users can log in
- Linux servers are not exposed to all domain users

### 8.2 Sudo Access

Grant administrative privileges via group membership:

`sudo visudo`

Example configuration:

`%IT-Admins ALL=(ALL) ALL`

This avoids assigning sudo access to individual users.


## 9. Authentication Testing

Tests performed:

- SSH login using AD credentials
- Console login using AD credentials
- Home directory auto-creation
- Sudo access validation
- Kerberos ticket verification

Example Kerberos test:

`klist`


## 10. Integration with Active Directory

From Active Directory:

- Ubuntu server appears as a computer object
- Authentication events are logged
- Access is controlled centrally via groups

This provides unified identity management across platforms.

## 11. Security Considerations

- No local user accounts used for daily access
- Access restricted via AD security groups
- Kerberos-based authentication
- Centralized logging of authentication events
- Linux server isolated in Server VLAN


## 12. Use Cases

This integration supports:

- Linux application servers
- Internal tools and dashboards
- Script execution using domain credentials
- Future automation using Ansible or SSH with AD users

