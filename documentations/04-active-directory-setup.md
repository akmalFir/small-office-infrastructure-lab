# 04-active-directory-setup.md

## Active Directory Deployment

### Installation & Promotion

1. Install AD DS role on Windows Server 2022:

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

2. Promote server to Domain Controller:

```powershell
Install-ADDSForest -DomainName "lab.local" -DomainNetbiosName "LAB" -SafeModeAdministratorPassword (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force)
```

3. Configure DNS to host internal zone for lab.local.

### Organizational Units (OU)

```text
LAB.local
 ├── AdminAccounts
 ├── LabUsers
 ├── LabComputers
 ├── Servers
      ├── DomainControllers
      ├── FileServers
      └── LinuxServers
```

### Group Policy Objects (GPOs)

| GPO               | Purpose                                                        |
| ----------------- | -------------------------------------------------------------- |
| PasswordPolicy    | Enforce strong passwords and expiration                        |
| HardeningBaseline | Disable SMBv1, restrict legacy authentication, UAC enforcement |
| DriveMapping      | Map network drives for users                                   |
| WindowsUpdate     | Schedule automated updates                                     |

### Verification

* Run `gpupdate /force` on client machines.
* Use `rsop.msc` or `gpresult /r` to confirm applied policies.
* Test login of domain users.
* Confirm AD objects replication between DCs if multiple.

### Troubleshooting Notes

* DNS misconfiguration may prevent clients from locating domain controllers.
* Time sync issues can cause Kerberos authentication failures.
* Ensure firewall ports 88, 389, 445, 53 are open between DC and clients.
