# NET-006 — NAT and CGNAT Path Analysis

## Hiring Claim

This artifact demonstrates that I can trace addressing and routing boundaries through a nested network, distinguish RFC1918 private addressing from RFC6598 carrier-grade NAT shared address space, identify an upstream CGNAT condition, recognize the limits of traceroute and available router telemetry, and document only what the collected evidence actually proves.

## Skills Demonstrated

- NAT/CGNAT path analysis
- RFC1918 private-address recognition
- RFC6598 shared-address recognition
- Default-gateway and route analysis
- Multi-router topology analysis
- WAN/LAN boundary identification
- Public-versus-WAN address comparison
- Traceroute interpretation and limitations
- Network segmentation awareness
- Evidence-scoped technical conclusions
- Privacy-conscious publication

## Environment

Observed addressing:

```text
Yoda 10.10.20.10/24
    |
    v
ER605 LAN 10.10.20.1
    |
    v
ER605 WAN 192.168.1.177/24
    |
    v
AX55 LAN 192.168.1.1/24
    |
    v
AX55 WAN 100.67.28.24/22
    |
    v
ISP / CGNAT
    |
    v
Public Internet
```

Yoda is intentionally restricted from Internet access by lab security policy. That control was not weakened for this experiment.

## Experiment 1 — Yoda Route and First Hop

Yoda reported `10.10.20.10/24` with a default route through `10.10.20.1`.

A route lookup for `1.1.1.1` selected:

```text
1.1.1.1 via 10.10.20.1 dev vmbr0 src 10.10.20.10
```

Both conventional and TCP/443 traceroute identified the ER605 as the first responding hop. Subsequent hops did not respond.

![Yoda first-hop routing and traceroute](evidence/screenshots/01-yoda-first-hop-traceroute.png)

### Finding

The evidence proves that Yoda selects `10.10.20.1` as its first routed hop for an off-subnet destination. The nonresponsive hops after the ER605 are not identified.

## Experiment 2 — ER605 WAN Boundary

The ER605 status interface showed:

```text
WAN IPv4:        192.168.1.177
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
Connection:      Dynamic IP
```

![ER605 WAN private address](evidence/screenshots/02-er605-wan-private-address.png)

The ER605 WAN MAC address was redacted from the public screenshot.

### Finding

This establishes the observed addressing relationship between the `10.10.20.0/24` lab and the upstream `192.168.1.0/24` network. The status page establishes addressing and topology; it is not a packet-level NAT translation record.

## Experiment 3 — AX55 CGNAT-Space WAN Address

The AX55 status interface showed:

```text
LAN IPv4:        192.168.1.1/24
WAN IPv4:        100.67.28.24
WAN Mask:        255.255.252.0
Default Gateway: 100.67.28.1
Connection:      Dynamic IP
```

![AX55 CGNAT WAN address](evidence/screenshots/03-ax55-cgnat-wan-address.png)

The AX55 MAC address was redacted from public evidence.

`100.67.28.24` falls within `100.64.0.0/10`, the shared address space used for carrier-grade NAT.

### Finding

The AX55 does not hold a globally routable public IPv4 address on its WAN interface. Its WAN address is inside CGNAT shared address space.

## Experiment 4 — WAN Address vs Internet-Visible Address

ENVY, which legitimately has Internet access through the household Wi-Fi network, was used as the outer observation host.

Its route selected:

```text
via 192.168.1.1 dev wlo1 src 192.168.1.3
```

The AX55 WAN address was compared with the IPv4 observed by an external Internet service:

```text
AX55 WAN IPv4:          100.67.28.24
Internet-visible IPv4:  [PUBLIC-IP]
WAN/public match:       NO
```

![CGNAT WAN versus public IP](evidence/screenshots/04-cgnat-wan-vs-public-ip.png)

The exact public IPv4 is retained locally and redacted from the repository.

### Finding

Two independent facts support the CGNAT conclusion:

1. The AX55 WAN address is within `100.64.0.0/10`.
2. The Internet-visible IPv4 is different from the AX55 WAN IPv4.

Together, these demonstrate an upstream carrier translation boundary between the AX55 and the public Internet.

## Experiment 5 — AX55 Routing Table

The AX55 routing table showed:

```text
0.0.0.0/0       via 100.67.28.1   WAN
100.67.28.0/22   connected         WAN
192.168.1.0/24   connected         LAN
```

![AX55 routing table and CGNAT upstream](evidence/screenshots/05-ax55-routing-table-cgnat-upstream.png)

### Finding

The routing table independently corroborates the AX55 topology from `192.168.1.0/24` to `100.67.28.0/22` and then through the default gateway `100.67.28.1`.

## Security-Control Observation

An attempted public-IP lookup from Yoda timed out during name resolution. This behavior was consistent with the existing lab policy that intentionally restricts Yoda's Internet access.

The security control was left intact. ENVY was used for the public-side comparison instead of weakening isolation simply to complete the experiment.

## Evidence Boundaries

The collected evidence directly establishes:

- Yoda address `10.10.20.10/24`
- Yoda default gateway `10.10.20.1`
- ER605 WAN `192.168.1.177/24`
- ER605 upstream gateway `192.168.1.1`
- AX55 LAN `192.168.1.1/24`
- AX55 WAN `100.67.28.24/22`
- AX55 upstream gateway `100.67.28.1`
- AX55 WAN membership in `100.64.0.0/10`
- a different Internet-visible public IPv4
- an upstream CGNAT condition

The collected evidence does not include a router NAT/session table showing a live mapping such as `inside-address:port -> translated-address:port`.

Yoda also did not generate an allowed end-to-end Internet flow through every boundary because its isolation policy remained enabled.

Accordingly, this artifact does not claim packet-level observation of every possible translation from Yoda to the Internet.

## Evidence Index

| Evidence | Purpose |
|---|---|
| `01-yoda-first-hop-traceroute.png` | Yoda addressing, route lookup, and ER605 first hop |
| `02-er605-wan-private-address.png` | ER605 WAN address and upstream private-network gateway |
| `03-ax55-cgnat-wan-address.png` | AX55 LAN/WAN boundary and RFC6598 shared-space WAN |
| `04-cgnat-wan-vs-public-ip.png` | AX55 WAN versus redacted Internet-visible IPv4 |
| `05-ax55-routing-table-cgnat-upstream.png` | AX55 connected networks and CGNAT-side default route |
| `nat-cgnat-findings.txt` | Concise evidence-derived findings |

## Key Takeaway

The strongest CGNAT proof in this experiment was the combination of an AX55 WAN address inside `100.64.0.0/10` and a different IPv4 observed from the public Internet.

## Status

**PROVEN**
