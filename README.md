# Small Office Infrastructure Lab

A fully virtualized small-office IT environment designed to simulate real-world infrastructure used in SMEs. This project showcases networking, system administration, security hardening, monitoring, and documentation skills relevant to IT Infrastructure, NOC, and SysAdmin roles.

---

## 📌 Project Summary

This lab simulates a 20-user company with multiple departments, a segmented network, centralized identity management, secure file sharing, monitoring, and logging. Everything is built using open-source and enterprise tools:

- pfSense for firewalling and VLAN segmentation
- Windows Server 2022 for Active Directory, DNS, DHCP, and GPO
- Windows Server 2022 for file services
- Ubuntu Server for Linux workloads
- Zabbix/Nagios for monitoring
- Graylog/Wazuh for centralized logging

This mirrors how a real small-to-medium company operates.

---

## 📡 Network Architecture

The network is segmented into multiple VLANs following least-privilege principles:

| VLAN | Name        | Subnet         | Purpose                              |
|------|-------------|----------------|--------------------------------------|
| 10   | Management  | 10.10.10.0/24  | Admin and server management          |
| 20   | Staff       | 10.10.20.0/24  | General employee devices             |
| 30   | Finance     | 10.10.30.0/24  | Restricted finance users             |
| 40   | Servers     | 10.10.40.0/24  | Domain controller, file server, etc. |
| 50   | Guest       | 10.10.50.0/24  | Internet-only guest network          |

A full network diagram is included in the `diagrams/` folder.

---

## 🧩 Components

### 1. pfSense Firewall
- VLAN trunking
- DHCP (or relay)
- Firewall segmentation
- DNS forwarding
- WireGuard VPN
- Static routes and aliases

### 2. Windows Server 2022 — Domain Controller
- Active Directory Domain Services
- DNS
- DHCP (optional)
- Organizational Units
- Group Policies
- Password policies
- Logon scripts

### 3. Windows Server 2022 — File Server
- Department shares
- NTFS permissions
- Home directories
- Access-based enumeration

### 4. Ubuntu Server
- AD domain join
- Kerberos + SSSD authentication
- Internal services (wiki, dashboard, or utility apps)

### 5. Monitoring (Zabbix or Nagios)
- CPU, RAM, disk tracking
- Service uptime (AD, DNS, SMB, HTTP)
- Network latency and packet loss
- Email/Telegram alerting

### 6. Logging (Graylog or Wazuh)
- pfSense syslogs
- Windows event logs
- Linux audit logs
- Security dashboards and alerts

---

## 📁 Documentation

All setup guides and configs are stored in the `documentation/` folder:

- 01 — Project Overview  
- 02 — Network Architecture  
- 03 — pfSense Setup  
- 04 — Active Directory Setup  
- 05 — File Server Setup  
- 06 — Ubuntu AD Join  
- 07 — Monitoring Setup  
- 08 — Logging Setup  
- 09 — Backup Procedures  
- 10 — Security Controls  

---

## 📸 Screenshots

Screenshots of major components (AD, GPO, pfSense, monitoring, logging) are stored in the `screenshots/` folder.

---

## 🧪 Skills Demonstrated

This lab highlights practical skills useful for infra and sysadmin roles:

- VLAN and subnet design
- Identity-based access control
- Windows and Linux server administration
- Group Policy management
- File sharing permissions (NTFS and Linux)
- Firewall rules and segmentation
- Monitoring and alerting
- Log centralization
- Backup strategy design
- Infrastructure documentation
- Troubleshooting and hardening

---

## 🎯 Purpose

The goal of this lab is to demonstrate real-world IT infrastructure skills that employers look for in junior SysAdmin, Infrastructure, and NOC positions.

---

## 🚀 Future Enhancements

- pfSense high availability (CARP)
- Automated user provisioning
- Additional PowerShell and Bash automation
- Config baseline with Ansible or DSC
- Expanded monitoring templates

---

