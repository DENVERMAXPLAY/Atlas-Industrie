# Atlas Industrie -- network architecture repository

Design of the Atlas Industrie network, the fictitious company of the
"Advanced Network Architectures" course. One folder per object type;
theaddressing plan is authoritative in `data/vlans.yml`.

Localcheck: `python3 scripts/check_vlans.py` from the repository root.

IPv6 ULA prefix : fd51:de79:be42::/48

Limits of check_vlans.py: it detects duplicate VLAN IDs and IPv4 overlaps only.
It does not validate IPv6 prefixes, mandatory fields, or the consistency
between VLAN, zone and subnet.

Git pre-commit hook
The hook is stored in hooks/ instead of .git/hooks/ because .git/hooks/ is not versioned and is not included when cloning the repository.

After cloning, run: git config core.hooksPath hooks
