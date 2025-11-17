## Security Controls & Hardening

### Firewall Policy

* Block all inbound traffic by default.
* Allow only required service ports (DNS, LDAP, SMB, RDP, SSH).
* Enforce inter-VLAN restrictions to isolate departments.
* Log all denied packets for auditing.

### AD Security Baseline

* Enforce strong password complexity and 90‑day rotation.
* Configure account lockout after 5 failed attempts.
* Disable SMBv1.
* Enforce NTLMv2 only.
* Apply CIS-based baseline GPO for Windows hardening.

### Linux Hardening

* Disable root SSH login.
* Enforce key-based authentication.
* Enable automatic security updates:

```bash
sudo apt install unattended-upgrades
```

* Configure auditd for system monitoring:

```bash
sudo apt install auditd
sudo systemctl enable auditd --now
```

### File Server Permissions

* Enforce least-privilege access.
* Apply NTFS access controls combined with share permissions.
* Restrict “Everyone” group from write access.

### Logging & Monitoring

* Forward system logs to Graylog via UDP 514.
* Enable Zabbix alerts for CPU, RAM, disk, and service monitoring.
* Configure email/SMS notifications for critical incidents.

### Verification Steps

* Confirm GPOs apply using `gpresult /r`.
* Validate SSH login does not allow root.
* Attempt unauthorized share access to verify restrictions.
* Check Zabbix and Graylog dashboards for live metrics and logs.

### Troubleshooting Notes

* Incorrect GPO linking may cause hardening policies to fail.
* Misconfigured file permissions can lock out legitimate users.
* Ensure time synchronization across all machines to avoid Kerberos failures.
