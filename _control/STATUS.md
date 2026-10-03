# Prove-It Status

## Current Sprint

**Network & Systems Infrastructure Depth Sprint**

**Target date:** October 26, 2026

**Goal:** Deepen networking and systems capability, strengthen employer-visible proof, and prepare the repository for the broad West DFW infrastructure job campaign.

## Primary Employment Discipline

**Network & Systems Infrastructure**

Target roles include:

- Network Administrator
- Network Analyst
- Network Engineer I
- Systems Administrator
- Systems Engineer
- Infrastructure Analyst / Engineer
- Systems / Network Administrator
- Network Operations
- Data Center IT / Infrastructure Operations
- Network Security / Systems Security roles where the work remains infrastructure-heavy

Security is treated as an integrated infrastructure competency, not a separate competing career identity.

## Completed Proof

| ID | Artifact | Primary Skills | Status |
|---|---|---|---|
| LAB-001 | Isolated Cybersecurity Range Architecture & Trust Boundary | TCP/IP, switching, routing, firewall policy, Proxmox, evidence handling | PROVEN |
| LEGACY-001 | Linux VM + SSH | Linux, Hyper-V, virtualization, SSH, remote administration | PROVEN |
| NET-001 | Ethernet, ARP & MAC Learning with Port Mirroring | Ethernet, ARP, MAC reasoning, ICMP, port mirroring, packet analysis | PROVEN |
| NET-002 | TCP vs UDP Traffic Analysis | TCP lifecycle, sequence/ACK behavior, sockets, UDP, ICMP errors | PROVEN |
| NET-003 | DNS Resolution and Troubleshooting | DNS, UDP/TCP 53, resolver paths, NXDOMAIN, timeout analysis | PROVEN |
| NET-004 | HTTP/TLS Traffic Analysis | HTTPS/TLS, TCP/443, ClientHello, SNI, X.509, TLS troubleshooting | PROVEN |
| NET-005 | Routing and Path Selection | Longest-prefix match, metrics, static routes, packet-path validation | PROVEN |
| NET-006 | NAT and CGNAT Path Analysis | NAT/CGNAT, RFC1918/RFC6598, routing boundaries, traceroute limits | PROVEN |
| NET-007 | VLAN Segmentation and Policy Enforcement | 802.1Q, PVIDs, DHCP, inter-VLAN routing, ACL isolation | PROVEN |

## Next Proof Block

| ID | Planned Proof | Purpose | Status |
|---|---|---|---|
| NET-008 | Protected Systems Enclave | Place a real service behind an explicit trust boundary and validate allowed/denied flows | COMPLETE |
| NET-009 | Layer-2 Fault Injection & Recovery | Troubleshoot a real VLAN/tagging/uplink fault on physical hardware | QUEUED |
| NET-010 | Centralized Logging & Infrastructure Telemetry | Generate and collect infrastructure events; validate time and observability | QUEUED |
| NET-011 | Secure Remote Management Under CGNAT | Solve a real remote-access constraint using supported technology | QUEUED |
| NET-012 | Backup, Failure, Restore & Service Validation | Perform controlled recovery and validate system, network, and policy state | QUEUED |

Cisco-specific features that are not supported by the physical lab are practiced and documented separately in CCNA lab work rather than falsely attributed to TP-Link hardware.

## Current Lab Baseline

- Household network: `192.168.1.0/24`
- Lab network: `10.10.20.0/24`
- ER605 trust boundary documented
- TL-SG108E integrated and baselined
- Yoda / Proxmox bridge documented
- Kali contained inside the lab
- ENVY established as a dual-homed management workstation with IPv4 forwarding disabled
- VLAN 30 / `10.10.30.0/24` implemented and policy-isolated
- Raw-evidence exclusion and sanitization workflow established
- Upstream CGNAT condition documented with bounded evidence

## Near-Term Technical Priorities

1. Strengthen Layer-2 switching and troubleshooting depth
2. Reinforce routing and route-selection reasoning
3. Build core network-services troubleshooting around DHCP, DNS, NTP, NAT, and VPN
4. Add monitoring, logging, and recovery proof
5. Continue Linux/Windows and virtualization depth in direct support of infrastructure roles
6. Prepare for CCNA and GSEC examinations in November 2026

## Flagship Direction

**Mission-Critical Network & Systems Defense Lab**

The flagship will integrate proven networking and systems capabilities into one recruiter-readable environment. It will reference existing proof rather than duplicate every artifact.

## Next Milestone

Complete the NET-008 through NET-012 proof block, then assemble the strongest evidence into the flagship while keeping the repository centered on one discipline:

**Network & Systems Infrastructure**
