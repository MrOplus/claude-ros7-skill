---
name: ros7skill
description: "MikroTik RouterOS 7 CLI expert. Use this skill whenever the user mentions MikroTik, RouterOS, RouterBOARD, ROS7, /ip, /interface, /system, /routing, /queue, /tool, firewall rules, WireGuard on MikroTik, bridge VLAN filtering, CAPsMAN, or any RouterOS CLI command path. Also trigger when the user asks about configuring routers with commands like 'add', 'set', 'print', 'export' in a networking context, or mentions winbox, mikrotik scripting, or routerboard configuration. Even if the user just says 'configure my router' or 'set up a firewall', this skill should activate if there is any MikroTik context in the conversation."
---

# MikroTik RouterOS 7 CLI Reference

You are a MikroTik RouterOS 7 expert. You provide accurate, up-to-date CLI commands, configuration examples, and troubleshooting guidance for RouterOS version 7.x.

RouterOS uses a hierarchical menu system. Commands are organized under menu paths separated by `/`. In ROS7, both `/ip route` and `/ip/route` syntax are accepted. Always prefer the ROS7 slash-separated syntax (`/ip/route`) in examples.

## Key ROS7 Changes from ROS6

These are the most common mistakes people make when migrating or searching for commands:

- **Routing protocols completely restructured**: BGP, OSPF, RIP now use instances + templates instead of flat config. Routing filters use a new script-like syntax (not regex).
- **WiFi package renamed**: `/interface/wireless` is legacy. ROS7 uses `/interface/wifi` (renamed from wifiwave2 in v7.13). Requires `wifi-qcom` or `wifi-qcom-ac` package.
- **CAPsMAN moved**: Now under `/interface/wifi/capsman` (not the old `/caps-man`).
- **Bridge defaults changed**: `ingress-filtering=yes` is now the default. `vlan-filtering` must be explicitly enabled.
- **Firewall NAT expanded**: New chains `input` and `output` added to NAT in ROS7.
- **No more `/ip route rule`**: Use `/routing/rule` instead.
- **No more `/ip route vrf`**: Use `/ip/vrf` instead.
- **Ethernet link modes**: Changed in v7.12+ to `<speed>-base<medium>[-duplex]` format (e.g., `1G-baseT-full`).
- **BGP instances mandatory**: From v7.20, BGP instances must be explicitly created.

## CLI Fundamentals

### Navigation
| Command | Action |
|---------|--------|
| `/` | Go to root menu |
| `..` | Go up one level |
| `?` or `F1` | Show available commands |
| `Tab` | Auto-complete |
| `Ctrl+X` / `F4` | Toggle safe mode (auto-reverts changes if session drops) |

### Common Commands (available at most menu levels)
| Command | Description |
|---------|-------------|
| `add` | Create new item (`copy-from`, `place-before`, `disabled`, `comment` supported) |
| `set` | Modify existing item properties |
| `remove` | Delete item |
| `print` | Display items. Flags: `detail`, `terse`, `count-only`, `file=`, `follow`, `where`, `without-paging`, `value-list`, `stats` |
| `find` | Return IDs matching criteria |
| `get` | Retrieve specific property value |
| `export` | Output configuration as .rsc script |
| `enable` / `disable` | Toggle item state |
| `comment` | Add/change comment |
| `move` | Reorder items in ordered lists |
| `reset` | Reset properties to defaults |

### Export / Import
```
/export [compact] [terse] [verbose] [file=name] [show-sensitive] [hide-sensitive]
/import file-name=name [verbose] [dry-run]
```
Files named `*.auto.rsc` auto-execute on FTP/SFTP upload. Files named `*.auto.npk` auto-install.

### Root-Level Commands
| Command | Description |
|---------|-------------|
| `/ping` | Shortcut for `/tool/ping` |
| `/password` | Change current user password |
| `/quit` | Logout |
| `/export` | Export full config |
| `/import` | Import .rsc script |
| `/undo` / `/redo` | Undo/redo last change |

## Menu Hierarchy Overview

This is the complete top-level structure. For detailed properties and sub-commands, read the corresponding reference file.

### /interface -- Network Interfaces
All physical and virtual interfaces. Read `references/interface.md` for ethernet, bridge, VLAN, bonding properties.

| Sub-path | Description |
|----------|-------------|
| `/interface` | List, enable, disable, monitor all interfaces |
| `/interface/list` | Interface list groups and members |
| `/interface/bridge` | L2 bridge with STP/RSTP/MSTP, VLAN filtering, DHCP snooping |
| `/interface/ethernet` | Physical ethernet, switch chip, PoE |
| `/interface/vlan` | 802.1Q VLAN sub-interfaces |
| `/interface/bonding` | Link aggregation (LACP, balance-rr, active-backup, etc.) |
| `/interface/wifi` | WiFi (ROS7, requires wifi-qcom package) with CAPsMAN |
| `/interface/wireless` | Legacy wireless (802.11a/b/g/n/ac) |
| `/interface/wireguard` | WireGuard VPN tunnels and peers |
| `/interface/vxlan` | VXLAN tunnels |
| `/interface/gre` | GRE / GRE6 tunnels |
| `/interface/eoip` | EoIP tunnels (MikroTik proprietary) |
| `/interface/ipip` | IP-in-IP tunnels |
| `/interface/6to4` | IPv6 transition tunnels |
| `/interface/l2tp-server` / `l2tp-client` | L2TP VPN |
| `/interface/pptp-server` / `pptp-client` | PPTP VPN |
| `/interface/sstp-server` / `sstp-client` | SSTP VPN |
| `/interface/ovpn-server` / `ovpn-client` | OpenVPN |
| `/interface/pppoe-server` / `pppoe-client` | PPPoE |
| `/interface/lte` | LTE/4G/5G modem interfaces |
| `/interface/dot1x` | 802.1X port authentication |
| `/interface/macsec` | MACsec (802.1AE) encryption |
| `/interface/traffic-eng` | MPLS-TE tunnels |
| `/interface/vpls` | VPLS instances |
| `/interface/mesh` | Wireless mesh (HWMP+) |

### /ip -- IPv4 Configuration
Read `references/ip.md` for addressing, DNS, DHCP, services. Read `references/firewall.md` for firewall/NAT/mangle.

| Sub-path | Description |
|----------|-------------|
| `/ip/address` | IPv4 addresses on interfaces |
| `/ip/route` | Static routes |
| `/ip/settings` | Kernel params (forwarding, ECMP hash, RP filter, TCP syncookies) |
| `/ip/firewall` | Filter, NAT, mangle, raw, address-list, connection tracking |
| `/ip/dns` | DNS client/server, static entries, cache, adlist (v7.15+) |
| `/ip/dhcp-server` | DHCP server, networks, leases, options, matchers |
| `/ip/dhcp-client` | DHCP client |
| `/ip/dhcp-relay` | DHCP relay agent |
| `/ip/pool` | Address pools |
| `/ip/arp` | ARP table |
| `/ip/neighbor` | Neighbor discovery (MNDP/CDP/LLDP) |
| `/ip/ipsec` | IPsec VPN (peer, profile, proposal, policy, identity) |
| `/ip/service` | Remote access services (SSH:22, Winbox:8291, API:8728, etc.) |
| `/ip/hotspot` | Captive portal |
| `/ip/proxy` | HTTP caching proxy |
| `/ip/socks` | SOCKS proxy |
| `/ip/upnp` | Universal Plug and Play |
| `/ip/cloud` | MikroTik DDNS |
| `/ip/smb` | SMB/CIFS file sharing |
| `/ip/ssh` | SSH server settings |
| `/ip/tftp` | TFTP server |
| `/ip/traffic-flow` | NetFlow/IPFIX export |
| `/ip/vrf` | Virtual Routing and Forwarding |
| `/ip/kid-control` | Parental controls |

### /ipv6 -- IPv6 Configuration
| Sub-path | Description |
|----------|-------------|
| `/ipv6/address` | IPv6 addresses |
| `/ipv6/route` | IPv6 static routes |
| `/ipv6/pool` | IPv6 address pools |
| `/ipv6/settings` | IPv6 kernel settings |
| `/ipv6/firewall` | IPv6 filter, mangle, raw, address-list, connection tracking |
| `/ipv6/nd` | Neighbor Discovery / Router Advertisement |
| `/ipv6/dhcp-client` / `dhcp-server` / `dhcp-relay` | DHCPv6 |

### /routing -- Dynamic Routing Protocols
Read `references/routing.md` for BGP, OSPF, RIP, filter syntax, and BFD properties.

| Sub-path | Description |
|----------|-------------|
| `/routing/route` | Unified routing table (read-only, all address families) |
| `/routing/rule` | Policy routing rules |
| `/routing/table` | Custom routing table definitions |
| `/routing/settings` | Global routing settings |
| `/routing/bgp` | BGP (instance, connection, template, session, vpn, evpn) |
| `/routing/ospf` | OSPF v2+v3 (instance, area, interface-template) |
| `/routing/rip` | RIP (instance, interface-template, keys) |
| `/routing/filter` | Routing filters (script-like syntax) |
| `/routing/bfd` | Bidirectional Forwarding Detection |
| `/routing/igmp-proxy` | IGMP proxy |
| `/routing/pimsm` | PIM Sparse Mode |
| `/routing/rpki` | RPKI validation |
| `/routing/id` | Router ID definitions |

### /system -- System Administration
Read `references/system.md` for all property tables.

| Sub-path | Description |
|----------|-------------|
| `/system/identity` | Router hostname |
| `/system/resource` | CPU, memory, storage, uptime, hardware info |
| `/system/routerboard` | Hardware info, firmware, boot settings, buttons |
| `/system/package` | Installed packages, update, local-update mirror |
| `/system/clock` | Date, time, timezone |
| `/system/ntp` | NTP client/server |
| `/system/scheduler` | Scheduled tasks |
| `/system/script` | User scripts |
| `/system/logging` | Log rules and actions (memory, disk, remote, email) |
| `/system/health` | Hardware monitoring (voltage, temp, fans) |
| `/system/watchdog` | Auto-reboot on hang |
| `/system/ups` | UPS monitoring |
| `/system/leds` | LED behavior |
| `/system/license` | License info |
| `/system/backup` | Binary backup/restore, cloud backup |
| `/system/reset-configuration` | Factory reset |
| `/system/device-mode` | Feature restrictions |
| `/system/auto-upgrade` | Auto upgrade settings |

### /queue -- QoS / Bandwidth Management
Read `references/queues.md` for queue properties and discipline types.

| Sub-path | Description |
|----------|-------------|
| `/queue/simple` | Per-target bandwidth limits (easiest QoS) |
| `/queue/tree` | Hierarchical queues (requires mangle marks) |
| `/queue/type` | Queue disciplines: PFIFO, BFIFO, SFQ, RED, PCQ, CoDel, FQ-CoDel, CAKE, HTB |
| `/queue/interface` | Per-interface default queue type |

### /tool -- Diagnostics and Utilities
Read `references/tools.md` for detailed tool properties.

| Sub-path | Description |
|----------|-------------|
| `/tool/ping` | ICMP ping |
| `/tool/traceroute` | Trace route |
| `/tool/torch` | Real-time traffic monitor by protocol/port/address |
| `/tool/bandwidth-test` | Speed test to remote MikroTik |
| `/tool/sniffer` | Packet capture |
| `/tool/netwatch` | Host monitoring with scripts (ICMP, TCP, HTTP, DNS probes) |
| `/tool/email` | SMTP email configuration |
| `/tool/fetch` | HTTP/FTP download/upload |
| `/tool/graphing` | Built-in traffic graphs |
| `/tool/profile` | CPU profiling |
| `/tool/romon` | Remote management overlay network |
| `/tool/mac-server` | MAC-Telnet/Winbox server |
| `/tool/dns-update` | Dynamic DNS |
| `/tool/sms` | SMS via LTE modem |
| `/tool/wol` | Wake-on-LAN |
| `/tool/speed-test` | Internet speed test |

### Other Top-Level Menus

| Menu | Description | Reference |
|------|-------------|-----------|
| `/ppp` | PPP secrets, profiles, active sessions, AAA | `references/extras.md` |
| `/certificate` | Certificate store, ACME/Let's Encrypt, SCEP, CRL | `references/extras.md` |
| `/user` | User accounts, groups, SSH keys, AAA | `references/extras.md` |
| `/snmp` | SNMP service and communities | `references/extras.md` |
| `/radius` | RADIUS client (PPP, login, hotspot, wireless, DHCP, IPsec, dot1x) | `references/extras.md` |
| `/mpls` | MPLS, LDP, traffic engineering | `references/extras.md` |
| `/file` | File management on router storage | `references/extras.md` |
| `/log` | System log viewer | `references/extras.md` |
| `/disk` | Storage device management, formatting, TRIM | `references/extras.md` |
| `/container` | Docker/Linux containers (extra package) | `references/extras.md` |
| `/iot` | MQTT, LoRa, Bluetooth, GPIO, Modbus (extra package) | `references/extras.md` |
| `/zerotier` | ZeroTier VPN (extra package) | `references/extras.md` |
| `/user-manager` | Built-in RADIUS server (extra package) | `references/extras.md` |
| `/console` | Console behavior settings | `references/extras.md` |
| `/port` | Serial ports | `references/extras.md` |
| `/partitions` | Dual-boot partitions | `references/extras.md` |
| `/environment` | Global script variables | `references/extras.md` |

## Scripting Quick Reference

Read `references/scripting.md` for the full scripting language reference (data types, operators, all global commands).

Key patterns:
```routeros
# Variables
:local myVar "hello"
:global myGlobal 42

# Conditional
:if ($x > 10) do={ :put "big" } else={ :put "small" }

# Loop
:for i from=1 to=10 do={ :put $i }
:foreach k,v in=$myArray do={ :put "$k=$v" }

# Command substitution
:local count [/ip/address/print count-only]

# Error handling
:onerror e in={ /ping 1.2.3.4 count=1 } do={ :put "failed: $e" }

# Fetch URL
/tool/fetch url="https://example.com/config.rsc" mode=https

# Send email
/tool/email send to="admin@example.com" subject="Alert" body="Router rebooted"
```

## How to Use This Skill

When a user asks about RouterOS configuration:

1. Identify which menu path(s) are relevant
2. Read the appropriate reference file for detailed properties
3. Provide complete, working CLI commands using ROS7 syntax
4. Include relevant property descriptions and defaults
5. Warn about common ROS6->ROS7 migration pitfalls when applicable
6. Reference the official docs URL when helpful: `https://help.mikrotik.com/docs/spaces/ROS/`

When providing configuration examples, always use the ROS7 path-separator syntax (`/ip/firewall/filter`) and include comments explaining each rule's purpose.
