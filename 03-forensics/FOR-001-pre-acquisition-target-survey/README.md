# FOR-001 — Pre-Acquisition Target Survey

> **Moved from an earlier repository.** I did this survey on August 31, 2026, in a separate repo I've since merged into this one. The machine, the HP ENVY, was later wiped and rebuilt with Fedora and is now the enclave host in [NET-008](../../01-networking/NET-008-protected-systems-enclave/README.md).

## At a Glance
- **What I did:** Surveyed a Windows 11 laptop I had set aside as a forensic target before imaging it: disk identity, encryption state, and a live-OS baseline, captured as PowerShell transcripts and redacted before publishing.
- **What it shows:** Checking what could ruin an acquisition while the machine still boots, instead of finding out after the drive is pulled.
- **What I found:** Two hazards. The storage was an Intel Optane module and a 238 GB SSD paired by Intel RST into a single RAID volume, so a pulled drive may not read as a normal disk. The OS volume was BitLocker-encrypted, so an image without the recovery key would be ciphertext. The acquisition itself later failed when the evidence drive dropped off USB mid-session.
- **Why it matters for detection and response:** The cheapest time to solve an acquisition problem is before the acquisition. The survey found the two hazards in minutes; the failure that actually happened was in my own evidence storage, which the survey didn't check.

## Skills Demonstrated

- Disk and volume identification with `Get-Disk`, `Get-PhysicalDisk`, `Get-Partition`, `Get-Volume`
- BitLocker status and protector review with `manage-bde`
- Live-system baseline: accounts, administrators, installed software, processes, services, network configuration
- PowerShell transcripts as evidence
- Redaction and pre-publication checks

## Setup

- **Target:** HP ENVY x360 Convertible 15m-dr1xxx, Windows 11 Home build 26200, broken screen, run on an external HDMI display.
- **Storage:** Intel Optane plus a 238 GB SSD, presented as one 256 GB volume (`BusType: RAID`).
- **Capture:** PowerShell 5.1 transcripts run locally on the target, copied off, redacted, then committed.

## What I Did

1. Recorded disk identity with `Get-Disk` and `Get-PhysicalDisk`: size, partition style, and bus type ([recon transcript](evidence/junk-laptop-recon.txt)).
2. Checked encryption with `manage-bde -status`.
3. Exported the BitLocker recovery key with `manage-bde -protectors -get C:` and stored it in my password vault. The key is not in this repo.
4. Captured a live-OS baseline in one transcript: system identity, local accounts and administrators, installed software, top processes by CPU, running services, partitions, volumes, network adapters, and IPv4 addresses ([baseline transcript](evidence/junk-laptop-baseline.txt)).
5. Redacted both transcripts (hostname, username, BIOS serial, MAC addresses, home-LAN addresses) and searched them for those values before committing.

## Findings

### Hazard 1 — RAID-paired storage

`Get-PhysicalDisk` reported one disk, `Intel Optane+238GBSSD`, with `BusType: RAID`, and `Get-Disk` reported its serial as `Optane_0000`, a placeholder rather than a drive serial. Windows was seeing the RST volume, not the two physical devices. If the SSD is pulled and attached to another machine, there's no guarantee it reads as an ordinary disk, because part of the volume's layout is managed by the RST pairing.

### Hazard 2 — BitLocker

```text
Conversion Status:    Used Space Only Encrypted
Encryption Method:    XTS-AES 128
Protection Status:    Protection On
Key Protectors:       Numerical Password, TPM
```

With TPM protection, the volume unlocks automatically on this machine and nowhere else. An image taken from the pulled drive is unreadable without the recovery key, which is why I exported it while the system still booted.

## Acquisition Attempt — September 7, 2026

I imaged the SSD from a Kali live boot in forensic mode to a dedicated 1.8 TB evidence drive. The first image hash-verified. The evidence drive then dropped off USB partway through the session, and the evidence folder became unreadable. The acquisition was never completed, and the image isn't usable as evidence.

I didn't go back for a second acquisition. The machine was wiped and rebuilt with Fedora for lab use.

### Lessons

- **The survey checked the source, not the destination.** It found both hazards on the target and nothing about the drive I was writing evidence to. The failure came from the destination.
- **Verify every part an acquisition depends on before starting it:** the source, the destination drive, and the connection between them. A long operation shouldn't start until all three have been checked.
- **A verified hash only proves the image as it was when hashed.** Once the folder it lived in became unreadable, the earlier verification no longer meant anything for the case.

## Evidence Limits

- The September 7 imaging commands, hash output, and the drive error aren't in this repo. They were on the evidence drive or in the session itself, and I didn't keep a copy elsewhere.
- The RAID hazard is identified from how Windows reported the disk. I didn't test reading the pulled SSD on another machine before the acquisition failed.
- The baseline has no narrative of what normal looks like on this machine. It was planned and not written before the machine was wiped.
- Redaction removed the hostname, username, BIOS serial, MAC addresses, and home-LAN addresses. Per-user service suffixes and the installed-software list are left in.

## Status
**SURVEY PROVEN — ACQUISITION NOT COMPLETED**

Disk identity, encryption state, and the live baseline are in the transcripts. The acquisition failed on the evidence drive, and the target no longer exists in this form.
