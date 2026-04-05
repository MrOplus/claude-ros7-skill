# claude-ros7-skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin that provides expert-level MikroTik RouterOS 7 CLI knowledge. When activated, Claude becomes a RouterOS 7 specialist capable of generating accurate configurations, troubleshooting issues, and explaining command syntax.

## What It Does

- Generates correct **RouterOS 7 CLI commands** using the modern slash-separated syntax (`/ip/firewall/filter` not `/ip firewall filter`)
- Covers **350+ command paths** across all RouterOS 7 menus
- Includes detailed **property tables** with types, defaults, and descriptions
- Warns about common **ROS6 to ROS7 migration pitfalls**
- Provides **complete configuration examples** for firewalls, VPNs, VLANs, routing, QoS, and more

## Coverage

| Domain | Menu Paths | Reference |
|--------|-----------|-----------|
| Interfaces | `/interface` -- ethernet, bridge, VLAN, bonding, WiFi, wireless, LTE, dot1x, MACsec | `interface.md` |
| IPv4 | `/ip` -- addressing, DNS, DHCP, pools, ARP, services, hotspot, proxy, VRF | `ip.md` |
| Firewall | `/ip/firewall` -- filter, NAT, mangle, raw, connection tracking, address lists, 30+ matchers | `firewall.md` |
| Routing | `/routing` -- BGP, OSPF, RIP, routing filters, BFD, IGMP, PIM-SM, RPKI | `routing.md` |
| System | `/system` -- identity, clock, NTP, scheduler, scripts, logging, health, packages, license | `system.md` |
| VPN/Tunnels | IPsec, WireGuard, L2TP, PPTP, SSTP, OpenVPN, GRE, EoIP, IPIP, VXLAN | `vpn.md` |
| QoS | `/queue` -- simple, tree, PCQ, CoDel, FQ-CoDel, CAKE, SFQ, RED | `queues.md` |
| Tools | `/tool` -- ping, traceroute, torch, bandwidth-test, netwatch, sniffer, fetch, email | `tools.md` |
| Scripting | Data types, operators, 40+ global commands, control structures, common patterns | `scripting.md` |
| Extras | PPP, certificates, users, SNMP, RADIUS, MPLS, containers, IoT, ZeroTier, User Manager | `extras.md` |

## Installation

### From GitHub

```bash
claude /plugin marketplace add MrOplus/claude-ros7-skill
claude /plugin install
claude /reload-plugins
```

### Manual

Clone this repo and install locally:

```bash
git clone https://github.com/MrOplus/claude-ros7-skill.git
cd claude-ros7-skill
claude /plugin install .
claude /reload-plugins
```

## Usage

Once installed, the skill activates automatically when you mention anything related to MikroTik or RouterOS. Examples:

```
> Set up a basic firewall with NAT masquerade for a home router

> Configure WireGuard VPN between two MikroTik routers

> Set up VLAN trunking with bridge VLAN filtering on a CRS326

> Write a script that backs up the config daily and emails it

> What's the difference between /queue/simple and /queue/tree?

> How do I configure OSPF between two routers in ROS7?
```

## Skill Structure

```
claude-ros7-skill/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── skills/
│   └── ros7skill/
│       ├── SKILL.md          # Main skill (283 lines)
│       └── references/       # Detailed reference docs
│           ├── interface.md   # Ethernet, Bridge, VLAN, WiFi
│           ├── ip.md          # Addressing, DNS, DHCP, Services
│           ├── firewall.md    # Filter, NAT, Mangle, Raw
│           ├── routing.md     # BGP, OSPF, RIP, Filters
│           ├── system.md      # Clock, NTP, Logging, Health
│           ├── vpn.md         # IPsec, WireGuard, L2TP, OpenVPN
│           ├── queues.md      # Simple, Tree, CAKE, FQ-CoDel
│           ├── tools.md       # Ping, Netwatch, Sniffer, Torch
│           ├── scripting.md   # Language reference
│           └── extras.md      # PPP, Certs, SNMP, MPLS, IoT
├── LICENSE
└── README.md
```

## Key ROS7 Changes the Skill Knows About

- Routing protocols restructured (BGP/OSPF/RIP use instances + templates)
- WiFi package renamed from `/interface/wireless` to `/interface/wifi`
- CAPsMAN moved to `/interface/wifi/capsman`
- Bridge `ingress-filtering=yes` is now the default
- Firewall NAT has new `input` and `output` chains
- Routing filters use script-like syntax (not regex)
- Ethernet link modes changed in v7.12+
- WireGuard is native (new in ROS7)
- CAKE and FQ-CoDel queue disciplines added
- DNS adlist (ad-blocking) added in v7.15

## Data Sources

All command references are sourced from the official MikroTik documentation at [help.mikrotik.com](https://help.mikrotik.com/docs/spaces/ROS/pages/328059/RouterOS).

## License

MIT
