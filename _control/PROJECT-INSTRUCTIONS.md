# Prove-It - Project Instructions

## Purpose

This repository is my body of proof for **Network & Systems Infrastructure**.

Study notes, books, course material, walkthroughs, exam preparation, and raw learning belong elsewhere.

This repository answers one question:

**Can I actually do the work?**

Every published artifact must demonstrate a capability through work I performed, evidence I collected, analysis I can explain, and validation that supports the result.

## Locked Direction

**Primary discipline: Network & Systems Infrastructure**

Immediate employment targets are infrastructure-heavy roles such as Network Administrator, Network Analyst, Network Engineer I, Systems Administrator, Systems Engineer, Infrastructure Analyst / Engineer, Network Operations, and Data Center IT / Infrastructure Operations.

Security is integrated into the discipline through segmentation, least privilege, firewall policy, packet analysis, logging, hardening, monitoring, incident support, and recovery.

The repository does not chase unrelated career identities.

Older or supporting knowledge may remain visible when it contributes to systems or infrastructure capability, but new proof should reinforce the locked discipline unless the strategy is intentionally changed later.

## Technical Progression

The near-term progression is:

**Networking fundamentals -> switching/routing depth -> core services -> systems/virtualization -> observability/recovery -> security-heavy infrastructure**

Longer-term security specialization may grow from this foundation into network forensics, DFIR, threat hunting, or offensive security, but those are future specialization gates rather than competing current lanes.

## Repository Architecture

Current proof is organized by capability:

- `00-lab-infrastructure`
- `01-networking`
- `foundation`
- `_control`

Additional directories are populated only when evidence earns them and when they directly support the infrastructure spine.

Do not create folders simply to make the repository look broad.

## Proof Workflow

**Identify capability -> Perform work -> Collect evidence -> Analyze -> Validate -> Sanitize -> Document -> Publish**

For troubleshooting work, prefer:

**Expected -> Observed -> Investigated -> Root Cause -> Change -> Validation**

For skill development, the operating method is:

**Learn -> Build -> Break -> Diagnose -> Fix -> Prove -> Explain**

## Employer Alignment

Before building a substantial artifact, ask:

**What Network & Systems Infrastructure hiring claim will this prove?**

Strong artifacts should map to recurring job requirements such as routing, switching, VLANs, DNS, DHCP, NAT, VPN, firewalls, Linux, Windows, virtualization, monitoring, logging, backup/recovery, troubleshooting, and change discipline.

A project should not be built only because it looks impressive.

## Evidence Standard

A screenshot, README, command, or successful ping alone is not proof.

Strong proof connects:

**Objective -> Action -> Machine-generated evidence -> Analysis -> Validation -> Finding**

Evidence may include terminal output, configurations, logs, PCAPs, scripts, queries, screenshots, before/after state, diagrams, timelines, restoration tests, and troubleshooting records.

Every claim must stay inside the boundary of what the evidence establishes.

## Hardware Honesty

Physical hardware proves only features the physical hardware actually supports.

Do not imply that TP-Link equipment is Cisco, Palo Alto, Fortinet, or other enterprise hardware.

Cisco-specific capabilities may be practiced through CCNA labs or simulators and documented separately when useful.

They must be clearly labeled as Cisco lab work rather than physical production-equivalent evidence.

A documented platform limitation is acceptable evidence. An invented capability is not.

## Production Awareness

Home-lab work does not equal production experience.

Where useful, artifacts may include a short **Production Considerations** section describing:

- change control
- rollback planning
- monitoring
- scale
- redundancy
- maintenance windows
- user/business impact

Do not claim production ownership that did not occur.

## Evidence Handling and Privacy

Raw evidence is reviewed before publication. Local raw-evidence directories are excluded from version control.

Omit or sanitize information that does not contribute to the proof: passwords, private keys, tokens, secrets, unnecessary MAC addresses or SSIDs, serial numbers, account identifiers, unnecessary public IPs, and unrelated packet-capture data.

Private RFC1918 addressing may remain when it materially explains the architecture.

Review staged content before every public commit.

## Failure and Troubleshooting

Failure can be useful evidence when it occurs naturally or when a controlled fault is deliberately introduced for troubleshooting practice.

Do not manufacture misleading failures for appearance.

Document what was changed, why the test was safe, what was expected, what actually happened, how the problem was isolated, and how normal operation was restored.

## Writing Standard

Published work represents work I actually performed and understand.

If I did not run, observe, test, analyze, or learn it, the artifact cannot claim that I did.

Every technical claim published under my name must be something I can explain and defend in an interview.

## Training and SANS Material

Training is an input, not a public artifact.

Do not publish proprietary books, copied labs, answer keys, exam questions, copyrighted training screenshots, proprietary VM images, or proprietary datasets.

When training teaches a useful capability:

**Learn -> Apply independently -> Publish original proof -> Integrate strong capabilities into flagships**

## Flagship

The current flagship direction is:

**Mission-Critical Network & Systems Defense Lab**

The flagship is a concise integration layer for the strongest proven infrastructure capabilities.

It should reference underlying artifacts rather than duplicate every screenshot and command.

Create or expand the flagship only when enough evidence exists to justify the claim.

## Gaps and Backlog

`GAPS.md` records weaknesses exposed while performing or explaining work.

`BACKLOG.md` tracks infrastructure capabilities, employer requirements, and proof still needing stronger evidence.

A gap is not a failure. It is a specific learning target with a retest.

## Status

- **QUEUED** - planned, not started
- **ACTIVE** - currently being built
- **THIN** - some evidence exists; stronger proof needed
- **PROVEN** - published evidence demonstrates the capability and I can explain it

## Scope and Safety

Testing is only against systems I own or am explicitly authorized to test.

Production systems and family systems are not targets.

Scope must be known before testing begins.

## End State

Build deep, defensible capability in Network & Systems Infrastructure:

- understand the normal state
- design and configure the environment
- recognize abnormal behavior
- troubleshoot systematically
- restore service safely
- secure the infrastructure
- explain the evidence clearly

**Master one trade. Prove it.**

## Cumulative Disclosure Review

Public-evidence review considers cumulative disclosure across the entire portfolio, not only the current artifact.

An identifier redacted in one artifact must not be exposed in another artifact when the two can be correlated.

Persistent infrastructure identifiers such as MAC addresses, SSIDs, serial numbers, public IP addresses, credentials, tokens, private keys, and unrelated packet-capture data are not published unless there is a specific documented reason.

When an identifier is technically relevant, preserve the analytical relationship with stable role labels such as `[ENVY-MAC]`, `[YODA-MAC]`, or `[ER605-MAC]` rather than publishing the real value.

Original unsanitized evidence remains local under `evidence/raw/` and is excluded from version control.
