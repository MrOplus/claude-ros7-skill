# /routing -- Dynamic Routing Reference

## Table of Contents
1. [BGP](#bgp)
2. [OSPF](#ospf)
3. [RIP](#rip)
4. [Routing Filters](#routing-filters)
5. [BFD](#bfd)
6. [Multicast (IGMP/PIM)](#multicast)
7. [RPKI](#rpki)
8. [Routing Tables and Rules](#routing-tables-and-rules)

---

## BGP

**ROS7 major change:** Complete rewrite. Uses instance + connection + template model. Instances must be explicitly defined from v7.20.

### /routing/bgp/instance

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Instance identifier |
| as | integer | -- | Autonomous System Number |

### /routing/bgp/connection

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Connection identifier |
| instance | string | -- | Associated BGP instance |
| remote.address | IP | -- | Peer IP address |
| remote.as | integer | -- | Remote AS number |
| local.address | IP | -- | Source address (auto if unset) |
| local.role | ebgp\|ibgp | -- | Connection type |
| router-id | IP | -- | BGP router identifier |
| multihop | yes\|no | -- | Enable multi-hop |
| listen | IP prefix | -- | Accept incoming on subnets |
| template | string | -- | Apply template settings |

### /routing/bgp/template

Contains all BGP protocol-related configuration. Applied to connections via `template=` parameter. Includes:
- Input/output filter chains
- Address families
- Timers (hold-time, keepalive)
- BFD settings
- Route reflection
- Confederation settings
- Graceful restart

### /routing/bgp/session (read-only)

Active BGP sessions. Commands: `clear`, `refresh`, `resend`, `reset`, `stop`

### Additional BGP Sub-menus

| Sub-path | Description |
|----------|-------------|
| `/routing/bgp/vpls` | BGP-based VPLS (RFC 4761) |
| `/routing/bgp/vpn` | L3VPN (VPNv4/VPNv6) |
| `/routing/bgp/evpn` | EVPN configuration |

---

## OSPF

**ROS7 change:** OSPFv2 and OSPFv3 merged. Interface-template replaces per-interface config. No need to define networks explicitly.

### /routing/ospf/instance

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Instance identifier |
| version | 2\|3 | -- | OSPF version |
| router-id | IP | -- | Router identification IP |
| redistribute | list | -- | Protocols to import (static, rip, bgp, connected) |
| originate-default | never\|always\|if-installed | never | Default route handling |
| out-filter-chain | string | -- | Output filter chain |
| out-select-chain | string | -- | Route selection chain |

### /routing/ospf/area

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Area identifier |
| area-id | IP | -- | 32-bit area ID (0.0.0.0 = backbone) |
| instance | string | -- | Associated instance |
| type | default\|stub\|nssa\|backbone | default | Area type |
| no-summaries | boolean | false | Block inter-area summaries (totally stubby) |

**Area types:**
- **default/standard** -- LSA types 1, 2, 3, 4, 5
- **stub** -- LSA types 1, 2, 3 only
- **totally-stubby** -- stub + no-summaries=yes (default route only)
- **NSSA** -- External via type 7 LSA
- **backbone** -- Area 0.0.0.0

### /routing/ospf/interface-template

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| networks | CIDR | -- | Network prefix |
| interfaces | string | -- | Physical interface |
| area | string | -- | Associated area |
| cost | integer | auto (100M/bandwidth) | Link metric |
| hello-interval | integer | 10s | Hello packet interval |
| dead-interval | integer | 40s | Neighbor timeout |
| priority | integer | 1 | DR/BDR election (0 = not eligible) |
| type | broadcast\|ptmp\|ptmp-broadcast\|nbma\|point-to-point | broadcast | Network type |
| vlink-transit-area | string | -- | Transit area for virtual link |
| vlink-neighbor-id | IP | -- | Remote router ID for virtual link |

### /routing/ospf/area/range

| Property | Type | Description |
|----------|------|-------------|
| prefix | CIDR | Summarized network range |
| area | string | Target area |
| advertise | yes\|no | Generate summary LSA |
| cost | integer | Associated metric |

### Other OSPF Sub-menus
- `/routing/ospf/static-neighbor` -- NBMA/PTMP neighbors
- `/routing/ospf/lsa` -- LSA database (read-only)
- `/routing/ospf/neighbor` -- Neighbor table (read-only)

---

## RIP

**ROS7 change:** No need to define networks as in v6. Use interface-templates instead.

### /routing/rip/instance

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Instance identifier |
| vrf | string | main | VRF context |
| afi | ipv4\|ipv6 | -- | Address family |
| in-filter-chain | string | -- | Inbound filter |
| out-filter-chain | string | -- | Outbound filter |
| redistribute | list | -- | External sources (bgp, connected, ospf, static) |
| originate-default | boolean | -- | Generate default route |
| routing-table | string | main | Target routing table |
| update-interval | time | -- | Advertisement frequency |

### /routing/rip/interface-template

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| instance | string | -- | Associated instance |
| interfaces | string | -- | Target interfaces |
| cost | integer | -- | Route metric increment |
| split-horizon | yes\|no | -- | Loop prevention |
| poison-reverse | yes\|no | -- | Reverse poisoning |
| mode | passive\|strict | -- | Advertisement behavior |
| key-chain | string | -- | MD5 auth chain reference |
| password | string | -- | Plaintext auth |

### /routing/rip/keys

| Property | Type | Description |
|----------|------|-------------|
| chain | string | Key collection name |
| key | string (max 16) | Auth credential |
| key-id | 0-255 | Message key selector |
| valid-from | datetime | Activation time |
| valid-till | datetime | Expiration time |

**Limitation:** Max metric is 15; metric > 15 = unreachable.

---

## Routing Filters

**ROS7 major change:** New script-like syntax. AS Path matching is per whole AS number (not character-based). Do NOT copy regex patterns from ROS6 or Cisco.

### /routing/filter/rule

| Property | Type | Description |
|----------|------|-------------|
| chain | string | Filter chain name |
| rule | string | Script-like filter logic |

**Syntax:** `if ( [matchers] ) { [actions] } else { [actions] }`

**Readable (matcher) properties:** dst, dst-len, bgp-path-len, ospf-metric, protocol, etc.
**Writable (action) properties:** distance, bgp-weight, bgp-med, gw, comment, blackhole
**Action commands:** accept, reject, return, jump, set, delete, append, filter, unset

### Other Filter Sub-menus

| Sub-path | Description |
|----------|-------------|
| `/routing/filter/community-list` | BGP community matching |
| `/routing/filter/community-ext-list` | Extended community lists |
| `/routing/filter/community-large-list` | Large community lists |
| `/routing/filter/num-list` | Numeric lists |
| `/routing/filter/select-rule` | Output route selection |
| `/routing/filter/filter-wizard` | Simplified rule generation (v7.20+) |

---

## BFD

**Menu:** `/routing/bfd`

| Sub-path | Description |
|----------|-------------|
| `/routing/bfd/configuration` | BFD configuration profiles |
| `/routing/bfd/session` | BFD sessions (read-only) |

---

## Multicast

### /routing/igmp-proxy

| Sub-path | Description |
|----------|-------------|
| `/routing/igmp-proxy` | IGMP proxy settings |
| `/routing/igmp-proxy/interface` | Upstream/downstream interfaces |
| `/routing/igmp-proxy/mfc` | Multicast forwarding cache |

### /routing/pimsm

| Sub-path | Description |
|----------|-------------|
| `/routing/pimsm/instance` | PIM-SM instances |
| `/routing/pimsm/interface-template` | Interface templates |
| `/routing/pimsm/static-rp` | Static Rendezvous Points |
| `/routing/pimsm/mfc` | Multicast forwarding cache |

---

## RPKI

**Menu:** `/routing/rpki`
Resource Public Key Infrastructure sessions for BGP route origin validation.

---

## Routing Tables and Rules

### /routing/route (read-only)
Unified view of all routes across address families.

### /routing/table

| Property | Description |
|----------|-------------|
| name | Table name |
| fib | Forwarding Information Base |

### /routing/rule

| Property | Type | Description |
|----------|------|-------------|
| dst-address | IP prefix | Destination match |
| src-address | IP prefix | Source match |
| action | lookup\|lookup-only-in-table\|drop\|unreachable | Rule action |
| table | string | Target routing table |
| interface | string | Interface match |

### /routing/settings
Global routing settings: policy rule order, gateway ping settings.

### /routing/stats
- `/routing/stats/memory` -- Memory usage by routing protocols
- `/routing/stats/process` -- Routing process statistics

### /routing/id
Router ID definitions for routing protocols.
