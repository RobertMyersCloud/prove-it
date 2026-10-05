# LNX-001 — Linux VM Build and SSH Administration

## Hiring Claim
After reviewing this artifact, a hiring manager has evidence that I can build a Linux server VM in Hyper-V, put it on the network, and administer it remotely over SSH, with proof from both the client and the server side.

## Employer Skills Demonstrated
- Hyper-V Generation 2 VM build
- Secure Boot template selection for Linux
- Ubuntu Server installation
- Linux network configuration checks
- DNS and external connectivity validation
- SSH remote administration from Windows
- SSH listener and log verification

## Objective
Build a fresh Linux virtual machine, get it on the network, and remotely administer it over SSH without being walked through the process.

This was not my first time building a VM. The difference here was proving each part instead of treating a working VM as proof by itself.

## Environment

| Item | Value |
|---|---|
| Host | Victus (Windows, Hyper-V) |
| VM | `prove-final-01`, Generation 2 |
| OS | Ubuntu Server 24.04.4 LTS |
| Resources | 4 GB RAM, 8 vCPU, 30 GB disk |
| Network | Hyper-V Default Switch |

## Build

Because I was installing Linux on a Generation 2 Hyper-V VM, I changed the Secure Boot template from the Windows default to **Microsoft UEFI Certificate Authority** before installing Ubuntu. That lets the VM trust the Ubuntu boot loader.

I installed Ubuntu Server with a normal non-root account and included OpenSSH Server.

I did not patch the VM after installation. Patching was not the objective. The goal was to build the system, establish networking, reach the outside network, and administer it over SSH.

## Local Validation

After installation I verified the system locally:

- User: `robert-myers`
- Hostname: `prove-final-01`
- Interface: `eth0`
- IPv4 address: `172.31.74.93/20`

I also confirmed TCP port 22 was listening on IPv4 and IPv6.

One detail I want to understand better: before I connected remotely, `ssh.service` showed as inactive while `ssh.socket` was listening on port 22. After the SSH connection was made, the service became active and logged the authentication. I'm leaving the deeper explanation for the systemd and services work instead of hand-waving past it here.

## External Connectivity

I tested connectivity to `www.google.com`. The VM resolved the name to a public address and got 6 of 6 ICMP replies with 0% packet loss. That shows both DNS resolution and outbound connectivity.

## Remote SSH Validation

From Windows on Victus I connected to `prove-final-01` over SSH and verified the user, hostname, and interface again from inside the remote session.

The Linux SSH logs recorded the successful connection from the Hyper-V host-side address `172.31.64.1`.

That gives evidence from both sides: Windows established the session, and Linux recorded the authentication.

## Evidence Review

During my final evidence review I noticed the package did not include proof of external connectivity. I had tested it but hadn't captured it. I went back to the VM and captured the validation (screenshot 07).

Doing the work and proving the work are two different things.

## Findings

- The VM was built with the correct Secure Boot trust template for Linux.
- The VM received an address on the Hyper-V Default Switch network and reached the internet with working DNS.
- SSH was listening on TCP/22 and accepted a session from the Windows host.
- The Linux side logged that session from the expected host-side address.

## Evidence Index

| Evidence | What it shows |
|---|---|
| `01-hyperv-vm-build.png` | Generation 2 VM creation |
| `02-hyperv-secure-boot.png` | Microsoft UEFI Certificate Authority template |
| `03-installer-network-config.png` | Installer network configuration |
| `04-installer-lvm-layout.png` | LVM storage layout |
| `05-local-linux-validation.png` | Local user, hostname, interface, SSH listener |
| `06-remote-ssh-validation.png` | SSH session from Windows and Linux log entry |
| `07-external-connectivity.png` | DNS resolution and 6/6 ICMP replies |

![Hyper-V VM Build](evidence/screenshots/01-hyperv-vm-build.png)

![Hyper-V Secure Boot](evidence/screenshots/02-hyperv-secure-boot.png)

![Installer Network Configuration](evidence/screenshots/03-installer-network-config.png)

![Installer LVM Layout](evidence/screenshots/04-installer-lvm-layout.png)

![Local Linux Validation](evidence/screenshots/05-local-linux-validation.png)

![Remote SSH Validation](evidence/screenshots/06-remote-ssh-validation.png)

![External Connectivity](evidence/screenshots/07-external-connectivity.png)

## Evidence Handling
VM MAC addresses and the MAC-derived IPv6 link-local addresses are replaced with `[VM-MAC]` and `[VM-LINK-LOCAL]`. My username and host names are left visible.

## Status
**PROVEN**
