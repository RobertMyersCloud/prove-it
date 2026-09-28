# NET-005 — Routing and Path Selection

## Hiring Claim

This artifact demonstrates that I can analyze Linux routing decisions, distinguish connected and default routes, apply longest-prefix match and route metrics correctly, create controlled static routes, validate the actual packet path, troubleshoot loss beyond a reachable next hop, and restore the system to its original routing state.

## Skills Demonstrated

- Linux routing-table analysis
- Connected vs default routes
- Longest-prefix match
- Route metric comparison
- Static host routes
- Dual-homed path selection
- Source-interface selection
- Packet-path validation with tcpdump
- Neighbor/next-hop validation
- Routing failure isolation
- Controlled change and restoration

## Environment

ENVY was dual-homed during testing.

| Interface | Address | Network |
|---|---|---|
| `enp0s20f0u3u3c2` | `10.10.20.101/24` | Lab |
| `wlo1` | `192.168.1.3/24` | Household/Internet |

The routing table contained two default routes:

```text
default via 192.168.1.1 dev wlo1 metric 600
default via 10.10.20.1 dev enp0s20f0u3u3c2 metric 20100
```

It also contained directly connected routes for both local networks.

## Experiment 1 — Connected Route vs Default Route

For Yoda (`10.10.20.10`), Linux selected:

```text
10.10.20.10 dev enp0s20f0u3u3c2 src 10.10.20.101
```

The `10.10.20.0/24` connected route was more specific than either `/0` default route.

For `1.1.1.1`, neither connected `/24` matched, leaving the two `/0` routes to compete. Because their prefix lengths were equal, the lower metric selected Wi-Fi:

```text
1.1.1.1 via 192.168.1.1 dev wlo1 src 192.168.1.3
```

![Baseline routing decisions](evidence/screenshots/01-longest-prefix-and-default-route.png)

### Finding

Routing selection was demonstrated as:

```text
Longest-prefix match
        ↓
competing equally specific routes
        ↓
metric / preference
        ↓
next hop and interface
```

A lower-metric default route does not override a more-specific route.

## Experiment 2 — Specific Route Overrides Default

A temporary host route was installed:

```text
1.1.1.1/32 via 10.10.20.1 dev enp0s20f0u3u3c2
```

Before the change, `1.1.1.1` used the Wi-Fi default with metric 600.

After the `/32` was installed, Linux selected:

```text
1.1.1.1 via 10.10.20.1 dev enp0s20f0u3u3c2 src 10.10.20.101
```

The `/32` won even though the competing Wi-Fi `/0` had a much lower metric.

Packet capture on the selected Ethernet interface observed:

```text
10.10.20.101 > 1.1.1.1: ICMP echo request
```

![Specific route packet validation](evidence/screenshots/02-specific-route-overrides-default.png)

### Finding

`ip route get` demonstrated the kernel's route decision while `tcpdump` independently demonstrated that packets actually followed the selected interface.

This separates **control-plane decision evidence** from **data-plane observation**.

## Experiment 3 — Route Restoration

After deleting the temporary `/32`, the destination immediately returned to:

```text
1.1.1.1 via 192.168.1.1 dev wlo1 src 192.168.1.3
```

![Route restored](evidence/screenshots/03-route-restored-to-default.png)

The controlled routing change was fully reversible.

## Experiment 4 — Route Exists but No Return Traffic

A second controlled `/32` was installed for the documentation address:

```text
203.0.113.10/32 via 10.10.20.1 dev enp0s20f0u3u3c2
```

Linux changed the selected path from Wi-Fi to the lab Ethernet interface.

The configured next hop was checked with the neighbor table and was:

```text
REACHABLE
```

The persistent MAC address was redacted from public evidence as `[ER605 MAC]`.

Two ICMP echo requests were transmitted, but no replies were received:

```text
2 packets transmitted
0 received
100% packet loss
```

![Route exists but no return traffic](evidence/screenshots/04-route-exists-but-no-return-traffic.png)

### Diagnosis

The evidence demonstrated:

```text
Local route exists                 YES
        ↓
Correct interface selected         YES
        ↓
Next hop reachable at Layer 2      YES
        ↓
Traffic transmitted                YES
        ↓
Return traffic observed            NO
```

The evidence therefore does not support the conclusion that ENVY lacked a route.

It supports the narrower conclusion that ENVY selected the configured route, reached its next hop locally, and transmitted traffic, while no return traffic was observed.

This localizes the observed failure beyond the demonstrated local route-selection and next-hop-reachability stages.

## Final Restoration

The temporary route was deleted.

Final route checks showed both test destinations again using:

```text
via 192.168.1.1 dev wlo1 src 192.168.1.3
```

No persistent routing changes were left behind.

## Evidence Index

| Evidence | Purpose |
|---|---|
| `01-longest-prefix-and-default-route.png` | Connected route, defaults, longest-prefix and metric behavior |
| `02-specific-route-overrides-default.png` | `/32` override plus actual packet-path evidence |
| `03-route-restored-to-default.png` | Controlled restoration |
| `04-route-exists-but-no-return-traffic.png` | Reachable next hop, transmitted traffic, and no return traffic |
| `routing-findings.txt` | Concise findings from the controlled routing experiments |

## Key Takeaway

A route being present does not prove end-to-end connectivity.

Effective troubleshooting separates:

**route selection → next-hop reachability → packet transmission → return path → destination/application behavior.**

## Status

**PROVEN**
