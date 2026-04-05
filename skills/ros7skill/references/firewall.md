# /ip/firewall -- Firewall Reference

## Table of Contents
1. [Filter](#filter)
2. [NAT](#nat)
3. [Mangle](#mangle)
4. [Raw](#raw)
5. [Connection Tracking](#connection-tracking)
6. [Address Lists](#address-lists)
7. [Common Matchers](#common-matchers)

---

## Filter

**Menu:** `/ip/firewall/filter`
**Commands:** `add`, `set`, `remove`, `print`, `print stats`, `reset-counters`, `reset-counters-all`

**Chains:**
- **input** -- Packets destined for the router itself
- **forward** -- Packets passing through the router
- **output** -- Packets originating from the router

**Actions:**

| Action | Description |
|--------|-------------|
| accept | Accept packet; stop processing |
| add-dst-to-address-list | Add destination to address-list |
| add-src-to-address-list | Add source to address-list |
| drop | Silently drop |
| fasttrack-connection | Enable FastTrack (IPv4 only) |
| jump | Jump to user-defined chain |
| log | Log and continue |
| passthrough | Count and continue |
| reject | Drop with ICMP error |
| return | Return from jump |
| tarpit | Hold TCP connections (IPv4 only) |

**Filter properties:**

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| action | name | accept | Filter action |
| chain | string | -- | Chain (input/forward/output/custom) |
| hw-offload | no\|yes | yes | Hardware offloading for FastTrack |
| reject-with | enum | icmp-no-route | ICMP error type for reject |
| jump-target | name | -- | Target chain for jump |
| address-list | name | -- | List for add-to-address-list |
| address-list-timeout | time | -- | Timeout for list entries |
| log | yes\|no | no | Log matches |
| log-prefix | string | -- | Log prefix |

**reject-with options:** icmp-no-route, icmp-admin-prohibited, icmp-not-neighbour, icmp-address-unreachable, icmp-port-unreachable, tcp-reset, icmp-err-src-routing-header, icmp-headers-too-long

---

## NAT

**Menu:** `/ip/firewall/nat`

**Chains:**
- **srcnat** -- Source NAT (packets leaving)
- **dstnat** -- Destination NAT (packets entering)
- **input** -- Packets to router applications (ROS7 new)
- **output** -- Packets from router applications (ROS7 new)

**Actions:**

| Action | Description |
|--------|-------------|
| accept | No translation |
| dst-nat | Replace destination address/port |
| src-nat | Replace source address with specified IP |
| masquerade | Replace source with outgoing interface address (dynamic IPs) |
| netmap | Static 1:1 address mapping |
| redirect | Replace destination with incoming interface address |
| same | Consistent source/dest per client (IPv4 only) |
| endpoint-independent-nat | UDP source-independent filtering |
| socksify | Route through SOCKS5 proxy |
| jump / log / passthrough / return | Standard chain actions |
| add-dst-to-address-list / add-src-to-address-list | Address list operations |

**NAT-specific properties:**

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| to-addresses | IP[-range] | 0.0.0.0 | Replacement address |
| to-ports | integer[-integer] | -- | Replacement port(s) |
| same-not-by-dst | yes\|no | -- | Ignore dest for same action |
| randomize-port | yes\|no | -- | Random port for endpoint-independent-nat |

**Service ports (ALGs):** `/ip/firewall/service-port`
FTP, H323, IRC, PPTP, SIP, TFTP, RTSP, UDPLITE, DCCP, SCTP

---

## Mangle

**Menu:** `/ip/firewall/mangle`

**Chains:** prerouting, input, output, forward, postrouting

**Actions:**

| Action | Description |
|--------|-------------|
| change-dscp | Modify DSCP field |
| change-mss | Modify TCP MSS |
| change-ttl | Modify TTL |
| clear-df | Clear Don't Fragment flag |
| mark-connection | Mark entire connection |
| mark-packet | Mark individual packet |
| mark-routing | Mark for policy routing |
| route | Force gateway (prerouting only) |
| set-priority | Set packet priority |
| sniff-pc / sniff-tzsp | Send to capture system |
| strip-ipv4-options | Remove IPv4 options |
| accept / drop / jump / log / passthrough / return | Standard actions |

**Mangle-specific properties:**

| Property | Type | Description |
|----------|------|-------------|
| new-dscp | integer [0..63] | New DSCP value |
| new-mss | integer | New MSS value |
| new-packet-mark | string | Packet mark name |
| new-priority | integer\|from-dscp\|from-ingress | Priority value |
| new-routing-mark | string | Routing mark name |
| new-ttl | decrement\|increment\|set:integer | TTL operation |
| new-connection-mark | string | Connection mark name |
| passthrough | yes\|no | Continue to next rule |
| route-dst | IP | Forced gateway IP |

**Note:** Max 4,096 unique packet marks.

---

## Raw

**Menu:** `/ip/firewall/raw`

**Chains:** prerouting, output (only two)

**Key action:** `notrack` -- Bypass connection tracking for matched packets

Also supports: accept, drop, add-to-address-list, jump, log, passthrough, return

**Purpose:** Drop or bypass packets before connection tracking to reduce CPU load.

---

## Connection Tracking

**Menu:** `/ip/firewall/connection/tracking`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no\|auto | auto | "auto" enables on first firewall rule |
| loose-tcp-tracking | yes\|no | yes | Incomplete handshakes as ESTABLISHED |
| tcp-established-timeout | time | 1d | Established connection timeout |
| tcp-syn-sent-timeout | time | 5s | SYN state timeout |
| tcp-fin-wait-timeout | time | 10s | FIN-WAIT timeout |
| tcp-close-wait-timeout | time | 10s | CLOSE-WAIT timeout |
| tcp-time-wait-timeout | time | 10s | TIME-WAIT timeout |
| udp-timeout | time | 30s | Unidirectional UDP timeout |
| udp-stream-timeout | time | 3m | Bidirectional UDP timeout |
| icmp-timeout | time | 10s | ICMP timeout |
| generic-timeout | time | 10m | Other protocol timeout |

---

## Address Lists

**Menu:** `/ip/firewall/address-list`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | DNS\|IP/netmask\|IP-IP | -- | IP, range, or DNS name |
| list | string | -- | List name |
| timeout | time | -- | Auto-removal time (unset=permanent) |
| disabled | yes\|no | no | Disable entry |

Entries with timeout are stored in RAM and lost on reboot. Entries without timeout are saved to disk.

---

## Common Matchers

Available across filter, nat, mangle, and raw rules.

### Stateless Matchers

| Matcher | Type | Description |
|---------|------|-------------|
| chain | name | Chain name |
| src-address | IP/netmask\|IP-IP | Source IP |
| dst-address | IP/netmask\|IP-IP | Destination IP |
| src-address-list | name | Source address list |
| dst-address-list | name | Destination address list |
| src-port | integer/range | Source port (requires protocol) |
| dst-port | integer/range | Destination port (requires protocol) |
| port | integer/range | Source OR destination port |
| protocol | name/ID | IP protocol (tcp, udp, icmp, etc.) |
| in-interface | name | Incoming interface |
| out-interface | name | Outgoing interface |
| in-interface-list | name | Incoming interface list |
| out-interface-list | name | Outgoing interface list |
| in-bridge-port | name | Bridge port (incoming) |
| out-bridge-port | name | Bridge port (outgoing) |
| src-mac-address | MAC | Source MAC |
| tcp-flags | ack,cwr,ece,fin,psh,rst,syn,urg | TCP flags (negate with `!`) |
| tcp-mss | integer/range | TCP MSS value |
| content | string | Packet content match |
| dscp | integer [0-63] | DSCP field |
| icmp-options | type:code | ICMP type and code |
| packet-mark | no-mark\|string | Mangle packet mark |
| packet-size | integer/range | Packet size (bytes) |
| ttl | integer [0-255] | Time to Live (IPv4 only) |
| limit | rate[/time],burst:mode | Rate limiting |
| dst-limit | rate[/time],burst,mode[/expire] | Per-flow rate limiting |
| nth | integer,integer | Match every nth packet |
| random | integer [1-99] | Random probability % |
| time | time-time,day-list | Time-based matching |
| tls-host | string | TLS SNI hostname (`*example.com`) |
| ipsec-policy | direction,policy | IPsec processing |
| per-connection-classifier | hash:denominator/remainder | Per-stream classification |
| fragment | yes\|no | Fragmented packets |
| hotspot | auth\|from-client\|http\|local-dst\|to-client | HotSpot matching |
| src-address-type | unicast\|local\|broadcast\|multicast | Source type |
| dst-address-type | unicast\|local\|broadcast\|multicast | Destination type |

### Stateful Matchers (require connection tracking)

| Matcher | Type | Description |
|---------|------|-------------|
| connection-state | established\|invalid\|new\|related\|untracked | Connection state |
| connection-mark | no-mark\|string | Connection mark |
| connection-nat-state | srcnat\|dstnat | NAT status |
| connection-type | ftp\|h323\|irc\|pptp\|sip\|tftp | ALG type |
| connection-bytes | integer-integer | Bytes through connection |
| connection-limit | integer,netmask | Connections per address |
| connection-rate | integer | Connection speed (bps) |
| layer7-protocol | name | L7 protocol name |
| routing-mark | string | Routing mark |

---

## IPv6 Firewall

The IPv6 firewall mirrors the IPv4 structure:

| Menu | Description |
|------|-------------|
| `/ipv6/firewall/filter` | IPv6 packet filter |
| `/ipv6/firewall/mangle` | IPv6 packet marking |
| `/ipv6/firewall/raw` | IPv6 raw rules |
| `/ipv6/firewall/address-list` | IPv6 address lists |
| `/ipv6/firewall/connection` | IPv6 connection tracking |

Same chains, actions, and matchers as IPv4 (minus IPv4-only features like fragment, hotspot, ttl).
