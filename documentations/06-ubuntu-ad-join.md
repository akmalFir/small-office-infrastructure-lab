# 06-ubuntu-ad-join.md

## Joining Ubuntu to Active Directory

### Install Required Packages

```bash
sudo apt update
sudo apt install realmd sssd sssd-tools krb5-user adcli samba-common samba-common-bin oddjob oddjob-mkhomedir packagekit
```

### Discover Domain

```bash
sudo realm discover lab.local
```

### Join Domain

```bash
sudo realm join -U administrator lab.local
```

### SSSD Configuration Snippet

```ini
[sssd]
services = nss, pam
config_file_version = 2
domains = lab.local

[domain/lab.local]
ad_domain = lab.local
krb5_realm = LAB.LOCAL
realmd_tags = manages-system joined-with-samba
fallback_homedir = /home/%u
default_shell = /bin/bash
```

### Verification

```bash
id administrator@lab.local
getent passwd labuser
```

### Troubleshooting Notes

* Kerberos authentication may fail if system clocks are not synced.
* DNS must resolve the AD domain correctly.
* If `realm discover` fails, verify SRV records exist in the AD DNS zone.
