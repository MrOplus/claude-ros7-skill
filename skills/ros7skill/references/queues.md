# /queue -- QoS / Bandwidth Management Reference

---

## /queue/simple

**Menu:** `/queue/simple`
**Commands:** `add`, `set`, `remove`, `print`

The simplest way to limit bandwidth for a specific target (IP, subnet, or interface).

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | text | -- | Queue identifier |
| target | IP/mask or interface | -- | Target for upload/download |
| dst-address | IP/mask | -- | Destination stream limitation |
| interface | name | -- | Queue attachment interface |
| parent | text\|none | -- | Parent queue |
| priority | 1-8 / 1-8 | -- | Priority (1=highest) |
| limit-at | number/number | -- | Guaranteed rate (upload/download) |
| max-limit | number/number | -- | Maximum rate (upload/download) |
| burst-limit | number/number | -- | Peak burst rate |
| burst-time | time/time | -- | Burst average calculation period |
| burst-threshold | number/number | -- | Burst activation threshold |
| queue | type/type | -- | Queue type (upload/download) |
| packet-marks | CSV list | -- | Firewall mangle marks |
| time | TIME-TIME,days | -- | Active time window |
| total-queue | type | -- | Global queue type |
| total-limit-at | number | -- | Global guaranteed rate |
| total-max-limit | number | -- | Global maximum rate |
| total-burst-limit/time/threshold | -- | -- | Global burst settings |

**Read-only stats:** rate, packet-rate, bytes, packets, queued-bytes, queued-packets, dropped, borrows, lends, pcq-queues

---

## /queue/tree

**Menu:** `/queue/tree`
**Commands:** `add`, `set`, `remove`, `print`

Hierarchical queuing -- requires mangle marks to classify traffic.

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| name | text | -- | Queue identifier |
| parent | interface\|queue name | -- | Parent attachment point |
| packet-mark | mark name | -- | Firewall mangle mark |
| max-limit | number | -- | Maximum bandwidth |
| limit-at | number | -- | Guaranteed minimum |
| priority | 1-8 | -- | Queue priority |
| burst-limit | number | -- | Burst rate |
| burst-time | time | -- | Burst period |
| burst-threshold | number | -- | Burst threshold |

---

## /queue/type

**Pre-defined types:**

| Name | Kind | Key Parameters |
|------|------|----------------|
| default | pfifo | pfifo-limit=50 |
| ethernet-default | pfifo | pfifo-limit=50 |
| wireless-default | sfq | sfq-perturb=5, sfq-allot=1514 |
| synchronous-default | red | red-limit=60 |
| hotspot-default | sfq | sfq-perturb=5, sfq-allot=1514 |
| pcq-upload-default | pcq | pcq classifiers |
| pcq-download-default | pcq | pcq classifiers |
| only-hardware-queue | none | Hardware buffer only |
| multi-queue-ethernet-default | mq-pfifo | mq-pfifo-limit=50 |
| default-small | pfifo | pfifo-limit=10 |

### Queue Disciplines

**PFIFO** -- Simple packet FIFO
- `pfifo-limit` -- Max packet count

**BFIFO** -- Byte FIFO
- `bfifo-limit` -- Max bytes

**SFQ** -- Stochastic Fairness Queuing
- `sfq-perturb` -- Hash interval (seconds)
- `sfq-allot` -- Bytes per round

**RED** -- Random Early Detection
- `red-limit`, `red-min-threshold`, `red-max-threshold`, `red-burst`, `red-avg-packet`

**PCQ** -- Per Connection Queuing
- `pcq-classifier` -- dst-address\|src-address\|dst-port\|src-port
- `pcq-rate` -- Per-connection rate limit
- `pcq-limit` -- Per-connection queue size
- `pcq-total-limit` -- Total queue size
- `pcq-burst-rate`, `pcq-burst-threshold`, `pcq-burst-time`
- `pcq-src-address-mask` / `pcq-dst-address-mask` -- Grouping mask

**CoDel** -- Controlled Delay
- `codel-target` -- Target delay (default 5ms)
- `codel-interval` -- Interval (default 100ms)
- `codel-limit` -- Queue limit (default 1000)
- `codel-ecn` -- ECN support (default no)

**FQ-CoDel** -- Fair Queue CoDel
- `fq-codel-target` -- Target delay (default 5ms)
- `fq-codel-interval` -- Interval (default 100ms)
- `fq-codel-limit` -- Queue limit (default 10240)
- `fq-codel-flows` -- Flow count (default 1024)
- `fq-codel-quantum` -- Bytes per round (default 1514)
- `fq-codel-memlimit` -- Memory limit (default 32MiB)
- `fq-codel-ecn` -- ECN support (default yes)

**CAKE** -- Common Applications Kept Enhanced (new in ROS7)
- `cake-bandwidth` -- Link bandwidth
- `cake-rtt` -- Base RTT (default 100ms)
- `cake-rtt-scheme` -- RTT estimation
- `cake-diffserv` -- DiffServ handling (diffserv3)
- `cake-flowmode` -- Flow isolation (triple-isolate)
- `cake-memlimit` -- Memory limit
- `cake-overhead` -- Per-packet overhead bytes
- `cake-ack-filter` -- ACK filtering
- `cake-atm` -- ATM cell compensation
- `cake-autorate-ingress` -- Auto bandwidth detection
- `cake-wash` -- Clear DSCP on egress
- `cake-nat` -- NAT-aware flow hashing

---

## /queue/interface

Per-interface default queue type assignment.
