# Prove-It

This repository is my hands-on body of proof for **Network & Systems Infrastructure**.

The goal is simple: demonstrate that I can understand, build, troubleshoot, secure, validate, and recover networked systems through work I actually performed and evidence I can explain in an interview.

## Current Technical Spine

**Network & Systems Infrastructure**

Primary focus:

- Ethernet, ARP, TCP/IP, routing, and switching
- VLANs, 802.1Q, inter-VLAN routing, and segmentation
- DNS, DHCP, NAT, VPN, and other core network services
- Linux and Windows systems administration
- Proxmox and Hyper-V virtualization
- Packet capture and protocol analysis
- Firewall and access-control behavior
- Monitoring, logging, backup, and recovery
- Structured troubleshooting and root-cause analysis

Security is integrated into the infrastructure work through segmentation, least privilege, traffic analysis, logging, hardening, and recovery validation.

## Proof Method

Every substantial artifact follows the same standard:

**Learn -> Build -> Break -> Diagnose -> Fix -> Prove -> Explain**

A screenshot, command, or successful ping is not enough by itself.

Strong proof connects:

**Objective -> Action -> Machine-generated evidence -> Analysis -> Validation -> Finding**

Failures are useful when they expose real troubleshooting work. Claims are limited to what the collected evidence actually supports.

## Repository Structure

- `00-lab-infrastructure/` - physical and virtual lab architecture, boundaries, and baseline state
- `01-networking/` - networking proof from Ethernet through segmentation and troubleshooting
- `foundation/` - supporting systems-foundation work such as Linux, virtualization, and SSH
- `_control/` - project status, evidence index, backlog, capture standards, and operating rules

Additional capability folders are created only when evidence earns them.

## Current Networking Proof

Published networking artifacts include:

- **NET-001** - Ethernet, ARP & MAC Learning with Port Mirroring
- **NET-002** - TCP vs UDP Traffic Analysis
- **NET-003** - DNS Resolution and Troubleshooting
- **NET-004** - HTTP/TLS Traffic Analysis
- **NET-005** - Routing and Path Selection
- **NET-006** - NAT and CGNAT Path Analysis
- **NET-007** - VLAN Segmentation and Policy Enforcement

These artifacts use physical and virtual lab systems, packet captures, routing evidence, switch/router configuration, controlled failure, and before/after validation.

## Evidence and Privacy

Raw working evidence stays local under ignored `evidence/raw/` directories.

Before publication, evidence is reviewed and unnecessary identifiers, credentials, secrets, MAC addresses, SSIDs, public IPs, account identifiers, and unrelated packet data are removed or replaced with stable role labels when needed.

Private RFC1918 addressing is retained when it materially explains the architecture.

## Training Boundary

Training is an input, not a public artifact.

I do not publish proprietary course material, answer keys, exam questions, copyrighted lab instructions, proprietary VM images, or restricted datasets.

Useful concepts are learned, reproduced independently in my own environment, validated with original evidence, and documented in my own words.

## End State

The objective is not to collect disconnected labs.

It is to build deep, defensible capability in network and systems infrastructure and to show the progression from fundamentals to production-style troubleshooting, observability, recovery, and security-focused operations.

**Prove it.**
