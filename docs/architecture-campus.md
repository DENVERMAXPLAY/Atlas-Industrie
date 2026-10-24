# Decision note - architecture of the Atlas Industrie campus

## 1. Context and requirements

Two buildings (A: head office and IT, B: production), 14 devices (2 cores,
2 firewalls, 10 access switches), 11 VLANs in trust zones.
- R1: production in B must continue if building A is isolated.
- R2: all inter-zone traffic is filtered by a firewall.
- R3: no single point of failure on the core and firewall functions.
- R4: availability target of 99.99 %.

## 2. Technical targets retained

- Two L3 cores (CC-A, CC-B), one per building, linked by two fibres on
  distinct routes. Each access switch is dual-homed to both cores.
- Firewall pair FW-A / FW-B in HA. The DMZ (VLAN 60) hangs off the firewalls.
- Default deny between IT and OT, written on one prefix (10.42.64.0/20).
- Unused ports are shut down in VLAN 99 (no address).

## 3. The two architectures compared

| | S1: three tiers | S2: distributed collapsed core |
|---|---|---|
| Layout | 2 modular cores + 4 distribution + 10 access | 2 fixed 10/25G cores + 10 access |
| Devices (with firewalls) | 18 | 14 |

S3 (collapsed core, both cores in A) fails R1. It is priced only to measure
what R1 costs.

## 4. Five-year costing

MAD excl. tax. TCO = CAPEX + 5 x (10 % of CAPEX + devices x 1.5 d x 5,400).

| | S1 | S2 | S3 |
|---|---|---|---|
| CAPEX | 1,155,600 | 766,800 | 745,200 |
| Support, 5 years | 577,800 | 383,400 | 372,600 |
| Operations, 5 years | 729,000 | 567,000 | 567,000 |
| **TCO** | **2,462,400** | **1,717,200** | **1,684,800** |

- S2 vs S1: S2 is 745,200 MAD cheaper (30 %).
- S2 vs S3: S2 costs 32,400 MAD more over 5 years.

## 5. Decision and justification

**Retained: S2.**
- Not S1: 745,200 MAD more and 4 extra devices to operate, with no gain in
  resilience (the access layer sets the ceiling).
- Not S3: it is cheaper but not equivalent, because isolating building A
  stops production in B. The 32,400 MAD is the price of requirement R1.
- Worth it: at 108,000 MAD per hour of outage, 32,400 MAD equals 18 minutes.
  S2 is paid back by avoiding a single 18-minute stoppage in 5 years.

## 6. Risks and points of failure

- **99.99 % is not demonstrated.** Doubled cores and firewalls give about
  99.9999 % each, but the non-redundant access switch (99.9 %) caps the
  chain near 99.90 %. Power supplies, fibre routes and failover mechanisms
  remain to be analysed.
- **Common causes.** In our assumed cabling plan the two fibre routes share no
  junction point (Route 1 via the cable chamber under the courtyard, Route 2
  via the south outer facade), but this is unproven without a site survey. The
  cores are fed by separate UPS (UPS A and UPS B), also an assumption. Both
  fibres still terminate in the same two technical rooms, and identical
  software on both cores means one bug hits both. Failures are not
  independent, so the theoretical figure overstates.
- **Firewall HA across buildings:** a cut inter-building link can cause
  split brain. A dedicated sync path is needed.
- **Collapsed core:** capacity limited by the 10/25G cores.

## 7. Assumptions and limits

- Teaching prices, 1 EUR = 10.8 MAD, to be replaced by quotations.
- 99.9 % availability per device and 108,000 MAD/h outage cost are assumptions.
- S1 is assumed to place its cores in A and B.
- Fibre routes, junction point and UPS come from the physical view
  (assumptions, not facts).
- Flat support and operations rates, no inflation or discounting.
- **Excluded:** power and cooling, UPS, civil works, network cards,
  spares, training, firewall subscriptions, taxes.