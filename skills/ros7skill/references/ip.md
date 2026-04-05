# /ip -- IPv4 Configuration Reference

## Table of Contents
1. [IP Address](#ip-address)
2. [IP Settings](#ip-settings)
3. [IP Route](#ip-route)
4. [ARP](#arp)
5. [IP Pool](#ip-pool)
6. [DNS](#dns)
7. [DHCP Server](#dhcp-server)
8. [DHCP Client](#dhcp-client)
9. [DHCP Relay](#dhcp-relay)
10. [IP Service](#ip-service)
11. [Neighbor Discovery](#neighbor-discovery)
12. [HotSpot](#hotspot)
13. [Cloud/DDNS](#cloud-ddns)
14. [Proxy](#proxy)
15. [Traffic Flow](#traffic-flow)
16. [VRF](#vrf)

---

## IP Address

**Menu:** `/ip/address`
**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IPv4/netmask [0..32] | -- | IPv4 address with netmask |
| comment | string | -- | Description |
| disabled | yes\|no | no | Whether address is disabled |
| interface | interface | -- | Interface for the address |
| network | IPv4 | -- | Network address (auto-calculated) |

**Read-only:** actual-interface, dynamic, invalid, slave, VRF

---

## IP Settings

**Menu:** `/ip/settings`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| accept-redirects | yes\|no | no | Accept ICMP redirect messages |
| accept-source-route | yes\|no | no | Accept packets with SRR option |
| allow-fast-path | yes\|no | yes | Enable Fast Path processing |
| arp-timeout | time | 30s | ARP record validity period |
| icmp-rate-limit | integer | 10 | Max ICMP rate (ms between packets) |
| ip-forward | yes\|no | yes | Enable packet forwarding between interfaces |
| ipv4-multipath-hash-policy | l3\|l4\|l3-inner | l3 | ECMP hash algorithm |
| max-neighbor-entries | integer | (RAM-dependent) | Maximum ARP table entries |
| route-cache | yes\|no | yes | Enable route caching |
| rp-filter | loose\|no\|strict | no | Source address validation (anti-spoofing) |
| secure-redirects | yes\|no | yes | Accept redirects only from default gateway |
| send-redirects | yes\|no | yes | Send ICMP redirects |
| tcp-syncookies | yes\|no | no | SYN flood protection |
| tcp-timestamps | disabled\|enabled\|random-offset | random-offset | TCP timestamp handling |

---

## IP Route

**Menu:** `/ip/route`
**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| dst-address | IP prefix | -- | Destination network |
| gateway | IP or interface | -- | Next-hop address or interface |
| distance | integer | -- | Route preference (lowest wins) |
| routing-table | string | main | Routing table |
| pref-src | IP | -- | Preferred source address |
| scope | integer | -- | Route scope level |
| target-scope | integer | -- | Target scope |
| check-gateway | arp\|ping\|none | -- | Gateway reachability check |
| suppress-hw-offload | yes\|no | -- | Disable hardware offloading |
| disabled | yes\|no | no | Disable route |
| comment | string | -- | Description |

**Route flags:** D=Dynamic, X=Disabled, I=Inactive, A=Active, C=Connected, S=Static, H=HW-offloaded, +=ECMP

**Related:**
- `/routing/table` -- Custom routing table definitions (name, fib)
- `/routing/rule` -- Policy routing rules (dst-address, src-address, action, table, interface)
- `/routing/route` -- Unified view of all routes (read-only)

---

## ARP

**Menu:** `/ip/arp`
**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IP | -- | IP address to map |
| interface | string | -- | Interface |
| mac-address | MAC | 00:00:00:00:00:00 | MAC address |
| published | yes\|no | no | Static proxy-ARP entry |
| comment | string | -- | Description |
| disabled | yes\|no | no | Disable entry |

**Read-only:** complete, dhcp, dynamic, invalid, status, VRF

---

## IP Pool

**Menu:** `/ip/pool`
**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Unique pool identifier |
| ranges | IP-IP list | -- | IP ranges (from1-to1,from2-to2) |
| next-pool | string | -- | Fallback pool when exhausted |
| comment | string | -- | Description |

**Sub-menu:** `/ip/pool/used` (read-only: address, pool, owner, info)

---

## DNS

**Menu:** `/ip/dns`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| allow-remote-requests | yes\|no | no | Router as DNS cache for clients |
| cache-max-ttl | time | 1w | Maximum cache TTL |
| cache-size | integer [64..4294967295] | 2048 | Cache size (KiB) |
| max-concurrent-queries | integer | 100 | Max concurrent queries |
| max-udp-packet-size | integer [50..65507] | 4096 | Max UDP packet size |
| mdns-repeat-ifaces | interface list | -- | Forward mDNS between interfaces |
| query-server-timeout | time | 2s | Timeout per server query |
| query-total-timeout | time | 10s | Total query timeout |
| servers | IPv4/IPv6[@vrf] list | -- | DNS server addresses |
| use-doh-server | string | -- | DoH server URL |
| verify-doh-cert | yes\|no | no | Validate DoH certificate |
| doh-max-concurrent-queries | integer | 50 | DoH concurrent query limit |
| vrf | vrf | main | VRF for DNS resolver |

### /ip/dns/static

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Domain name |
| type | A\|AAAA\|CNAME\|FWD\|MX\|NS\|NXDOMAIN\|SRV\|TXT | A | Record type |
| address | IPv4/IPv6 | -- | A/AAAA record address |
| cname | string | -- | CNAME alias |
| forward-to | string | -- | FWD server |
| match-subdomain | yes\|no | no | Match subdomains |
| mx-exchange | string | -- | MX server |
| mx-preference | integer | 0 | MX preference |
| regexp | regex | -- | Regex domain matching |
| text | string | -- | TXT record content |
| ttl | time | 24h | Record TTL |
| address-list | string | -- | Add to firewall address-list |

### /ip/dns/adlist (v7.15+)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| url | string | -- | Adlist URL |
| file | string | -- | Local adlist file |
| ssl-verify | yes\|no | -- | Validate SSL cert |
| disabled | yes\|no | no | Disable |

Commands: `pause`, `reload`

### /ip/dns/cache
Commands: `print`, `flush`

### /ip/dns/forwarders

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Forwarder name |
| dns-servers | string | -- | Nameserver IPs |
| doh-servers | string | -- | DoH URL |
| verify-doh-cert | yes\|no | yes | Validate DoH cert |

---

## DHCP Server

**Menu:** `/ip/dhcp-server`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address-pool | string\|static-only | static-only | IP pool |
| authoritative | after-10sec-delay\|after-2sec-delay\|yes\|no | yes | Response to unknown |
| conflict-detection | yes\|no | yes | Detect IP conflicts |
| disabled | yes\|no | no | Disable server |
| interface | string | -- | Server interface |
| lease-script | string | -- | Script on lease change |
| lease-time | time | 30m | Default lease duration |
| name | string | -- | Server name |
| use-radius | yes\|no\|accounting | no | RADIUS usage |
| add-arp | yes\|no | no | Dynamic ARP entries |
| client-mac-limit | integer\|unlimited | unlimited | Clients per MAC |

### /ip/dhcp-server/network

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IP/netmask | -- | Network for DHCP |
| gateway | IP | 0.0.0.0 | Default gateway |
| dns-server | string | -- | DNS servers |
| domain | string | -- | DNS domain |
| ntp-server | IP | -- | NTP servers |
| wins-server | IP | -- | WINS servers |
| next-server | IP | -- | PXE boot server |
| boot-file-name | string | -- | PXE boot filename |
| dhcp-option | string | -- | Additional DHCP options |
| netmask | integer [0..32] | 0 | Network mask |

### /ip/dhcp-server/lease

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IP\|pool | 0.0.0.0 | Lease address |
| mac-address | MAC | 00:00:00:00:00:00 | Client MAC |
| client-id | string | none | Client identifier |
| server | string | -- | DHCP server |
| lease-time | time | 0s | Lease duration |
| block-access | yes\|no | no | Block client |
| rate-limit | string | -- | Bandwidth limit (rx/tx) |
| comment | string | -- | Description |

Commands: `make-static`, `check-status`, `send-reconfigure`

### /ip/dhcp-server/option

| Property | Type | Description |
|----------|------|-------------|
| code | integer [1..254] | DHCP option code |
| name | string | Option name |
| value | string | Parameter value |

### /ip/dhcp-server/matcher (v7.4+)

| Property | Type | Description |
|----------|------|-------------|
| code | integer [1..254] | DHCP option code to match |
| matching-type | exact\|substring | Match method |
| value | string | Search value |
| address-pool | string\|static-only | Pool assignment |
| option-set | name\|none | Custom option set |

### /ip/dhcp-server/alert

Detects rogue DHCP servers on the network.

---

## DHCP Client

**Menu:** `/ip/dhcp-client`
**Commands:** `add`, `set`, `remove`, `print`, `release`, `renew`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| add-default-route | yes\|no\|special-classless | yes | Install default route |
| default-route-distance | integer [0..255] | -- | Route distance |
| disabled | yes\|no | yes | Disable client |
| host-name | string | -- | Hostname sent to server |
| interface | string | -- | Client interface |
| script | script | -- | Script on lease change |
| use-peer-dns | yes\|no | yes | Accept DNS from server |
| use-peer-ntp | yes\|no | yes | Accept NTP from server |

---

## DHCP Relay

**Menu:** `/ip/dhcp-relay`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| add-relay-info | yes\|no | no | Add Option 82 |
| dhcp-server | IPv4 list | -- | DHCP server address(es) |
| dhcp-server-vrf | vrf | main | Server VRF |
| interface | interface | -- | Relay interface |
| local-address | IP | 0.0.0.0 | Relay identification |
| name | string | -- | Relay name |

---

## IP Service

**Menu:** `/ip/service` (cannot add/remove, only set)

**Default services:**

| Service | Port | Protocol |
|---------|------|----------|
| telnet | 23 | tcp |
| ftp | 21 | tcp |
| www | 80 | tcp |
| ssh | 22 | tcp |
| www-ssl | 443 | tcp |
| api | 8728 | tcp |
| winbox | 8291 | tcp |
| api-ssl | 8729 | tcp |

| Property | Type | Description |
|----------|------|-------------|
| address | IP/IPv6 prefix list | Allowed access prefixes |
| certificate | name | SSL certificate |
| disabled | yes\|no | Enable/disable service |
| max-sessions | integer [1-1000] | Max connections |
| port | integer [1-65535] | Listening port |
| tls-version | any\|only-1.2 | TLS version |
| vrf | name | VRF instance |

---

## Neighbor Discovery

**Menu:** `/ip/neighbor`
- `/ip/neighbor` -- Discovered devices (MNDP/CDP/LLDP)
- `/ip/neighbor/discovery-settings` -- Protocol settings

---

## HotSpot

**Menu:** `/ip/hotspot`

| Sub-path | Description |
|----------|-------------|
| `/ip/hotspot` | Gateway instances |
| `/ip/hotspot/profile` | Server profiles |
| `/ip/hotspot/user` | Users |
| `/ip/hotspot/user/profile` | User profiles |
| `/ip/hotspot/active` | Active sessions |
| `/ip/hotspot/host` | Hosts |
| `/ip/hotspot/cookie` | Cookies |
| `/ip/hotspot/ip-binding` | IP binding (bypass/block) |
| `/ip/hotspot/walled-garden` | Walled garden HTTP rules |
| `/ip/hotspot/walled-garden/ip` | Walled garden IP rules |

---

## Cloud/DDNS

**Menu:** `/ip/cloud`
- MikroTik DDNS and time synchronization
- `/ip/cloud/advanced` -- Advanced settings

---

## Proxy

- `/ip/proxy` -- HTTP caching proxy
- `/ip/proxy/access` -- Access rules
- `/ip/proxy/cache` -- Cache management
- `/ip/socks` -- SOCKS proxy

---

## Traffic Flow

**Menu:** `/ip/traffic-flow`
- NetFlow/sFlow/IPFIX export
- `/ip/traffic-flow/target` -- Collector targets

---

## VRF

**Menu:** `/ip/vrf`
- VRF instance definitions (replaces deprecated /ip/route/vrf)
