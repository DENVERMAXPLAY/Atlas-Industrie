# Decision note - architecture of the Atlas Industrie campus

## 1. Context and requirements

Two buildings (A: head office and IT, B: production), 14 devices (2 cores,
2 firewalls, 10 access switches), 11 VLANs in trust zones.
- R1: production in B must continue if building A is isolated.
- R2: all inter-zone traffic is filtered by a firewall.
- R3: no single point of failure on the core and firewall functions.
- R4: availability target of 99.99 % for production.

## 2. Technical targets retained

- Two L3 cores (CC-A, CC-B), one per building, linked by two fibres on
  distinct routes. Each access switch is dual-homed to both cores.
- OT gateways and production supervision are carried by CC-B, so production
  stays local to B.
- Firewall pair FW-A / FW-B in HA, one device per building. All inter-zone
  flows cross it. The DMZ (VLAN 60) hangs off the firewalls.
- Default deny between IT and OT, written on one prefix (10.42.64.0/20).
  Only named, logged exceptions are allowed.
- Unused ports are shut down in VLAN 99 (no address).
- A single Internet access, through building A.

## 3. The two architectures compared

| | S1: three tiers | S2: distributed collapsed core |
|---|---|---|
| Layout | 2 modular cores + 4 distribution + 10 access | 2 fixed 10/25G cores + 10 access |
| Devices (with firewalls) | 18 | 14 |
| Hops, access to core | 2 | 1 |
| Survives the loss of building A | Yes (cores in A and B) | Yes |
| Scalability | Strong: add distribution blocks | Limited by core ports |
| Operations | More devices, more complex | Simpler |

S3 (collapsed core, both cores in A) fails R1: if A is lost, B loses its
gateways and routing. It is priced only to measure what R1 costs.

## 4. Five-year costing

MAD excl. tax. TCO = CAPEX + 5 x (10 % of CAPEX + devices x 1.5 d x 5,400).

| | S1 | S2 | S3 |
|---|---|---|---|
| Cores | 324,000 | 172,800 | 172,800 |
| Distribution (4 x 54,000) | 216,000 | - | - |
| Access (10 x 27,000) | 270,000 | 270,000 | 270,000 |
| Optics and fibre | 129,600 | 108,000 | 86,400 |
| Firewalls (pair) | 216,000 | 216,000 | 216,000 |
| **CAPEX** | **1,155,600** | **766,800** | **745,200** |
| Devices | 18 | 14 | 14 |
| Support, 5 years | 577,800 | 383,400 | 372,600 |
| Operations, 5 years | 729,000 | 567,000 | 567,000 |
| **TCO** | **2,462,400** | **1,717,200** | **1,684,800** |

- S2 vs S1: S2 is 745,200 MAD cheaper (30 %).
- S2 vs S3: S2 costs 32,400 MAD more over 5 years (about 2 %).

## 5. Decision and justification

**Retained: S2.**
- Not S1: 745,200 MAD more and 4 extra devices to operate. It adds no
  resilience that matters at this scale, because the non-redundant access
  layer sets the availability ceiling.
- Not S3: it is cheaper but not equivalent, because isolating building A
  stops production in B. The 32,400 MAD is the price of requirement R1.
- Worth it: at 108,000 MAD per hour of outage, 32,400 MAD equals 18 minutes.
  S2 is paid back by avoiding a single 18-minute stoppage in 5 years.

## 6. Risks and points of failure

- **99.99 % is not demonstrated.** With every device at 99.9 %, a path with no
  redundancy (access, core, firewall) gives 99.70 % (26.3 h of downtime per
  year). Doubling the core and the firewall gives 99.90 % (8.8 h per year),
  because the non-redundant access switch caps the chain. This stays below
  the 99.99 % target. Power supplies, fibre routes and failover mechanisms
  remain to be analysed, and the access switch of critical OT devices should
  be dual-attached.
- **Common causes.** Failures are not independent, so the theoretical figure
  overstates. In our assumed cabling plan, the two fibre routes (north
  technical gallery, south outer facade) meet again in the cable chamber
  under the courtyard: one digger cuts both. The cores are fed by separate
  UPS (UPS A and UPS B). Both fibres also terminate in the same two technical
  rooms, and identical software on both cores means one bug hits both.
- **Firewall HA across buildings:** a cut inter-building link can cause
  split brain. A dedicated sync path is needed, and production must not
  depend on the firewall pair.
- **Single Internet access through A:** if A is lost, B has no Internet and
  no vendor remote maintenance. This is accepted by the business, since
  production does not depend on it. It is to be revisited in the WAN chapter.
- **Collapsed core:** capacity limited by the 10/25G cores.

## 7. Assumptions and limits

- Teaching prices, 1 EUR = 10.8 MAD, to be replaced by quotations.
- 99.9 % availability per device and 108,000 MAD/h outage cost are assumptions.
- S1 is assumed to place its cores in A and B.
- Fibre routes, junction point and UPS come from the physical view
  (assumptions, not facts; a site survey is needed to confirm them).
- Flat support and operations rates, no inflation or discounting.
- **Excluded:** power and cooling, UPS, civil works, network cards,
  spares, training, firewall subscriptions, taxes.