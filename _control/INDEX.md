# Prove-It Evidence Index

This index maps published proof to Network & Systems Infrastructure capabilities.

## Published Evidence

| ID | Artifact | Skills Demonstrated | Status |
|---|---|---|---|
| LAB-001 | Isolated Cybersecurity Range Architecture & Trust Boundary | TCP/IP, switching, routing, firewall policy, Proxmox bridging, dual-homing, evidence handling | PROVEN |
| LEGACY-001 | Linux VM + SSH | Linux, Hyper-V, virtualization, SSH, remote administration | PROVEN |
| NET-001 | Ethernet, ARP & MAC Learning with Port Mirroring | Ethernet, ARP, ICMP, neighbor-cache analysis, port mirroring, packet analysis | PROVEN |
| NET-002 | TCP vs UDP Traffic Analysis | TCP handshake/teardown, sockets, sequence/ACK behavior, UDP, ICMP errors | PROVEN |
| NET-003 | DNS Resolution and Troubleshooting | DNS resolver paths, UDP/TCP 53, NXDOMAIN, timeout analysis, caching observations | PROVEN |
| NET-004 | HTTP/TLS Traffic Analysis | TCP/443, TLS lifecycle, SNI, ALPN, X.509, encrypted traffic analysis | PROVEN |
| NET-005 | Routing and Path Selection | Longest-prefix match, metrics, static routes, next-hop validation, restoration | PROVEN |
| NET-006 | NAT and CGNAT Path Analysis | RFC1918/RFC6598, WAN/LAN boundaries, route analysis, CGNAT evidence | PROVEN |
| NET-007 | VLAN Segmentation and Policy Enforcement | 802.1Q, tagged/untagged ports, PVID, DHCP, inter-VLAN routing, ACLs | PROVEN |

## Skill-to-Proof Map

| Skill | Evidence | Coverage |
|---|---|---|
| Ethernet / Layer 2 | LAB-001; NET-001; NET-007 | PROVEN |
| ARP / neighbor behavior | NET-001; NET-007 | PROVEN |
| TCP | NET-002; NET-003; NET-004 | PROVEN |
| UDP | NET-002; NET-003 | PROVEN |
| ICMP | NET-001; NET-002; NET-005; NET-007 | PROVEN |
| DNS | NET-003 | PROVEN |
| HTTP / TLS | NET-004 | PROVEN |
| Routing / path selection | LAB-001; NET-005; NET-006; NET-007 | PROVEN |
| NAT / CGNAT | NET-006 | PROVEN |
| VLANs / 802.1Q | NET-007 | PROVEN |
| Inter-VLAN routing | NET-007 | PROVEN |
| ACL / policy enforcement | LAB-001; NET-007 | PROVEN |
| Packet analysis | NET-001; NET-002; NET-003; NET-004; NET-005 | PROVEN |
| Port mirroring | NET-001 | PROVEN |
| Proxmox virtual networking | LAB-001 | PROVEN |
| Hyper-V virtualization | LEGACY-001 | PROVEN |
| Linux remote administration | LEGACY-001 | PROVEN |
| Evidence sanitization | LAB-001; NET-001 through NET-007 | PROVEN |
| Structured troubleshooting | NET-003; NET-004; NET-005; NET-006; NET-007 | PROVEN |
| Centralized logging / telemetry | Planned NET-010 | QUEUED |
| Secure remote management / VPN | Planned NET-011 | QUEUED |
| Backup / restore validation | Planned NET-012 | QUEUED |

## Next Proof

| ID | Planned Artifact | Target Capability |
|---|---|---|
| NET-008 | Protected Systems Enclave | Real protected workload, explicit allowed/denied paths |
| NET-009 | Layer-2 Fault Injection & Recovery | Physical switching fault isolation and restoration |
| NET-010 | Centralized Logging & Infrastructure Telemetry | Event collection, observability, time correlation |
| NET-011 | Secure Remote Management Under CGNAT | VPN / secure remote-access architecture and validation |
| NET-012 | Backup, Failure, Restore & Service Validation | Recovery workflow and layered post-restore validation |

## Flagship

**Mission-Critical Network & Systems Defense Lab - PLANNED**

The flagship will integrate proven artifacts into one concise architecture and troubleshooting narrative without duplicating the underlying evidence.
