# 05 — File Server Setup

## 1. Overview

This document describes the deployment and configuration of the **Windows File Server** used in the Small Office Infrastructure Lab.

The file server provides:
- Centralized file storage
- Department-based shared folders
- Access control using Active Directory security groups
- User home directories
- Visibility control using Access-Based Enumeration (ABE)

The goal is to ensure **secure, structured, and manageable file access** aligned with enterprise best practices.

## 2. Server Details

| Item        | Value               |
|------------|---------------------|
| OS         | Windows Server 2022 |
| Hostname   | `SRV-FS01`          |
| IP Address | 10.10.10.20         |
| VLAN       | Server (VLAN 10)    |
| Role       | File Services       |

The server uses a **static IP address** and is joined to the Active Directory domain.


## 3. Base Configuration

### 3.1 Initial Setup

Performed before file service configuration:
- Join server to `lab.local` domain
- Apply Windows updates
- Verify DNS resolution
- Confirm domain authentication
- Place server in the `Servers` OU


## 4. File Server Role Installation

### 4.1 Installed Roles and Features

Installed via Server Manager:
- File and Storage Services
- File Server role

Optional features:
- File Server Resource Manager (FSRM)

## 5. Share Design

### 5.1 Share Structure

The file system is organized by **department and function**.

D:
├── Departments
│ ├── IT
│ ├── Staff
│ └── Finance
├── Public
└── Home



### 5.2 Share Types

| Share Name    | Purpose                                |
|--------------|-----------------------------------------|
| `IT$`        | IT department files                     |
| `Staff$`     | General staff files                     |
| `Finance$`   | Restricted finance data                 |
| `Public`     | Read-only shared resources              |
| `Home$`      | User home directories                   |

Hidden shares (`$`) reduce accidental access.


## 6. Active Directory Integration

### 6.1 Security Groups

Access is controlled using **AD security groups**, not individual users.

Examples:
- `FS-IT-RW`
- `FS-Staff-RW`
- `FS-Finance-RW`
- `FS-Public-RO`

This approach:
- Simplifies permission management
- Scales cleanly as users change roles


## 7. NTFS Permission Model

### 7.1 Permission Strategy

- Permissions are assigned at the **folder level**
- Inheritance is controlled and limited
- Users receive access only through group membership

### 7.2 Example Permissions

#### IT Share
| Group        | Permission |
|-------------|------------|
| FS-IT-RW    | Modify     |
| IT-Admins   | Full       |
| Everyone    | None       |

#### Finance Share
| Group            | Permission |
|------------------|------------|
| FS-Finance-RW    | Modify     |
| IT-Admins        | Full       |

### 7.3 Share vs NTFS Permissions

- Share permissions: `Everyone = Full`
- NTFS permissions: **Primary access control**

This avoids permission conflicts and simplifies troubleshooting.


## 8. Access-Based Enumeration (ABE)

ABE is enabled on all departmental shares.

Effect:
- Users only see folders they have access to
- Reduces confusion and information disclosure

Configuration: Server Manager → File and Storage Services → Shares → Advanced Settings


## 9. User Home Directories

### 9.1 Home Folder Configuration

- Each user receives a personal folder:
- The folder is automatically created on first login
- Permissions:
  - User: Full control
  - Administrators: Full control


### 9.2 Drive Mapping

Home drives are mapped using:
- Group Policy
- Logon scripts

Example: H: → Home Directory


## 10. Drive Mapping via Group Policy

### 10.1 GPO Configuration

Drive mappings are applied based on:
- User OU
- Security group membership

Examples:
- Staff → `S:` mapped to `\\SRV-FS01\Staff$`
- Finance → `F:` mapped to `\\SRV-FS01\Finance$`


## 11. Testing and Validation

Tests performed:
- Access validation per department
- Permission inheritance checks
- Unauthorized access attempts
- Home directory creation
- Drive mapping consistency


## 12. Security Considerations

- No direct permissions assigned to individual users
- Administrative access restricted to IT groups
- Sensitive data isolated within Finance shares
- Optional file access auditing enabled





