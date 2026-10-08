# NET-007 — VLAN Segmentation and Policy Enforcement

> **Correction — October 5, 2026.** The isolation in this project covers one direction of one path: VLAN 30 to the lab LAN (`10.10.20.0/24`). When I re-tested in NET-008, VLAN 30 could still reach the household network and the internet, because I never added it to `GRP_LabNet`, the group the existing egress rules use ([NET-008 Finding 3](../NET-008-protected-systems-enclave/README.md#finding-3--vlan30-had-household-and-internet-egress-through-the-er605)). The VLAN 30 DHCP pool I set up here also handed out `10.10.31.1` as Primary DNS, a typo ([NET-008 Finding 1](../NET-008-protected-systems-enclave/README.md#finding-1--vlan30-dhcp-handed-out-a-nonexistent-dns-server)). Both are fixed in NET-008.

## Hiring Claim
After reviewing this artifact, a hiring manager has evidence that I can design and implement VLAN segmentation, configure tagged and untagged switch ports, verify DHCP and Layer-3 inter-VLAN routing, distinguish Layer-2 neighbor behavior from routed communication, and block VLAN 30 from reaching the lab LAN with a LAN-to-LAN ACL.

## Skills Demonstrated
- 802.1Q VLAN configuration
- Tagged uplink and untagged access-port design
- PVID configuration
- VLAN-aware DHCP
- Inter-VLAN routing
- ARP / neighbor-table analysis
- Layer-2 versus Layer-3 troubleshooting
- LAN-to-LAN ACL enforcement
- Before/after policy validation
- Controlled infrastructure change
- Port mirroring and passive packet capture
- Switch port hardening

## Environment
The existing lab network was preserved while a second routed VLAN was introduced.

| Component | Address / Role |
|---|---|
| Existing LAN / VLAN 1 | `10.10.20.0/24` |
| ER605 gateway | `10.10.20.1` |
| Yoda | `10.10.20.10` |
| VLAN 30 | `10.10.30.0/24` |
| VLAN 30 gateway | `10.10.30.1` |
| ENVY after cutover | `10.10.30.100` |

```text
ER605 Port 3
     |
     | VLAN 1 untagged
     | VLAN 30 tagged
     v
SG108E Port 1
     +---------------------+
     |                     |
SG108E Port 2         SG108E Port 3
VLAN 1                VLAN 30 / PVID 30
     |                     |
   Yoda                  ENVY
10.10.20.10          10.10.30.100
```

## Baseline
Before segmentation, ENVY and Yoda shared `10.10.20.0/24`. ENVY used `10.10.20.101/24` and could reach both the ER605 gateway and Yoda. The SG108E initially had 802.1Q disabled and all ports used PVID 1.

The existing network was preserved rather than redesigning it merely to make a VLAN ID match the subnet number.

## VLAN 30 Creation
A second LAN interface was created on the ER605:

```text
Name: VLAN30
VLAN ID: 30
IP Address: 10.10.30.1
Subnet Mask: 255.255.255.0
DHCP Server: Enabled
DHCP Range: 10.10.30.100-10.10.30.199
Default Gateway: 10.10.30.1
Primary DNS: 10.10.31.1   (typo; fixed to 10.10.30.1 in NET-008)
```

The DHCP settings are shown in NET-008 [evidence 18](../NET-008-protected-systems-enclave/evidence/18-er605-vlan30-dhcp-dns-before.png) (as I set them here) and [evidence 19](../NET-008-protected-systems-enclave/evidence/19-er605-vlan30-dhcp-dns-after.png) (after the fix). Both were captured October 5, 2026; I didn't capture the form when I created VLAN 30.

The existing VLAN 1 / `10.10.20.0/24` network remained intact. The ER605 carried VLAN 30 as tagged traffic while retaining VLAN 1 as untagged traffic on the lab uplink.

## SG108E VLAN Membership
802.1Q VLAN support was enabled on the managed switch.

| Port | Device / Role | VLAN 30 |
|---|---|---|
| Port 1 | ER605 uplink | Tagged |
| Port 2 | Yoda | Not Member |
| Port 3 | ENVY | Untagged |
| Ports 4-8 | Not used for VLAN 30 | Not Member |

The resulting VLAN 30 membership was Port 1 tagged and Port 3 untagged.

![VLAN 30 membership](evidence/screenshots/03-sg108e-vlan30-membership.png)

## Access-Port PVID
Port 3 connects to ENVY. Its PVID was changed from `1` to `30`, causing untagged frames entering Port 3 to be classified as VLAN 30 traffic.

![Port 3 PVID 30](evidence/screenshots/04-sg108e-port3-pvid30.png)

## DHCP Cutover
After the PVID change, ENVY's Ethernet connection was disconnected and reactivated. ENVY received:

```text
10.10.30.100/24
Gateway: 10.10.30.1
```

Its connected Ethernet route changed from `10.10.20.0/24` to `10.10.30.0/24`.

This validated the path:

```text
ENVY
  | untagged
  v
SG108E Port 3 / PVID 30
  | VLAN 30
  v
SG108E Port 1
  | tagged VLAN 30
  v
ER605 Port 3
  v
10.10.30.1
```

The address and gateway show the path works end to end. The tagging and the ER605 port in this diagram come from the configuration; see [Evidence Limits](#evidence-limits).

## Inter-VLAN Routing Before Policy
VLAN separation created a separate Layer-2 broadcast domain, but it did not automatically prevent Layer-3 communication.

Before applying an ACL, ENVY successfully reached Yoda.

```text
ENVY: 10.10.30.100
Yoda: 10.10.20.10
Route: 10.10.20.10 via 10.10.30.1
ICMP: 2 transmitted, 2 received, 0% loss
```

The replies arrived with TTL 63, consistent with traffic crossing a Layer-3 routing boundary.

![Inter-VLAN routing before policy](evidence/screenshots/01-vlan30-intervlan-routing-before-policy.png)

## Layer-2 Neighbor Behavior
ENVY did not maintain a direct neighbor entry for Yoda at `10.10.20.10`. Instead, it maintained a reachable Layer-2 neighbor for its local gateway at `10.10.30.1`.

ENVY recognized Yoda as off-subnet and forwarded traffic to its gateway rather than ARPing directly for Yoda.

```text
ENVY 10.10.30.100
        |
        v
Gateway 10.10.30.1
        |
        | Layer-3 routing
        v
Yoda 10.10.20.10
```

The gateway hardware address visible in terminal evidence was redacted before publication.

## Policy Enforcement
An IPv4 LAN-to-LAN ACL was created on the ER605:

```text
Name: DENY_VLAN30_TO_LAB
Policy: Block
Service: ALL
IP Type: IPv4
Direction: LAN->LAN
Source Network: VLAN30
Destination Network: LAN
Effective Time: Any
States: New, Established, Invalid, Related
```

![VLAN30 to LAN ACL](evidence/screenshots/05-er605-vlan30-to-lab-acl.png)

The form's States field is set to `New, Established, Invalid, Related`. This project doesn't test how the ER605 applies that setting to reply traffic. NET-008 shows that SSH started from the lab LAN to VLAN 30 still works with this rule in place.

This screenshot shows the rule as it was being created. The saved rule in the ER605 policy table is shown in [NET-008 evidence 07](../NET-008-protected-systems-enclave/evidence/07-er605-vlan30-to-lab-deny-rule.png).

## Post-Policy Validation
After the ACL was applied, the VLAN 30 gateway remained reachable:

```text
ENVY -> 10.10.30.1
2 transmitted, 2 received, 0% loss
```

Yoda became unreachable from VLAN 30:

```text
ENVY -> 10.10.20.10
2 transmitted, 0 received, 100% loss
```

The route still existed:

```text
10.10.20.10 via 10.10.30.1
```

![ACL isolation after policy](evidence/screenshots/02-vlan30-acl-isolation-after-policy.png)

The failure was therefore not caused by a missing route, failed VLAN, failed switch port, or unavailable VLAN gateway. The ACL intentionally changed what traffic was permitted across the routed boundary.

## Before vs. After

| Test | Before ACL | After ACL |
|---|---|---|
| ENVY address | `10.10.30.100/24` | `10.10.30.100/24` |
| VLAN 30 gateway reachable | Yes | Yes |
| Route to Yoda exists | Yes | Yes |
| ENVY can ping Yoda | Yes | No |
| Direct Yoda L2 neighbor on ENVY | No | No |
| VLAN 30 operational | Yes | Yes |
| VLAN30 -> LAN policy | Permitted | Blocked |

## What the Lab Proved
**Layer-2 segmentation:** VLAN 30 created a separate broadcast domain.

**Layer-3 routing:** because the ER605 had interfaces in both networks, it could route between `10.10.30.0/24` and `10.10.20.0/24` before policy enforcement.

**Security policy:** the LAN-to-LAN ACL prevented VLAN 30 from reaching the existing lab LAN without destroying the underlying route or VLAN.

This separates two different questions:

```text
Can the network route the packet?
Should security policy permit the packet?
```

## Troubleshooting Record

**Expected:** VLAN 30 should not reach the lab LAN.
**Observed:** with the VLAN in place and no ACL, ENVY pinged Yoda with 2 of 2 replies at TTL 63, routed via `10.10.30.1` (screenshot 01). The VLAN alone did not stop routed traffic.
**Change:** I created `DENY_VLAN30_TO_LAB` on the ER605 (screenshot 05).
**Validation:** ENVY still reached `10.10.30.1` (2 of 2), Yoda failed (0 of 2, 100% loss), and the route via `10.10.30.1` was still there (screenshot 02). The loss came from the policy, not from routing or the VLAN.

## Re-test: 802.1Q Tag Captured on the Trunk (October 7, 2026)

The original build showed VLAN 30 tagging only as switch configuration. This re-test puts a capture box on a mirror of the Port 1 trunk and records the tag on the wire.

### Capture setup

The capture box is the XPS (Kali) with a USB-C Ethernet adapter (ASIX AX88179). The adapter didn't show up in `lsusb` at first. It appeared after I reconnected it, and the kernel created `eth0` on its own.

**What went wrong first.** I plugged the XPS into Port 5 before setting up the mirror. Port 5 was enabled but unused, so it was a normal VLAN 1 access port. NetworkManager brought the link up and the ER605 handed the XPS a lease: `kali`, `10.10.20.101` (evidence 07). For a few minutes the capture box was a full member of the lab LAN. I pulled the cable and logged it as GAP-008 (fixed below).

**Making the capture box silent.** Before it went back on the wire:

| Step | Command | Result |
|---|---|---|
| Stop NetworkManager from managing the adapter | `sudo nmcli device set eth0 managed no` | `eth0` shows `unmanaged` |
| Stop IPv6 address autoconfiguration on it | `sudo sysctl -w net.ipv6.conf.eth0.disable_ipv6=1` | `= 1` |
| Bring the link up with no address | `sudo ip link set eth0 up` | `NO-CARRIER,…,UP`, no `inet` or `inet6` lines |

Evidence 08. None of these survive a reboot, so nothing is left on the XPS afterward.

**Mirror.** SG108E Port Mirror enabled, mirroring port 5; Port 1 mirrored on ingress and egress (evidence 09). Port 1 is the uplink to the ER605 and the only link that carries VLAN 30 tagged. With the cable back in Port 5, `eth0` showed `UP,LOWER_UP` and still had no address.

### Result

```text
sudo tcpdump -i eth0 -e -nn -c 10 'host 10.10.30.100 or (vlan 30 and host 10.10.30.100)'
```

The filter matches ENVY's traffic with or without a tag, so untagged frames would also have shown up if the tag were being stripped somewhere.

All 10 frames were tagged: `ethertype 802.1Q (0x8100), length 78: vlan 30, p 0, ethertype IPv4`. Source was ENVY, destination the ER605's VLAN 30 interface. `0 packets dropped by kernel` (evidence 10). A second capture in NET-008 also shows the ER605's replies to ENVY tagged `vlan 30` on the same trunk ([NET-008 evidence 59](../NET-008-protected-systems-enclave/evidence/59-xps-tcpdump-envy-correlated-photo.jpg)).

The frames were ENVY's own background traffic, not a test I generated. What that traffic was is covered in [NET-008](../NET-008-protected-systems-enclave/README.md#background-egress-attempts--october-7-2026).

### Putting the switch back

| Change | Evidence |
|---|---|
| Port Mirror disabled; every port's ingress and egress mirroring set to Disable | 11 |
| Ports 5–8 (unused, Link Down) disabled. Ports 1–4 left Enabled at 1000MF | 06 before / 12 after |
| XPS plugged back into Port 5: `eth0 DOWN <NO-CARRIER,…,UP>`, no link | 13 |

The same action that got a DHCP lease at the start now gets no link.

### Notes from the session

- **The SG108E doesn't answer ping.** `ping 10.10.20.100` timed out while the ER605 at `10.10.20.1` answered, and kept timing out after the web interface was working again. Ping can't be used to check whether this switch is up.
- **The web interface hung during the cleanup.** It timed out until I cleared the browser's cookies for it. The switch's address didn't change.
- **The switch has no save-config option.** Changes appear to be saved as they're applied. I haven't confirmed that they survive a power loss.

### Re-test Evidence Limits

- Evidence 08, 10 and 13 are phone photos of the XPS screen. MAC addresses in them are masked.
- The capture is on the Port 1 trunk only. I didn't capture on Port 3, so the untagged side of ENVY's access port is still shown by configuration only.
- Persistence of the port changes across a switch power loss hasn't been tested.

## Evidence Index

| Evidence | Purpose |
|---|---|
| `01-vlan30-intervlan-routing-before-policy.png` | Inter-VLAN routing before ACL enforcement |
| `02-vlan30-acl-isolation-after-policy.png` | Gateway health, retained route, and blocked inter-VLAN traffic |
| `03-sg108e-vlan30-membership.png` | Tagged uplink and untagged ENVY access-port membership |
| `04-sg108e-port3-pvid30.png` | ENVY ingress traffic assigned to VLAN 30 |
| `05-er605-vlan30-to-lab-acl.png` | LAN-to-LAN policy that blocks VLAN 30 from the lab LAN |
| `06-sg108e-port-status-before.png` | October 7: Ports 1–4 Enabled at 1000MF; Ports 5–8 Enabled, Link Down |
| `07-er605-dhcp-xps-lease.png` | ER605 DHCP client list: capture laptop `kali` leased `10.10.20.101` from Port 5 (MACs masked) |
| `08-xps-eth0-listen-only-photo.jpg` | XPS `eth0` unmanaged, IPv6 disabled, up with no address (photo, MACs masked) |
| `09-sg108e-port-mirror-p1-to-p5.png` | Port Mirror on, mirroring port 5; Port 1 ingress and egress |
| `10-xps-tcpdump-vlan30-tagged-photo.jpg` | 10 of 10 frames `802.1Q (0x8100) … vlan 30` on the trunk (photo, MACs masked) |
| `11-sg108e-port-mirror-disabled.png` | Port Mirror off; all ports Disable |
| `12-sg108e-unused-ports-disabled.png` | Ports 5–8 Disabled; Ports 1–4 Enabled at 1000MF |
| `13-xps-port5-disabled-no-carrier-photo.jpg` | XPS in Port 5 after the change: `NO-CARRIER`, no link (photo, MAC masked) |
| `vlan-segmentation-findings.txt` | Concise evidence-derived findings |

## Evidence Limits
- **Baseline and DHCP cutover:** I didn't capture ENVY at `10.10.20.101`, the SG108E with 802.1Q disabled, or the lease change from `10.10.20.101` to `10.10.30.100`. Screenshot 01 shows ENVY at `10.10.30.100/24` afterward; the route-table change isn't captured.
- **ER605 Port 3:** the diagram shows the SG108E uplink on ER605 Port 3. No screenshot here shows the ER605 port.
- **802.1Q tagging:** in the original build, tagging was shown as switch configuration only (screenshot 03). The October 7 re-test above captures VLAN 30 tagged frames on the Port 1 trunk.
- **DHCP settings:** shown only in NET-008 evidence 18 and 19, captured October 5, 2026.

## Evidence Handling
Hardware addresses visible in terminal evidence were redacted before publication. No credentials, authentication secrets, or unrelated raw packet captures are included.

## Final State

```text
Yoda
10.10.20.10
Existing LAN / VLAN 1

ENVY
10.10.30.100
VLAN 30
```

VLAN 30 remained operational, ENVY retained connectivity to its VLAN 30 gateway, and `DENY_VLAN30_TO_LAB` remained enabled.

## Key Takeaway
A VLAN creates a Layer-2 boundary, but VLAN membership alone does not guarantee isolation between routed networks.

Effective segmentation requires the interaction of:

```text
VLAN membership
       +
Layer-3 routing
       +
Security policy
```

Each stage was tested on its own, and the VLAN 30 to lab LAN block has before-and-after evidence.

## Status

**PROVEN**

VLAN 30, inter-VLAN routing, and the VLAN 30 to lab LAN block are proven with before-and-after tests. Household and internet egress from VLAN 30 were not covered here; they were found open and closed in NET-008 (October 5, 2026). The 802.1Q tag on the trunk was captured on the wire on October 7, 2026, and the unused switch ports were disabled.
