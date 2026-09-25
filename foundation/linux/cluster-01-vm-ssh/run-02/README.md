# Cluster 01 — Run 2: Linux VM Build and SSH



**Run:** 2 — Coached  

**Status:** Complete  

**Platform:** Hyper-V / Ubuntu Server 24.04.4 LTS  

**Host:** Victus (Windows)  

**VM:** prove-linux-01



## Objective



The goal of this run was to build a Linux virtual machine in Hyper-V, get it on the network, and prove that I could remotely administer it from Windows over SSH.



This was not my first time building a VM. I have built several before. The difference with this run was capturing the work and producing evidence instead of just getting the machine running and moving on.



## Build



I created `prove-linux-01` as a Generation 2 Hyper-V VM with:



- 4 GB RAM

- 2 virtual processors

- 30 GB virtual disk

- Hyper-V Default Switch

- Ubuntu Server 24.04.4 LTS



Before installing Ubuntu, I changed the Generation 2 Secure Boot template from the Windows default to **Microsoft UEFI Certificate Authority**, which allows the VM to trust the Ubuntu/Linux boot loader.



I then installed Ubuntu Server, created my normal user account, configured the hostname as `prove-linux-01`, and included OpenSSH Server so the machine could be administered remotely.



## Network Validation



After installation, I logged into the VM locally and verified the user, hostname, and network configuration.



The VM received:



- Interface: `eth0`

- IPv4 address: `172.31.78.3/20`



I tested network connectivity from the VM. I verified that it could reach outside networks and also communicate with another machine on my own network.



The connectivity testing showed that the VM was not just running locally in Hyper-V. It had working network connectivity through the Hyper-V Default Switch.



## SSH Validation



I moved back to Windows on Victus and connected to the VM with SSH.



The first connection required me to accept the VM's ED25519 host key. After authenticating, the Windows terminal changed to the remote Linux shell:



`robert-myers@prove-linux-01`



From the remote session I verified the user, hostname, and `eth0` configuration again.



I also checked the SSH service and listening socket.



The SSH service showed `active (running)`, and the system logs recorded the successful connection from the Hyper-V host-side address:



`Accepted password for robert-myers from 172.31.64.1 ... ssh2`



The socket check showed SSH listening on TCP port 22 on both IPv4 and IPv6:



- `0.0.0.0:22`

- `\[::]:22`



This gave me evidence from both sides of the connection: Windows successfully established the SSH session, and Linux recorded and accepted that connection.



## What This Proves



This run shows that I can:



- create and configure a Generation 2 Linux VM in Hyper-V

- configure the correct Secure Boot trust template for Linux

- install Ubuntu Server

- work from both Windows PowerShell and the Linux command line

- identify a Linux user's identity, hostname, interface, and IP configuration

- validate network connectivity

- remotely administer Linux over SSH

- verify that the SSH service is running

- verify that TCP port 22 is listening

- use system-generated evidence to validate a successful SSH connection



## What I Learned



Nothing in the basic VM build surprised me because I have built virtual machines several times before. The difference in this run was slowing down enough to prove each part instead of treating a working VM as proof by itself.



One thing I want to come back to is how SSH is being started. `systemctl status ssh` showed the service as active while also showing the service unit as disabled and `ssh.socket` as the trigger. I know SSH worked, but I want to understand exactly what systemd is doing there instead of hand-waving past it.



That belongs in the processes and services work later in Foundation.



The Ubuntu installer also left unused capacity in the LVM volume group. I am leaving that untouched because I will use it during the filesystem/LVM work in Cluster 02.



## Evidence



### Hyper-V Build



!\[Hyper-V VM Build](evidence/screenshots/01-hyperv-vm-build.png)



!\[Hyper-V VM Settings](evidence/screenshots/02-hyperv-vm-settings.png)



### Storage



!\[Ubuntu LVM Layout](evidence/screenshots/03-ubuntu-lvm-layout.png)



### Victus Network State



!\[Victus Network State](evidence/screenshots/04-victus-network-state.png)



### SSH Validation



!\[SSH Session and Service Proof](evidence/screenshots/05-ssh-session-service-proof.png)



Additional machine-generated evidence:



- `evidence/victus-hyperv-starting-state.txt`

- `evidence/vm-definition.txt`

- `evidence/vm-disk.txt`

- `evidence/vm-network-adapter.txt`



## Run Result



**Run 2 — COMPLETE**



I completed the VM build and SSH validation with coaching available. Cluster 01 is not PROVEN yet.



The remaining requirement is Run 3: closed-book solo.


