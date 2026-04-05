# /interface -- Network Interface Reference

## Table of Contents
1. [Ethernet](#ethernet)
2. [Bridge](#bridge)
3. [VLAN](#vlan)
4. [Bonding](#bonding)
5. [WiFi (ROS7)](#wifi)
6. [Wireless (Legacy)](#wireless-legacy)
7. [Interface Lists](#interface-lists)
8. [LTE](#lte)
9. [Dot1X](#dot1x)
10. [MACsec](#macsec)

## Common Interface Commands

Available at `/interface`:
- `print` / `print stats` -- List interfaces with traffic statistics
- `set` -- Configure name, mtu, etc.
- `enable` / `disable` -- Toggle interface state
- `monitor-traffic` -- Real-time per-interface traffic
- `reset-counters` -- Reset traffic counters

---

## Ethernet

**Menu:** `/interface/ethernet`
**Commands:** `set`, `print`, `print stats`, `monitor`, `blink`, `reset-counters`, `reset-mac-address`, `cable-test`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| advertise | link mode list | (empty) | Advertised link modes (v7.12+ new format) |
| arp | disabled\|enabled\|local-proxy-arp\|proxy-arp\|reply-only | enabled | ARP behavior |
| arp-timeout | auto\|integer | auto | ARP record timeout |
| auto-negotiation | yes\|no | yes | Capability negotiation |
| bandwidth | integer/integer | unlimited/unlimited | RX/TX bandwidth limits (kbps) |
| combo-mode | auto\|copper\|sfp | auto | Combo interface mode |
| fec-mode | auto\|fec74\|fec91\|off | auto | Forward Error Correction (SFP28/QSFP+/QSFP28) |
| l2mtu | 0-65536 | (empty) | Layer 2 MTU |
| mac-address | MAC | (empty) | Custom MAC address |
| mdix-enable | yes\|no | yes | MDI/X auto-crossover |
| mtu | 0-65536 | 1500 | Layer 3 MTU |
| name | string | -- | Interface name |
| poe-out | auto-on\|forced-on\|off | off | PoE output control |
| poe-priority | 0-99 | (empty) | PoE priority |
| rx-flow-control | on\|off\|auto | off | Receive pause frames |
| sfp-shutdown-temperature | integer | 95 (80 for QSFP) | Thermal shutdown (v7.6+) |
| speed | link mode | (empty) | Speed when auto-negotiation disabled (v7.12+) |
| tx-flow-control | on\|off\|auto | off | Transmit pause frames |

**ROS7.12+ link mode change:**
- Old: `10M-full`, `100M-full`, `1000M-full` + separate `full-duplex`
- New: `<speed>-base<medium>[-duplex]` (e.g., `10M-baseT-full`, `1G-baseX`, `25G-baseCR`)
- `full-duplex` property removed (embedded in link mode)

**Sub-menus:**
- `/interface/ethernet/switch` -- Switch chip configuration
- `/interface/ethernet/switch/rule` -- Switch ACL rules
- `/interface/ethernet/switch/host` -- Switch host table
- `/interface/ethernet/switch/port` -- Switch port settings
- `/interface/ethernet/switch/port-isolation` -- Port isolation
- `/interface/ethernet/switch/vlan` -- Switch VLAN table
- `/interface/ethernet/poe` -- PoE settings

---

## Bridge

**Menu:** `/interface/bridge`
**Commands:** `add`, `set`, `remove`, `print`, `monitor`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| admin-mac | MAC | none | Static bridge MAC (when auto-mac=no) |
| ageing-time | time | 00:05:00 | Host info retention in bridge DB |
| arp | enum | enabled | ARP behavior |
| auto-mac | yes\|no | yes | Auto MAC from bridge ports |
| dhcp-snooping | yes\|no | no | Enable DHCP snooping |
| dhcp-agent-circuit-id | string | !dhcp-agent-circuit-id | Option 82 Circuit ID (v7.23+) |
| dhcp-agent-remote-id | string | !dhcp-agent-remote-id | Option 82 Remote ID (v7.23+) |
| ether-type | 0x9100\|0x8100\|0x88a8 | 0x8100 | VLAN tag EtherType |
| fast-forward | yes\|no | yes | Fast forwarding for 2-port bridges |
| forward-delay | time | 00:00:15 | STP init phase duration |
| frame-types | admit-all\|admit-only-untagged-and-priority-tagged\|admit-only-vlan-tagged | admit-all | Allowed frame types |
| igmp-snooping | yes\|no | no | Multicast group learning |
| ingress-filtering | yes\|no | yes | VLAN ingress validation (default yes in v7) |
| max-hops | integer [6-40] | 20 | BPDU hop limit (MSTP) |
| max-learned-entries | integer\|auto\|unlimited | auto | FDB learning limit (v7.16+) |
| multicast-querier | yes\|no | no | Generate IGMP/MLD queries |
| name | text | bridgeN | Interface name |
| priority | integer [0-65535] | 32768 | Bridge priority (STP) |
| protocol-mode | none\|rstp\|stp\|mstp | rstp | Spanning tree mode |
| pvid | integer [1-4094] | 1 | Port VLAN ID |
| ra-guard | yes\|no | no | Router Advertisement validation |
| region-name | text | -- | MSTP region name |
| region-revision | integer [0-65535] | 0 | MSTP revision |
| vlan-filtering | yes\|no | no | Enable VLAN filtering |

### /interface/bridge/port

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| auto-isolate | yes\|no | no | Prevent non-edge forwarding |
| bpdu-guard | yes\|no | no | Disable port on BPDU |
| bridge | name | -- | Parent bridge |
| broadcast-flood | yes\|no | yes | Flood broadcast |
| edge | auto\|no\|yes\|no-discover\|yes-discover | auto | Edge port config |
| fast-leave | yes\|no | no | Immediate IGMP leave |
| frame-types | enum | admit-all | Ingress frame filtering |
| ingress-filtering | yes\|no | yes | VLAN membership validation |
| hw | yes\|no | yes | Hardware offloading |
| interface | name | -- | Physical interface |
| path-cost | integer [1-200000000] | -- | STP port cost |
| point-to-point | auto\|yes\|no | auto | Point-to-point detection |
| priority | integer [0-240] | 128 | Port priority |
| pvid | integer [1-4094] | 1 | Port VLAN ID |
| tag-stacking | yes\|no | no | Add additional VLAN tag |
| trusted | yes\|no | no | DHCP snooping trusted |
| unknown-multicast-flood | yes\|no | yes | Flood unknown multicast |
| unknown-unicast-flood | yes\|no | yes | Flood unknown unicast |
| horizon | integer | none | Split horizon group |

### /interface/bridge/vlan

| Property | Type | Description |
|----------|------|-------------|
| bridge | name | Parent bridge |
| tagged | interface list | Ports that send tagged frames |
| untagged | interface list | Ports that send untagged frames |
| vlan-ids | integer list [1-4094] | VLAN IDs |

### Other Bridge Sub-menus
- `/interface/bridge/host` -- FDB entries (mac-address, interface, bridge, vid)
- `/interface/bridge/mdb` -- Multicast database
- `/interface/bridge/settings` -- Global settings (allow-fast-path, use-ip-firewall, use-ip-firewall-for-vlan)
- `/interface/bridge/nat` -- Bridge NAT rules (MAC-level)
- `/interface/bridge/filter` -- Bridge firewall filter rules

**ROS7 changes:**
- `ingress-filtering` enabled by default in v7
- v7.23: `add-dhcp-option82` removed; replaced with variable-based circuit/remote ID
- v7.16: `max-learned-entries` added

---

## VLAN

**Menu:** `/interface/vlan`
**Commands:** `add`, `set`, `remove`, `print`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| arp | enum | enabled | ARP behavior |
| arp-timeout | auto\|integer | auto | ARP record timeout |
| disabled | yes\|no | no | Interface disabled |
| interface | name | (required) | Parent interface |
| l3-hw-offloading | yes\|no | yes | L3HW offloading (v7.21+) |
| mvrp | yes\|no | no | Multiple VLAN Registration Protocol |
| mtu | integer [68-65535] | 1500 | Layer 3 MTU |
| name | string | (required) | Interface name |
| use-service-tag | yes\|no | (unset) | IEEE 802.1ad Service Tag |
| vlan-id | integer [1-4094] | 1 | VLAN identifier |

---

## Bonding

**Menu:** `/interface/bonding`
**Commands:** `add`, `set`, `remove`, `print`, `monitor`, `monitor-slaves`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Interface name |
| slaves | string | none | Comma-separated ethernet interfaces |
| mode | enum | balance-rr | Bonding mode |
| mtu | integer | 1500 | MTU |
| primary | string | none | Primary slave |
| transmit-hash-policy | layer-2\|layer-2-and-3\|layer-3-and-4\|encap-2-and-3\|encap-3-and-4 | layer-2 | Traffic distribution |
| link-monitoring | arp\|mii\|none | mii | Link detection method |
| mii-interval | time | 100ms | MII monitoring frequency |
| arp-interval | time | 100ms | ARP monitoring frequency |
| arp-ip-targets | IP | -- | ARP target IP |
| up-delay | time | 0s | Link up delay |
| down-delay | time | 0s | Link down delay |
| min-links | integer | 0 | Min active slaves |
| lacp-mode | active\|passive | active | LACP participation |
| lacp-rate | 1sec\|30secs | 30secs | LACP exchange rate |
| lacp-user-key | integer [0-1023] | 0 | Port key upper bits (v7.3+) |
| mlag-id | integer | -- | Multi-chassis LAG ID |
| forced-mac-address | MAC | none | Static MAC |

**Modes:** 802.3ad (LACP), balance-xor, balance-rr, active-backup, balance-tlb, balance-alb, broadcast

---

## WiFi

**Menu:** `/interface/wifi` (requires wifi-qcom or wifi-qcom-ac package)
**Available since:** ROS7 (renamed from wifiwave2 in v7.13)

### /interface/wifi/configuration

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| mode | ap\|station\|station-bridge\|station-pseudobridge | ap | Operating mode |
| ssid | string | -- | Network name |
| country | country code | Latvia | Regulatory domain |
| hide-ssid | yes\|no | no | SSID visibility |
| beacon-interval | 100ms..1s | 100ms | Beacon timing |
| dtim-period | 1..255 | 1 | Multicast delivery period |
| max-clients | 1..1000 | 1000 | Max associated devices |
| tx-power | 0..40 dBm | unset | Transmit power ceiling |
| distance | kilometers | 2km | Long-range link distance |
| multicast-enhance | enabled\|disabled | disabled | Multicast to unicast |
| station-roaming | yes\|no | no | Roaming between APs |

### /interface/wifi/channel

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| band | 2ghz-g\|2ghz-n\|2ghz-ax\|2ghz-be\|5ghz-a\|5ghz-ac\|5ghz-an\|5ghz-ax\|5ghz-be\|6ghz-ax\|6ghz-be | newest supported | Band/standard |
| frequency | MHz list/ranges | all supported | Channel frequencies |
| width | 20mhz\|20/40mhz\|20/40/80mhz\|20/40/80/160mhz\|20/40/80/160/320mhz | widest supported | Channel bandwidth |
| skip-dfs-channels | 10min-cac\|all\|disabled | disabled | DFS avoidance |

### /interface/wifi/security

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| authentication-types | list | empty (open) | wpa-psk, wpa2-psk, wpa3-psk, wpa-eap, wpa2-eap, wpa3-eap, owe |
| passphrase | string (8-63) | "" | PSK password |
| encryption | list | ccmp | ccmp, ccmp-256, gcmp, gcmp-256, tkip |
| group-encryption | enum | ccmp | Multicast cipher |
| management-protection | allowed\|disabled\|required | -- | 802.11w PMF |
| ft | yes\|no | no | 802.11r fast roaming |
| ft-over-ds | yes\|no | no | FT over DS |
| wps | push-button\|disabled | push-button | WPS mode |
| disable-pmkid | yes\|no | no | Omit PMKID |

### /interface/wifi/datapath

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| bridge | bridge interface | none | Bridge assignment |
| vlan-id | 1..4095\|none | none | Client VLAN |
| client-isolation | yes\|no | no | Prevent client-to-client |
| traffic-processing | on-cap\|on-capsman\|on-capsman-secure | on-cap | Data forwarding (v7.21+) |

### /interface/wifi/aaa

RADIUS integration for wireless authentication (WPA2/3-Enterprise).

### /interface/wifi/security/multi-passphrase (PPSK)

Up to 10,000 passphrases per interface; per-passphrase VLAN assignment. Not compatible with WPA3-PSK.

### /interface/wifi/capsman

CAPsMAN (centralized AP management). Sub-menus:
- `/interface/wifi/capsman` -- CAPsMAN settings
- `/interface/wifi/cap` -- CAP (controlled AP) configuration
- `/interface/wifi/provisioning` -- Radio template provisioning rules
- `/interface/wifi/capsman/remote-cap` -- Remote CAP management

### Other WiFi Sub-menus
- `/interface/wifi/access-list` -- Client connection filtering
- `/interface/wifi/registration-table` -- Connected clients
- `/interface/wifi/radio` -- Radio capabilities
- `/interface/wifi/frequency-scan` -- RF spectrum scanner
- `/interface/wifi/spectral-scan` -- Frequency power plotting (v7.16+)
- `/interface/wifi/scan` -- AP discovery (station mode)
- `/interface/wifi/interworking` -- Hotspot 2.0 profiles

---

## Wireless (Legacy)

**Menu:** `/interface/wireless` (802.11a/b/g/n/ac)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Interface name |
| mode | station\|ap-bridge\|bridge\|station-wds\|... | station | Operating mode |
| ssid | string (0-32) | system/identity | Network name |
| band | 2ghz-b\|2ghz-b/g\|5ghz-a\|5ghz-a/n/ac\|... | -- | Frequency band |
| frequency | integer (MHz) | -- | Channel frequency |
| channel-width | 5mhz\|10mhz\|20mhz\|20/40mhz\|... | 20mhz | Spectrum width |
| tx-power | integer [-30..40] | -- | Transmit power (dBm) |
| country | country name | etsi | Regulatory domain |
| security-profile | string | default | Security config reference |
| distance | dynamic\|indoors\|integer | dynamic | ACK timeout calculation |
| hw-retries | integer [0-15] | 7 | Frame retry attempts |

Sub-menus: security-profiles, access-list, connect-list, registration-table, channels, nstreme, nstreme-dual, cap, snooper, align

---

## Interface Lists

**Menu:** `/interface/list`

| Sub-path | Description |
|----------|-------------|
| `/interface/list` | List definitions |
| `/interface/list/member` | Static member assignments |

Predefined lists: `all`, `none`, `dynamic`, `static`

---

## LTE

**Menu:** `/interface/lte`

| Sub-path | Description |
|----------|-------------|
| `/interface/lte` | LTE interface config |
| `/interface/lte/apn` | APN profiles |
| `/interface/lte/at-chat` | AT command communication |
| `/interface/lte/info` | Modem status |

---

## Dot1X

**Menu:** `/interface/dot1x`

| Sub-path | Description |
|----------|-------------|
| `/interface/dot1x/server` | 802.1X authenticator |
| `/interface/dot1x/client` | 802.1X supplicant |

---

## MACsec

**Menu:** `/interface/macsec`

| Sub-path | Description |
|----------|-------------|
| `/interface/macsec` | MACsec interface config |
| `/interface/macsec/profile` | MACsec security profiles |
