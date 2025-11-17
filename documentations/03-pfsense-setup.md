## pfSense Setup

### Installation & Interface Configuration

1. Install pfSense VM.
2. Configure WAN interface (DHCP or static).
3. Create VLAN interfaces for all segments:

```text
Interfaces -> Assignments -> VLANs -> Add VLAN
```

4. Assign static IP addresses per VLAN.
5. Enable interfaces and configure gateway.

### DHCP Configuration Example (Users VLAN)

```text
Range: 10.10.30.50-200
DNS: 10.10.20.10 (AD DC)
Gateway: 10.10.30.1
```

### Firewall Rules Example

```text
Allow DNS (53) → DC01
Allow SMB (445) → FS01
Allow HTTP/HTTPS → Internet
Block all unnecessary traffic to other VLANs
```

### NAT & VPN (Optional)

* Configure outbound NAT for Internet access.
* Set up OpenVPN server for remote admin access if required.

### Syslog Forwarding

```text
Status -> System Logs -> Settings -> Remote Logging
Target IP: 10.10.50.10 (Graylog)
Port: 514 UDP
```

### Verification Steps

* Ping between VLANs according to allowed rules.
* Check DHCP lease distribution.
* Confirm firewall rules are applied in pfSense status.
* Test VPN connection if configured.

### Troubleshooting Notes

* Check VLAN tagging if inter-VLAN routing fails.
* Confirm DHCP ranges do not overlap.
* Review firewall logs for blocked packets.
* Ensure WAN connectivity if Internet access fails.

