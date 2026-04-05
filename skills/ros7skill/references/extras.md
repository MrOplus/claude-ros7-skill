# Additional Menus Reference

## Table of Contents
1. [PPP](#ppp)
2. [Certificate](#certificate)
3. [User Management](#user-management)
4. [SNMP](#snmp)
5. [RADIUS](#radius)
6. [MPLS](#mpls)
7. [File Management](#file-management)
8. [Disk Management](#disk-management)
9. [Container](#container)
10. [IoT](#iot)
11. [ZeroTier](#zerotier)
12. [User Manager](#user-manager)
13. [IPv6](#ipv6)

---

## PPP

### /ppp/profile

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Profile name |
| local-address | IP\|pool | -- | Local tunnel address |
| remote-address | IP\|pool | -- | Remote tunnel address |
| bridge | string | -- | Bridge for PPP endpoints |
| change-tcp-mss | yes\|no\|default | no | Adjust TCP MSS |
| dns-server | IP | -- | DNS for clients |
| idle-timeout | time | -- | Inactivity disconnect |
| session-timeout | time | -- | Max connection time |
| rate-limit | string | -- | Bandwidth limit (rx/tx) |
| use-compression | yes\|no\|default | default | Compression |
| use-encryption | yes\|no\|default\|require | default | Encryption |
| use-ipv6 | yes\|no\|default\|require | default | IPv6 |
| on-up / on-down | script | -- | Login/logout scripts |
| only-one | yes\|no\|default | default | Single session per user |
| parent-queue | none\|name | -- | Parent queue |
| interface-list | list | -- | Interface list assignment |

### /ppp/secret

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Username |
| password | string | -- | Password |
| profile | string | default | Profile |
| service | any\|async\|isdn\|l2tp\|pppoe\|pptp\|ovpn\|sstp | any | Allowed services |
| caller-id | string | -- | Source identifier |
| local-address / remote-address | IP | -- | IP overrides |
| routes | string | -- | Routes on connect |
| limit-bytes-in / limit-bytes-out | integer | 0 | Byte limits |

### /ppp/active (read-only)
Active PPP connections: name, address, caller-id, service, encoding, uptime, bytes, packets

### /ppp/aaa

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| use-radius | yes\|no | no | RADIUS auth |
| accounting | yes\|no | yes | RADIUS accounting |
| interim-update | time | 0s | Interim update interval |

---

## Certificate

**Menu:** `/certificate`
**Commands:** `add`, `set`, `remove`, `print`, `sign`, `export-certificate`, `import`, `add-acme`

### Template Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| common-name | string | -- | Certificate CN |
| days-valid | days | 365 | Validity period |
| digest-algorithm | md5\|sha1\|sha256\|sha384\|sha512 | sha256 | Hash algorithm |
| key-size | 1024\|2048\|4096\|8192\|prime256v1\|secp384r1\|secp521r1 | 2048 | Key size |
| key-usage | list | (all) | Usage flags |
| subject-alt-name | DNS:\|IP:\|email: | -- | SAN |
| country / state / locality / organization / unit | string | -- | Issuer fields |

### Commands
- `sign <name> [ca-crl-host=IP]` -- Sign certificate
- `export-certificate <name> [type=pem\|pkcs12] [export-passphrase=str]` -- Export
- `import [file-name=str] [passphrase=str] [trusted=yes\|no]` -- Import
- `add-acme [directory-url=URL] [domain-names=list]` -- ACME/Let's Encrypt

### /certificate/settings
CRL settings: crl-download, crl-store (ram\|system), crl-use, builtin-trust-store

---

## User Management

### /user

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Username |
| password | string | -- | Password |
| group | string | -- | Group membership |
| address | IP/mask\|IPv6 | -- | Allowed login addresses |

### /user/group

| Property | Type | Description |
|----------|------|-------------|
| name | string | Group name |
| policy | list | Policies: local, telnet, ssh, ftp, reboot, read, write, policy, test, winbox, password, web, sniff, sensitive, api, rest-api, romon |

Default groups: read, write, full

### /user/ssh-keys
- `/user/ssh-keys/public-keys` -- Import SSH public keys
- `/user/ssh-keys/private-keys` -- Import SSH private keys

### /user/active (read-only)
Active sessions. Command: `request-logout`

### /user/aaa

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| use-radius | yes\|no | no | RADIUS auth |
| default-group | string | read | Default RADIUS group |

### /user/settings
Password complexity: minimum-password-length, minimum-categories

---

## SNMP

**Menu:** `/snmp`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | no | Enable SNMP |
| contact | string | -- | Contact info |
| location | string | -- | Location |
| trap-community | string | public | Trap community |
| trap-generators | interfaces\|start-trap\|temp-exception | -- | Trap triggers |
| trap-target | IP list | 0.0.0.0 | Trap receivers |
| trap-version | 1\|2\|3 | 1 | SNMP version |

### /snmp/community

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Community name |
| address | IP CIDR | 0.0.0.0/0 | Allowed sources |
| security | authorized\|none\|private | none | Security level |
| read-access | yes\|no | yes | Allow reads |
| write-access | yes\|no | no | Allow writes |
| authentication-protocol | MD5\|SHA1 | MD5 | SNMPv3 auth |
| encryption-protocol | DES\|AES | DES | SNMPv3 encryption |

---

## RADIUS

**Menu:** `/radius`

| Property | Type | Description |
|----------|------|-------------|
| address | IP | RADIUS server address |
| secret | string | Shared secret |
| service | list | Services: ppp, login, hotspot, wireless, dhcp, ipsec, dot1x |
| timeout | time | Request timeout |
| src-address | IP | Source address |
| accounting-port | integer | Accounting port (default 1813) |
| authentication-port | integer | Auth port (default 1812) |

- `/radius/incoming` -- CoA/DM messages
- `/radius/monitor` -- Client statistics

---

## MPLS

### /mpls/ldp

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| afi | ip\|ipv6 | -- | Address family |
| lsr-id | IP | -- | Label switching router ID |
| transport-addresses | IP | -- | LDP session addresses |
| use-explicit-null | yes\|no | no | Explicit-null bindings |
| vrf | name | main | VRF |

### /mpls/ldp/interface

| Property | Type | Description |
|----------|------|-------------|
| interface | string | Interface or list |
| hello-interval | time | Hello interval (default 5s) |
| hold-time | time | Neighbor timeout (default 15s) |

### Other MPLS Sub-menus
- `/mpls/ldp/neighbor` -- LDP neighbors
- `/mpls/ldp/accept-filter` -- Label accept filters
- `/mpls/ldp/advertise-filter` -- Label advertise filters
- `/mpls/local-mapping` / `/mpls/remote-mapping` -- Label mappings
- `/mpls/forwarding-table` -- Forwarding entries
- `/mpls/interface` -- MPLS-enabled interfaces

### RSVP-TE
See `/interface/traffic-eng` in vpn.md reference.

---

## File Management

**Menu:** `/file`
- `print` -- List files
- `remove` -- Delete file
- `set` -- Rename file

---

## Disk Management

**Menu:** `/disk`

| Command | Description |
|---------|-------------|
| `print` | List storage devices |
| `format` | Format with filesystem |
| `eject` | Safely unmount |
| `trim` | TRIM unused blocks |
| `add` | Create partitions, swap, tmpfs |
| `set` | Configure mount point |
| `test` | Performance test (v7.16+) |
| `monitor-traffic` | Real-time I/O stats |

---

## Container

**Menu:** `/container` (requires `container` package)

| Command | Description |
|---------|-------------|
| `add` | Add container from image |
| `start` / `stop` | Control container |
| `shell` | Attach shell |

Sub-menus:
- `/container/config` -- Global settings (registry, tmpdir)
- `/container/envs` -- Environment variables
- `/container/mounts` -- Volume mounts

---

## IoT

**Menu:** `/iot` (requires `iot` package)

| Sub-path | Description |
|----------|-------------|
| `/iot/mqtt/brokers` | MQTT broker connections |
| `/iot/mqtt/publish` | Publish MQTT messages |
| `/iot/lora` | LoRa radio config |
| `/iot/lora/servers` | LoRa network servers |
| `/iot/bluetooth` | Bluetooth scanner |
| `/iot/bluetooth/scanners` | BLE scanners |
| `/iot/gpio` | GPIO pins |
| `/iot/modbus` | Modbus client |

---

## ZeroTier

**Menu:** `/zerotier` (requires `zerotier` package)

| Sub-path | Description |
|----------|-------------|
| `/zerotier` | Global settings |
| `/zerotier/interface` | Network interfaces |

---

## User Manager

**Menu:** `/user-manager` (requires `user-manager` package)

Built-in RADIUS server:
- `/user-manager/user` -- Users
- `/user-manager/user/group` -- Groups
- `/user-manager/profile` -- Service profiles
- `/user-manager/profile/limitation` -- Rate/time limits
- `/user-manager/router` -- NAS entries
- `/user-manager/session` -- Active sessions
- `/user-manager/database` -- Database settings

---

## IPv6

| Sub-path | Description |
|----------|-------------|
| `/ipv6/address` | IPv6 addresses |
| `/ipv6/route` | Static routes |
| `/ipv6/pool` | Address pools |
| `/ipv6/settings` | Kernel settings (forwarding, ECMP hash) |
| `/ipv6/firewall/filter` | Packet filter |
| `/ipv6/firewall/mangle` | Packet marking |
| `/ipv6/firewall/raw` | Raw rules |
| `/ipv6/firewall/address-list` | Address lists |
| `/ipv6/nd` | Neighbor Discovery / RA |
| `/ipv6/nd/prefix` | ND prefix advertisement |
| `/ipv6/dhcp-client` | DHCPv6 client |
| `/ipv6/dhcp-server` | DHCPv6 server |
| `/ipv6/dhcp-relay` | DHCPv6 relay |
| `/ipv6/neighbor` | Neighbor table |
