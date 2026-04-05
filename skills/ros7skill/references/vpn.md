# VPN and Tunnel Reference

## Table of Contents
1. [IPsec](#ipsec)
2. [WireGuard](#wireguard)
3. [L2TP](#l2tp)
4. [PPTP](#pptp)
5. [SSTP](#sstp)
6. [OpenVPN](#openvpn)
7. [GRE](#gre)
8. [EoIP](#eoip)
9. [IPIP](#ipip)
10. [VXLAN](#vxlan)

---

## IPsec

### /ip/ipsec/peer

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| address | IP/IPv6 prefix | 0.0.0.0/0 | Remote peer address |
| exchange-mode | aggressive\|base\|main\|ike2 | main | IKE exchange method |
| local-address | IP/IPv6 | -- | Local address binding |
| name | string | -- | Peer name |
| passive | yes\|no | no | Listen-only mode |
| port | integer | 500 | Communication port |
| profile | string | default | Profile reference |
| send-initial-contact | yes\|no | yes | Send initial contact |

### /ip/ipsec/profile

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| dh-group | modp768..modp8192, ecp256..ecp521 | modp1024,modp2048 | DH group |
| dpd-interval | time\|disable-dpd | 8s | Dead peer detection |
| enc-algorithm | 3des\|aes-128..256\|blowfish\|camellia\|des | aes-128 | Phase 1 encryption |
| hash-algorithm | md5\|sha1\|sha256\|sha512 | sha1 | Phase 1 hash |
| lifetime | time | 1d | Phase 1 lifetime |
| nat-traversal | yes\|no | yes | NAT-T |

### /ip/ipsec/proposal

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| auth-algorithms | md5\|null\|sha1\|sha256\|sha512 | sha1 | Phase 2 auth |
| enc-algorithms | null\|des\|3des\|aes-*-cbc/ctr/gcm\|blowfish\|camellia\|twofish | aes-256/192/128-cbc | Phase 2 encryption |
| lifetime | time | 30m | SA validity |
| pfs-group | ecp/modp groups\|none | modp1024 | PFS group |

### /ip/ipsec/identity

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| auth-method | digital-signature\|eap\|pre-shared-key\|rsa-key\|... | pre-shared-key | Auth method |
| certificate | string | -- | Certificate reference |
| generate-policy | no\|port-override\|port-strict | no | Dynamic policy |
| mode-config | none\|request-only\|string | none | Mode config ref |
| my-id | auto\|address\|fqdn\|user-fqdn\|key-id | auto | Local identity |
| peer | string | -- | Associated peer |
| secret | string | -- | Pre-shared key |

### /ip/ipsec/policy

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| action | discard\|encrypt\|none | encrypt | Action |
| dst-address | IP prefix | 0.0.0.0/32 | Destination match |
| src-address | IP prefix | 0.0.0.0/32 | Source match |
| ipsec-protocols | ah\|esp | esp | Protocol |
| level | require\|unique\|use | require | SA handling |
| peer | string | -- | Associated peer |
| proposal | string | default | Proposal reference |
| tunnel | yes\|no | no | Tunnel mode |
| template | yes\|no | no | Template policy |

### /ip/ipsec/mode-config

| Property | Type | Description |
|----------|------|-------------|
| address / address-pool | IP/pool | IP for initiator |
| responder | yes\|no | Server/client mode |
| split-include | IP prefix list | Subnets to tunnel |
| static-dns | IP list | DNS servers |
| system-dns | yes\|no | Use system DNS |

### Other IPsec Sub-menus
- `/ip/ipsec/keys` -- RSA/public keys
- `/ip/ipsec/active-peers` -- Active Phase 1 (read-only)
- `/ip/ipsec/installed-sa` -- Active SAs (read-only)
- `/ip/ipsec/statistics` -- Error counters
- `/ip/ipsec/settings` -- RADIUS integration

---

## WireGuard

**Menu:** `/interface/wireguard`
**Commands:** `add`, `set`, `remove`, `print`, `wg-import`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | string | -- | Interface name |
| listen-port | integer | 13231 | Listen port |
| mtu | integer | 1420 | MTU |
| private-key | base64 | (auto) | Private key |
| public-key | base64 | (read-only) | Public key |
| vrf | string | main | VRF for socket |

### /interface/wireguard/peers

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| interface | string | -- | Parent interface |
| public-key | base64 | -- | Peer's public key |
| endpoint-address | IP/hostname | -- | Remote endpoint |
| endpoint-port | integer | -- | Remote port |
| allowed-address | IP prefix list | -- | Allowed source addresses |
| preshared-key | base64 | -- | Optional PSK |
| persistent-keepalive | integer [1-65535] | 0 | Keepalive (seconds) |
| responder | yes\|no | no | Responder-only mode |
| client-address | IP prefix | -- | Address for QR import |
| client-endpoint | IP prefix | -- | Server address for client |
| client-dns | IP prefix | -- | DNS for client |

**Import:** `wg-import file=<path>` or `config-string="<config>"` (v7.19+)

---

## L2TP

### Client (`/interface/l2tp-client`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| connect-to | IP\|IPv6 | -- | Server address |
| user / password | string | -- | Credentials |
| profile | name | default-encryption | PPP profile |
| use-ipsec | yes\|no | no | Enable IPSec |
| l2tp-proto-version | l2tpv2\|l2tpv3-ip\|l2tpv3-udp | l2tpv2 | Protocol version |
| add-default-route | yes\|no | no | Add default route |
| allow | auth methods | mschap2,mschap1,chap,pap | Allowed auth |

### Server (`/interface/l2tp-server/server`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | no | Enable server |
| default-profile | name | default-encryption | PPP profile |
| authentication | auth methods | mschap1,mschap2 | Accepted auth |
| use-ipsec | no\|yes\|require | no | IPSec mode |
| ipsec-secret | string | -- | IPSec PSK |
| max-sessions | unlimited\|number | unlimited | Max sessions |

---

## PPTP

### Client (`/interface/pptp-client`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| connect-to | IP | -- | Server address |
| user / password | string | -- | Credentials |
| profile | name | default-encryption | PPP profile |
| add-default-route | yes\|no | no | Add default route |

### Server (`/interface/pptp-server/server`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | no | Enable server |
| default-profile | name | default-encryption | PPP profile |
| authentication | auth methods | mschap1,mschap2 | Accepted auth |

---

## SSTP

### Client (`/interface/sstp-client`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| connect-to | IP\|IPv6 | -- | Server address |
| port | integer | 443 | Port |
| certificate | string\|none | none | Client certificate |
| tls-version | any\|only-1.2 | any | TLS version |
| verify-server-certificate | yes\|no | no | Verify cert |
| pfs | yes\|no\|required | no | PFS |

### Server (`/interface/sstp-server/server`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | no | Enable server |
| port | integer | 443 | Listen port |
| certificate | string\|none | none | Server certificate |
| verify-client-certificate | yes\|no | no | Verify client cert |

---

## OpenVPN

### Client (`/interface/ovpn-client`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| connect-to | IP\|IPv6 | -- | Server address |
| port | integer | 1194 | Port |
| protocol | tcp\|udp | tcp | Transport |
| mode | ip\|ethernet | ip | Tunnel mode (tun/tap) |
| certificate | string\|none | none | Client certificate |
| auth | md5\|sha1\|null\|sha256\|sha512 | sha1 | Auth method |
| cipher | null\|aes128-cbc\|aes128-gcm\|aes256-cbc\|aes256-gcm\|... | blowfish128 | Cipher |
| verify-server-certificate | yes\|no | no | Verify cert |
| use-peer-dns | yes\|no | no | Use server DNS |

**Command:** `import-ovpn-configuration` -- Import .ovpn file

### Server (`/interface/ovpn-server`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| enabled | yes\|no | yes | Enable |
| port | integer | 1194 | Listen port |
| protocol | tcp\|udp | tcp | Transport |
| mode | ip\|ethernet | ip | Tunnel mode |
| certificate | name\|none | none | Server cert |
| redirect-gateway | def1\|disabled\|ipv6 | disabled | Push routes |
| push-routes | string | -- | Routes to push |
| require-client-certificate | yes\|no | no | Require client cert |
| vrf | name | -- | VRF (v7.17+) |

**Command:** `export-client-configuration` -- Generate .ovpn

**ROS7 changes:** VRF support (v7.17), TLS-crypt (v7.17rc3), GCM ciphers, multiple instances (v7.17).

---

## GRE

**Menu:** `/interface/gre` (RFC 1701)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| local-address | IP | 0.0.0.0 | Local endpoint |
| remote-address | IP | (required) | Remote endpoint |
| mtu | integer | 1476 | MTU |
| keepalive | interval,retries | 10s,10 | Keepalive |
| allow-fast-path | yes\|no | yes | FastPath (disable for IPsec) |
| ipsec-secret | string | (empty) | IPsec PSK |
| dont-fragment | inherit\|no | no | DF bit |

Also: `/interface/gre6` for GRE over IPv6.

---

## EoIP

**Menu:** `/interface/eoip` (MikroTik proprietary, RFC 1701)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| local-address | IP | -- | Local endpoint |
| remote-address | IP | -- | Remote endpoint |
| tunnel-id | integer [0-65536] | (required) | Must match both sides |
| mtu | integer | auto | MTU |
| keepalive | interval,retries | 10s,10 | Keepalive |

Also: `/interface/eoip6` for EoIP over IPv6.

---

## IPIP

**Menu:** `/interface/ipip` (RFC 2003)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| local-address | IP | -- | Local endpoint |
| remote-address | IP | -- | Remote endpoint |
| mtu | integer | 1500 | MTU |
| keepalive | interval,retries | 10s,10 | Keepalive |
| ipsec-secret | string | -- | IPsec PSK |

Also: `/interface/6to4` for 6to4 transition tunnels.

---

## VXLAN

**Menu:** `/interface/vxlan`

| Sub-path | Description |
|----------|-------------|
| `/interface/vxlan` | VXLAN tunnel interfaces |
| `/interface/vxlan/vteps` | Tunnel Endpoint config |
| `/interface/vxlan/fdb` | Forwarding database |
