# Evidence Index

Every artifact below is a documented run in my own lab, with machine evidence and the limits of each claim stated in its README.

## Published Artifacts

| ID | Artifact | Skills Demonstrated |
|---|---|---|
| LAB-001 | [Isolated Cybersecurity Range Architecture & Trust Boundary](00-lab-infrastructure/LAB-001-cybersecurity-range/README.md) | TCP/IP, switching, routing, firewall policy, Proxmox bridging, dual-homing, evidence handling |
| NET-001 | [Ethernet, ARP & MAC Learning with Port Mirroring](01-networking/NET-001-ethernet-arp-mac-learning/README.md) | Ethernet, ARP, ICMP, neighbor-cache analysis, port mirroring, packet analysis |
| NET-002 | [TCP vs UDP Traffic Analysis](01-networking/NET-002-tcp-udp-traffic-analysis/README.md) | TCP handshake/teardown, sockets, sequence/ACK behavior, UDP, ICMP errors |
| NET-003 | [DNS Resolution and Troubleshooting](01-networking/NET-003-dns-resolution-troubleshooting/README.md) | DNS resolver paths, UDP/TCP 53, NXDOMAIN, timeout analysis |
| NET-004 | [HTTP/TLS Traffic Analysis](01-networking/NET-004-http-tls-traffic-analysis/README.md) | TCP/443, TLS lifecycle, SNI, ALPN, X.509, encrypted traffic analysis |
| NET-005 | [Routing and Path Selection](01-networking/NET-005-routing-path-selection/README.md) | Longest-prefix match, metrics, static routes, next-hop validation, restoration |
| NET-006 | [NAT and CGNAT Path Analysis](01-networking/NET-006-nat-cgnat-path-analysis/README.md) | RFC1918/RFC6598, WAN/LAN boundaries, route analysis, CGNAT evidence |
| NET-007 | [VLAN Segmentation and Policy Enforcement](01-networking/NET-007-vlan-segmentation/README.md) | 802.1Q, tagged/untagged ports, PVID, DHCP, inter-VLAN routing, ACLs |
| NET-008 | [Protected Systems Enclave](01-networking/NET-008-protected-systems-enclave/README.md) | Router ACL + firewalld rich rules, least-privilege SSH, persistent routes, positive/negative testing |
| LNX-001 | [Linux VM Build and SSH Administration](02-linux/LNX-001-linux-vm-ssh/README.md) | Linux, Hyper-V, virtualization, SSH, remote administration |

## Skill-to-Proof Map

| Skill | Evidence |
|---|---|
| Ethernet / Layer 2 | LAB-001, NET-001, NET-007 |
| ARP / neighbor behavior | NET-001, NET-007 |
| TCP | NET-002, NET-003, NET-004 |
| UDP | NET-002, NET-003 |
| ICMP | NET-001, NET-002, NET-005, NET-007 |
| DNS | NET-003 |
| HTTP / TLS | NET-004 |
| Routing / path selection | LAB-001, NET-005, NET-006, NET-007, NET-008 |
| CGNAT identification / address boundaries | NET-006 |
| VLANs / 802.1Q | NET-007 |
| Inter-VLAN routing | NET-007 |
| ACL / policy enforcement | LAB-001, NET-007, NET-008 |
| Host firewall policy | NET-008 |
| Host hardening / service minimization | NET-008 |
| DHCP option troubleshooting | NET-008 |
| Secure administrative access | NET-008 |
| Linux patching and kernel validation | NET-008 |
| Packet analysis | NET-001 through NET-005 |
| Port mirroring | NET-001 |
| Proxmox virtual networking | LAB-001 |
| Hyper-V virtualization | LNX-001 |
| Linux remote administration | LNX-001 |
| Evidence sanitization | LAB-001, LNX-001, NET-001 through NET-008 |
| Structured troubleshooting | NET-003 through NET-008 |

A companion investigation, [packet-analysis-lab](https://github.com/RobertMyersCloud/packet-analysis-lab), applies the protocol work to troubleshooting and security analysis.
