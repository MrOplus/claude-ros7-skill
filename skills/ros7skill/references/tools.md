# /tool -- Diagnostics and Utilities Reference

---

## /tool/ping (alias: /ping)

| Parameter | Type | Description |
|-----------|------|-------------|
| address | IP | Target host (required) |
| count | integer | Number of requests |
| size | integer | Payload size |
| ttl | integer | Time to Live |
| interval | time | Time between pings |
| src-address | IP | Source address |
| interface | name | Network interface |
| dscp | integer | DSCP marking |
| routing-table | string | Routing table |
| arp-ping | flag | ARP-based ping |

**MAC Ping:** `/tool/mac-server/ping` (enabled: yes\|no)

---

## /tool/traceroute

| Parameter | Type | Description |
|-----------|------|-------------|
| address | IP | Target host (required) |

Uses TTL/ICMP to map network paths.

---

## /tool/bandwidth-test

### Server (`/tool/bandwidth-server`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| allocate-udp-ports-from | integer | 2000 | UDP port range start |
| authenticate | yes\|no | yes | Require auth |
| enabled | yes\|no | yes | Enable server |
| max-sessions | integer [1-1000] | 100 | Max tests |

### Client

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IP\|IPv6 | -- | Target |
| connection-count | integer [1-255] | -- | TCP connections |
| direction | both\|receive\|transmit | receive | Direction |
| duration | time | -- | Duration |
| interval | time [20ms-5s] | 1s | Report interval |
| local-tx-speed | integer | -- | Max TX (bps) |
| remote-tx-speed | integer | -- | Max RX (bps) |
| protocol | udp\|tcp | udp | Protocol |
| random-data | yes\|no | no | Incompressible data |
| user / password | string | -- | Auth |

---

## /tool/netwatch

**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| host | string | "" | IP/domain to probe |
| type | icmp\|tcp-conn\|http-get\|https-get\|dns\|simple | simple | Probe method |
| interval | time | 10s | Probe interval |
| timeout | time | 3s | Response timeout |
| src-address | string | "" | Source IP |
| start-delay | time | 3s | Initial delay |
| startup-delay | time | 5m | Boot delay |
| up-script | string | "" | Script on recovery |
| down-script | string | "" | Script on failure |
| test-script | string | "" | Script after every probe |

### Probe-specific options

**ICMP:** packet-interval, packet-count, packet-size, thr-max, thr-avg, thr-stdev, thr-jitter, thr-loss-percent, thr-loss-count, ttl, accept-icmp-time-exceeded

**TCP-CONNECT:** port (80), thr-tcp-conn-time

**HTTP-GET:** port (80), thr-http-time (10s), http-code-min (100), http-code-max (299)

**HTTPS-GET:** port (443), certificate, check-certificate, thr-http-time, http-code-min, http-code-max

**DNS:** record-type (A\|AAAA\|MX\|NS), dns-server

**ROS7 change:** Multiple probe types added in v7.4 (previously only simple ICMP).

---

## /tool/torch

Real-time traffic monitor by interface. Classification:
- Source/destination address (IPv4, IPv6)
- Port, Protocol, MAC-protocol, VLAN ID, MAC-address, DSCP

Shows traffic before firewall. Hardware-offloaded packets may not appear.

---

## /tool/profile

| Parameter | Type | Description |
|-----------|------|-------------|
| cpu | integer\|total\|all | CPU core selection |

Displays per-process CPU usage. 80+ service classifiers.

---

## /tool/sniffer

**Commands:** `start`, `stop`, `save`, `quick`, `protocol`, `host`, `connection`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| file-limit | integer [10..4GB KiB] | 1000KiB | Max file size |
| file-name | string | (empty) | Output file |
| filter-interface | name\|all | all | Interface |
| filter-ip-address | ip/mask (max 16) | (empty) | IP filter |
| filter-port | port (max 16) | (empty) | Port filter |
| filter-ip-protocol | protocol (max 16) | (empty) | Protocol filter |
| filter-direction | any\|rx\|tx | (empty) | Direction |
| filter-size | integer range | (empty) | Packet size |
| filter-vlan | integer [0-4095] (max 16) | (empty) | VLAN filter |
| memory-limit | integer | 100KiB | Buffer memory |
| memory-scroll | yes\|no | yes | Overwrite old data |
| only-headers | yes\|no | no | Headers only |
| streaming-enabled | yes\|no | no | TZSP streaming |
| streaming-server | IP | 0.0.0.0 | TZSP receiver |

---

## /tool/email

SMTP email configuration for scripts and alerts.

---

## /tool/fetch

HTTP/FTP download and upload utility.

```routeros
/tool/fetch url="https://example.com/file.txt" mode=https dst-path=file.txt
/tool/fetch url="https://api.example.com" http-method=post http-data="key=value"
```

---

## /tool/graphing

Built-in SNMP graphing:
- `/tool/graphing/interface` -- Interface traffic graphs
- `/tool/graphing/queue` -- Queue graphs
- `/tool/graphing/resource` -- System resource graphs

---

## /tool/romon

Router Management Overlay Network -- manage remote devices through intermediate MikroTik routers.

- `/tool/romon` -- RoMON settings
- `/tool/romon/port` -- Port configuration

---

## /tool/mac-server

- `/tool/mac-server` -- MAC-Telnet server
- `/tool/mac-server/mac-winbox` -- MAC-based WinBox access
- `/tool/mac-server/ping` -- MAC ping server

---

## Other Tools

| Tool | Description |
|------|-------------|
| `/tool/dns-update` | Dynamic DNS update |
| `/tool/sms` | SMS via LTE modem |
| `/tool/traffic-generator` | Traffic generation |
| `/tool/wol` | Wake-on-LAN |
| `/tool/speed-test` | Internet speed test (Ookla-compatible) |
| `/tool/flood-ping` | High-rate stress test ping |
| `/tool/ip-scan` | IP address scanner |
| `/tool/mac-scan` | MAC address scanner |
