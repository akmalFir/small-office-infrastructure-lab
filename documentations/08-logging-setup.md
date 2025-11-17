## Graylog Logging Setup

### Installation Steps

1. Install MongoDB:

```bash
sudo apt install mongodb
sudo systemctl enable mongodb --now
```

2. Install OpenSearch/Elasticsearch:

```bash
sudo apt install opensearch
sudo systemctl enable opensearch --now
```

3. Install Graylog server:

```bash
wget https://packages.graylog2.org/repo/packages/graylog-4.5-repository_latest.deb
sudo dpkg -i graylog-4.5-repository_latest.deb
sudo apt update
sudo apt install graylog-server
sudo systemctl enable graylog-server --now
```

4. Configure Graylog server with a strong password secret.
5. Create syslog UDP input on Graylog UI for pfSense and Linux logs.

### Verification Steps

* Confirm logs from pfSense and Linux servers appear in Graylog dashboards.
* Use filters to ensure firewall, authentication, and system logs are properly ingested.
* Test alerting rules to confirm notifications are triggered.

### Troubleshooting Notes

* Ensure firewall allows incoming UDP port 514 to Graylog.
* Verify Elasticsearch/OpenSearch is running and reachable.
* Check Graylog server logs for any ingestion errors.
