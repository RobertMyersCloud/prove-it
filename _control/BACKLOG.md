# Prove-It Backlog

This backlog tracks capabilities that strengthen the locked discipline:

**Network & Systems Infrastructure**

The goal is depth, not breadth.

New work should strengthen infrastructure design, troubleshooting, operations, resilience, or security rather than open unrelated career lanes.

## Immediate Networking Depth

- 802.1Q trunk behavior
- Native VLAN behavior and platform limitations
- Layer-2 fault injection and recovery
- STP / RSTP concepts and Cisco lab validation
- EtherChannel / LACP
- OSPF neighbor formation and route selection
- Static / default / floating routes
- FHRP concepts
- IPv6 addressing and routing
- Structured subnetting / VLSM design
- Physical interface and error-state analysis
- ARP / neighbor-state behavior under failure
- MAC learning and endpoint movement
- Wireless fundamentals where relevant

## Core Network Services

- DHCP scopes and lease behavior
- DHCP options and client troubleshooting
- DNS client/server troubleshooting
- NTP and time synchronization
- NAT / PAT
- VPN / secure remote access
- SNMP
- Syslog
- Monitoring and alerting

## Network Security and Resilience

- Stateful firewall behavior
- ACL placement and validation
- Default-deny segmentation
- Management-plane isolation
- Protected Systems Enclave
- Firewall logging
- IDS / IPS fundamentals
- Secure administrative access
- Network configuration backup
- Recovery runbooks
- Change control and rollback planning
- Exposure assessment
- Zero Trust networking principles as they apply to infrastructure

## Systems and Virtualization

### Linux

- Filesystems and permissions
- Users and groups
- Processes and services
- systemd
- SSH
- sockets
- Linux networking and firewalling
- journalctl and logs
- Bash administration
- package management
- storage / LVM
- hardening
- backup / restore

### Windows

- PowerShell
- Windows networking
- services
- Event Logs
- local users / groups
- NTFS permissions
- firewall
- scheduled tasks
- patching
- hardening
- backup / restore

### Virtualization

- Proxmox networking
- Proxmox backup / restore
- Hyper-V virtual networking
- VM lifecycle
- snapshots versus backups
- recovery validation
- resource monitoring

## Infrastructure Observability

- Centralized syslog
- SNMP polling
- interface-state monitoring
- bandwidth / utilization monitoring
- service health checks
- time synchronization
- event correlation
- packet capture from mirrored traffic
- baseline versus abnormal behavior

## Automation for Infrastructure

- PowerShell diagnostics
- Bash diagnostics
- configuration collection
- reachability checks
- route / interface inventory
- log parsing
- repeatable validation scripts
- simple Python only where it directly improves infrastructure operations

## Enterprise Services - Supporting, Not a Separate Career Lane

- Active Directory fundamentals
- DNS integration
- DHCP relay
- domain join
- authentication basics
- group policy concepts
- AAA / RADIUS concepts

These are studied as systems and infrastructure dependencies, not as a return to an IAM-focused career path.

## Cloud - Infrastructure-Relevant Only

- AWS / Azure networking
- virtual networks / VPCs
- route tables
- security groups / network controls
- VPN / hybrid connectivity
- cloud logging
- cloud infrastructure troubleshooting

## Future Security Specialization

Only after the infrastructure foundation and production experience are strong:

- Zeek
- Suricata
- NetFlow
- deeper packet reconstruction
- network detection
- incident support
- network forensics
- DFIR
- threat hunting
- offensive networking / pivoting

These are future specializations built on the infrastructure spine, not present-day competing lanes.

## Planned Proof Block

- **NET-008 - Protected Systems Enclave**
- **NET-009 - Layer-2 Fault Injection & Recovery**
- **NET-010 - Centralized Logging & Infrastructure Telemetry**
- **NET-011 - Secure Remote Management Under CGNAT**
- **NET-012 - Backup, Failure, Restore & Service Validation**

## Flagship

**Mission-Critical Network & Systems Defense Lab**

The flagship will integrate proven capabilities in:

- Layer 2 / Layer 3 networking
- segmentation
- core services
- virtualization
- observability
- troubleshooting
- recovery
- security-focused infrastructure operations

No separate financial-crime, IAM, GRC, or unrelated portfolio track is planned inside this repository.
