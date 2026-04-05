# /system -- System Administration Reference

## Table of Contents
1. [Identity](#identity)
2. [Clock and NTP](#clock-and-ntp)
3. [Scheduler](#scheduler)
4. [Script](#script)
5. [Resource](#resource)
6. [RouterBOARD](#routerboard)
7. [Package](#package)
8. [License](#license)
9. [Logging](#logging)
10. [Health](#health)
11. [Other System Menus](#other-system-menus)

---

## Identity

**Menu:** `/system/identity`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string (max 64) | MikroTik | Router hostname; used in DHCP, ND, wireless SSID |

Also modifiable via SNMP OID `1.3.6.1.2.1.1.5.0`.

---

## Clock and NTP

### /system/clock

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| time | HH:MM:SS | current | System time |
| date | mmm/DD/YYYY | jan/02/1970 | System date |
| time-zone-name | string | manual | Timezone identifier |
| time-zone-autodetect | yes\|no | yes | Auto-detect timezone |

**Sub-menu:** `/system/clock/manual` (when time-zone-name=manual)

| Property | Type | Description |
|----------|------|-------------|
| time-zone | [+\|-]HH:MM | GMT offset |
| dst-delta | [+\|-]HH:MM | DST additional offset |
| dst-start / dst-end | datetime | DST period |

### /system/ntp/client

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | no | Activate NTP client |
| mode | broadcast\|manycast\|multicast\|unicast | -- | Operating mode |
| vrf | string | main | VRF |

### /system/ntp/client/servers

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IPv4\|IPv6\|FQDN | -- | Server address |
| min-poll | integer [3-17] | 6 | Min poll interval (2^n seconds) |
| max-poll | integer [3-17] | 10 | Max poll interval |
| iburst | yes\|no | yes | Rapid burst at startup |

### /system/ntp/server

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | no | Activate NTP server |
| broadcast | yes\|no | no | Broadcast mode |
| use-local-clock | yes\|no | no | Use system time when no external source |

---

## Scheduler

**Menu:** `/system/scheduler`
**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | name | -- | Task name |
| on-event | name | -- | Script name to execute |
| start-date | date | -- | Date for initial execution |
| start-time | time | -- | Time for initial execution ("startup" for boot) |
| interval | time | 0s | Repeat interval; 0 = once only |
| run-count | integer | (read-only) | Execution counter |

When start-time=startup with interval=0, script runs once per boot.

---

## Script

**Menu:** `/system/script`
**Commands:** `add`, `set`, `remove`, `run`, `get`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | Script[num] | Script identifier |
| source | string | -- | Script source code |
| policy | string | ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon | Permissions |
| dont-require-permissions | yes\|no | no | Bypass permission checks |

**Sub-menus:**
- `/system/script/environment` -- Script variables (name, value, user)
- `/system/script/job` -- Running scripts (read-only: owner, policy, started)

---

## Resource

**Menu:** `/system/resource` (read-only)

| Property | Type | Description |
|----------|------|-------------|
| uptime | time | Time since boot |
| version | string | RouterOS version |
| free-memory / total-memory | string | RAM usage |
| cpu | string | CPU model |
| cpu-count | integer | Number of CPUs |
| cpu-frequency | string | Current frequency |
| cpu-load | percent | CPU usage % |
| free-hdd-space / total-hdd-space | string | Storage usage |
| architecture-name | string | CPU architecture |
| board-name | string | RouterBOARD model |

**Sub-menus:**
- `/system/resource/cpu` -- Per-core load
- `/system/resource/irq` -- IRQ management and RPS
- `/system/resource/hardware` -- PCI/USB/SCSI devices
- `/system/resource/hardware/usb-settings` -- USB authorization
- `/system/resource/hardware/usb-power-reset` -- USB power cycling

---

## RouterBOARD

**Menu:** `/system/routerboard`

**Read-only:** current-firmware, factory-firmware, model, serial-number, upgrade-firmware

**Command:** `upgrade` -- Upgrade firmware

### /system/routerboard/settings

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| auto-upgrade | yes\|no | no | Auto upgrade firmware after RouterOS update |
| baud-rate | integer | 115200 | Serial port speed |
| boot-delay | time | 2s | Boot keystroke timeout |
| boot-device | enum | nand-if-fail-then-ethernet | Boot source |
| boot-os | router-os\|swos | router-os | OS selection (CRS3xx) |
| cpu-frequency | varies | model-dependent | CPU frequency |
| cpu-mode | power-save\|regular | power-save | CPU power mode |
| enable-jumper-reset | yes\|no | yes | Allow jumper reset |
| protected-routerboot | enabled\|disabled | disabled | Disable etherboot |
| silent-boot | yes\|no | no | Disable boot beep |

### /system/routerboard/mode-button

| Property | Type | Description |
|----------|------|-------------|
| enabled | yes\|no | Enable button |
| hold-time | time interval | Activation hold duration |
| on-event | string | Script to execute |

Same structure for `/system/routerboard/reset-button` and `/system/routerboard/wps-button`.

---

## Package

**Menu:** `/system/package`
**Commands:** `disable`, `enable`, `uninstall`, `print`

### /system/package/update
- `install` -- Upgrade RouterOS
- `install ignore-missing` -- Upgrade main package only

### /system/package/local-update
- `download-all`, `download`, `refresh`

Auto-install: Files named `*.auto.npk` auto-install on FTP/SFTP upload.

---

## License

**Menu:** `/system/license`
**Commands:** `print`, `renew`, `generate-new-id`

| Level | Type | Description |
|-------|------|-------------|
| 0 | Trial (24h) | Full features, expires in 24 hours |
| 1 | Free Demo | Limited tunnels |
| 3 | WISP CPE | Wireless AP mode |
| 4-5 | WISP | Enhanced/Higher limits |
| 6 | Controller | Unlimited |

**ROS7 change:** Demo license blocks version upgrades from v7.8.

---

## Logging

**Menu:** `/system/logging`
**Commands:** `add`, `set`, `remove`, `print`

### Rules

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| action | name | memory | References configured action |
| prefix | string | (empty) | Text prepended to messages |
| regex | string | (empty) | Pattern filter |
| topics | list | info | Message categories (supports `!` exclusion) |

### Actions (`/system/logging/action`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| target | disk\|echo\|email\|memory\|remote | memory | Storage destination |
| memory-lines | integer | 1000 | RAM buffer capacity |
| disk-file-name | string | log | Output filename prefix |
| disk-lines-per-file | integer | 100 | Lines per file |
| remote-port | IP[:Port] | 0.0.0.0:514 | Syslog endpoint |
| remote-protocol | tcp\|udp\|tls | udp | Transport |
| remote-log-format | cef\|default\|syslog | default | Message format |
| syslog-facility | RFC 3164 values | daemon | Facility code |
| syslog-time-format | bsd-syslog\|iso8601 | bsd-syslog | Timestamp format |
| vrf | name | main | VRF instance (v7.19+) |

**Log viewing:** `/log print`, `/log print follow`, `/log print where [condition]`
**Clear buffer:** `/system/logging/action/clear action=<name>` (v7.20+)

**ROS7 enhancements:** CEF format (v7.18), millisecond timestamps (v7.18), VRF support (v7.19), separate memory buffers (v7.20)

**Common topics:** account, bgp, bridge, certificate, dhcp, dns, error, firewall, health, hotspot, info, interface, ipsec, ntp, ospf, ppp, route, script, system, warning, watchdog, web-proxy, wireguard, wireless

---

## Health

**Menu:** `/system/health`

Displays voltage, temperature, fan speed, current, power (hardware-dependent).

### /system/health/settings (fan control)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| fan-full-speed-temp | integer | 65 | Max fan temp |
| fan-target-temp | integer | 58 | Target temp |
| fan-min-speed-percent | integer [0..100] | 12 | Min fan speed % (v7.14+) |
| cpu-overtemp-check | yes\|no | no | CPU overtemp monitoring |
| cpu-overtemp-threshold | integer | 105 | Max CPU temp |

---

## Other System Menus

| Menu | Description |
|------|-------------|
| `/system/watchdog` | Auto-reboot on hang or ping failure |
| `/system/ups` | APC UPS monitoring (requires ups package) |
| `/system/leds` | LED behavior configuration |
| `/system/leds/settings` | Global LED settings |
| `/system/note` | Login message of the day |
| `/system/history` | Undo/redo action history |
| `/system/backup` | Binary backup (.backup): save, load |
| `/system/backup/cloud` | Cloud backup settings |
| `/system/reset-configuration` | Factory reset (keep-users, no-defaults, skip-backup, run-after-reset, caps-mode) |
| `/system/reboot` | Reboot router |
| `/system/shutdown` | Shut down router |
| `/system/device-mode` | Feature restrictions |
| `/system/auto-upgrade` | Automatic upgrade settings |
| `/system/swos` | SwOS settings (applicable switches) |
