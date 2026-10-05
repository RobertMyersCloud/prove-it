# NET-005 — Routing and Path Selection

## Hiring Claim

> After reviewing this artifact, a hiring manager has evidence that I can read a Linux routing table on a dual-homed host, apply longest-prefix match and route metrics to explain which path the kernel picks, override that choice with a static host route, confirm with `tcpdump` which interface the traffic actually left on, and remove the change afterward.

## Skills Demonstrated

- Linux routing-table analysis
- Connected vs default routes
- Longest-prefix match
- Route metric comparison
- Static host routes
- Dual-homed path selection
- Source-address selection (from `ip route get`)
- Packet-path validation with tcpdump
- Neighbor/next-hop validation
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

The lab default carries metric `20100`. NetworkManager adds 20000 to a default route's metric when its connectivity check fails (documented in NET-008), and the lab network has no internet egress by design: the ER605 rule `DENY_Lab_to_Any` blocks lab-to-WAN traffic (LAB-001; NET-008 evidence 24, `24-er605-acl-rules-before.png`). A base metric of 100 plus that penalty is consistent with ENVY's lab link failing its connectivity check, but I didn't capture NetworkManager's connectivity state here.

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

Correction (October 5, 2026): this experiment proves route selection, not reachability. Screenshot 02 shows two echo requests leaving `enp0s20f0u3u3c2` and no replies in the capture. The lab has no internet egress by design (`DENY_Lab_to_Any`), so replies weren't expected. The result shows where the kernel sent the traffic, nothing more.

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

The MAC address is redacted as `[ER605-MAC]` (see Evidence Handling).

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

It supports the narrower conclusion that ENVY selected the configured route and its next hop was `REACHABLE` in the neighbor table. The neighbor check ran after the ping, as shown in screenshot 04.

Correction (October 5, 2026): this test can't tell me where the loss happened. `203.0.113.10` is in TEST-NET-3 (`203.0.113.0/24`, RFC 5737), a documentation range, so no host was going to answer. On top of that, the ER605 blocks lab traffic to the internet (`DENY_Lab_to_Any`). With both of those true, "no return traffic" can't distinguish a path problem from a destination that doesn't exist. I also didn't run `tcpdump` during this test, so "Traffic transmitted" above comes from ping's `2 packets transmitted` count, not from a capture on the wire. What this experiment does show is the route change and the next-hop state.

## Final Restoration

The temporary route was deleted.

Final route checks showed both test destinations again using:

```text
via 192.168.1.1 dev wlo1 src 192.168.1.3
```

No persistent routing changes were left behind.

Screenshot 03 shows `1.1.1.1` back on `wlo1`. I didn't capture the route deletion or the restored `ip route get` for `203.0.113.10`.

## Evidence Limits

- Experiment 2 shows two echo requests leaving the lab NIC. It does not show reachability; replies weren't expected because the lab has no internet egress.
- Experiment 4: no `tcpdump` was captured, and the destination is a documentation address, so the test doesn't localize any failure.
- I didn't capture the `ip route del` commands or the restored `ip route get 203.0.113.10`.
- I didn't capture NetworkManager's connectivity state, so the reason for metric `20100` is inferred.

## Evidence Handling

The ER605's MAC address is redacted in screenshot 04. The label in the image reads `[ER605 MAC]`; in this README and other text I refer to it as `[ER605-MAC]`. Private lab and household addresses were kept because the routing decisions depend on them.

## Evidence Index

| Evidence | Purpose |
|---|---|
| `01-longest-prefix-and-default-route.png` | Connected route, defaults, longest-prefix and metric behavior |
| `02-specific-route-overrides-default.png` | `/32` override plus actual packet-path evidence |
| `03-route-restored-to-default.png` | Controlled restoration |
| `04-route-exists-but-no-return-traffic.png` | `/32` route change, ping with no replies, and `REACHABLE` next hop |
| `routing-findings.txt` | Concise findings from the controlled routing experiments |

## Key Takeaway

A route being present does not prove end-to-end connectivity.

Effective troubleshooting separates:

**route selection → next-hop reachability → packet transmission → return path → destination/application behavior.**

## Status

**PROVEN**

Longest-prefix match, metric tie-breaking, the `/32` override, and the interface the traffic left on are all shown in command output and `tcpdump`. Experiment 4 is limited: it shows the route change and next-hop state, not where traffic was lost.
