## Zabbix Monitoring Setup

### Installation Commands

```bash
sudo dnf update
sudo dnf install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent
```

### Host Configuration

* Add all servers and clients to Zabbix.
* Configure agent keys and templates for monitoring.
* Enable host availability checks and metric collection.

### Example Triggers

| Trigger        | Threshold      |
| -------------- | -------------- |
| CPU usage high | >80%           |
| Disk space low | <20%           |
| Service down   | Not responding |

### Verification Steps

* Access Zabbix web frontend.
* Confirm all hosts report metrics.
* Trigger alerts manually to test notifications.

### Troubleshooting Notes

* Ensure firewall allows Zabbix Agent communication (port 10050).
* Verify MySQL service is running for Zabbix server.
* Check agent logs for connection errors.

