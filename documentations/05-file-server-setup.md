# 05-file-server-setup.md

## File Server Configuration

### Folder Structure

```text
D:\Shares
 ├── Departments
 │    ├── IT
 │    ├── HR
 │    └── Finance
 └── Public
```

### NTFS Permissions Example (IT Folder)

| Group          | Permission   |
| -------------- | ------------ |
| IT_Staff       | Modify       |
| Domain Users   | None         |
| Administrators | Full Control |

### SMB Share Creation

```powershell
New-SmbShare -Name "IT" -Path "D:\Shares\Departments\IT" -FullAccess "Administrators" -ChangeAccess "LAB\IT_Staff"
```

### Verification Steps

* Access share from a domain user account.
* Confirm permissions by attempting unauthorized access.
* Use `Get-SmbShare` to review share settings.

### Troubleshooting Notes

* Ensure NTFS permissions do not conflict with share permissions.
* Confirm network connectivity to the file server.
* Verify DNS resolution of server name from clients.
* Check Windows Firewall if shares are inaccessible.
