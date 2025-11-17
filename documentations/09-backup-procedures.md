## Backup Strategy

### Linux Backup

* Script archives `/etc`, `/home`, `/var/log` directories.
* Schedule script via cron to run daily:

```bash
0 2 * * * /usr/local/bin/linux-backup.sh
```

* Verify backup files exist and check integrity with `tar -tzf backupfile.tar.gz`.

### Windows Backup

* Perform System State backup via PowerShell:

```powershell
wbadmin start systemstatebackup -backuptarget:D: -quiet
```

* Nightly incremental backups of shared folders using `robocopy`.
* Verify by restoring test files to a separate location.

### File Server Backup

* Use Robocopy to mirror `D:\Shares` to backup disk nightly:

```powershell
robocopy D:\Shares E:\Backup /MIR /R:3 /W:5 /LOG:C:\BackupLogs\robocopy.log
```

* Check logs and verify file integrity.

### Verification Steps

* Confirm backup files exist after scheduled runs.
* Test restoration on a VM to ensure integrity.
* Check log files for errors.

### Troubleshooting Notes

* Ensure scripts have execution permissions.
* Monitor disk space to prevent backup failure.
* Confirm network paths are accessible for remote backups.
