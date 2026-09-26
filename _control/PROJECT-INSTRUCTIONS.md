# Prove-It — Project Instructions

## Purpose
This repository is my body of proof. Study notes, books, course material, walkthroughs, exam preparation, and raw learning belong elsewhere.

This repository answers one question: **Can I actually do the work?**

Every published artifact must demonstrate a capability through work I performed, evidence I collected, analysis I can explain, and validation that supports the result.

## Direction
Immediate employment lanes:
1. Financial Crime / Investigations
2. Networking / Network Operations
3. Security Operations / Cyber Investigation

Long-term progression: **Cyber Investigation → DFIR → Threat Hunting → Offensive Security → Red Team**

## Repository Architecture
Proof is organized by capability:
`00-lab-infrastructure`, `01-networking`, `02-network-security`, `03-linux`, `04-windows`, `05-security-foundations`, `06-security-operations`, `07-data-analysis`, `08-financial-crime`, `09-dfir`, `10-threat-hunting`, `11-offensive-security`, `12-cloud`, and `tools`.

Directories are populated when evidence earns them.

## Proof Workflow
**Identify capability → Perform work → Collect evidence → Analyze → Validate → Sanitize → Document → Publish**

Projects should demonstrate realistic job behavior: troubleshooting, investigation, analysis, configuration, validation, remediation, or reporting.

## Employer Alignment
Before building a substantial artifact, ask: **What hiring claim will this prove?**

Each project identifies employer-relevant skills. Prefer work supporting at least one primary lane and, where practical, a second lane. Periodically compare the portfolio with current job requirements and add recurring requirements to the backlog.

## Evidence Standard
A screenshot, README, or command alone is not proof.

Strong proof connects: **Objective → Action → Machine-generated evidence → Analysis → Validation → Finding**

Evidence may include terminal output, configurations, logs, PCAPs, exported data, scripts, queries, screenshots, before/after state, troubleshooting evidence, diagrams, timelines, findings, and remediation validation.

## Evidence Handling and Privacy
Raw evidence is reviewed before publication. Local raw-evidence directories are excluded from version control.

Omit or sanitize information that does not contribute to the proof: passwords, private keys, tokens, secrets, unnecessary MAC addresses or SSIDs, serial numbers, account identifiers, unnecessary public IPs, and other sensitive identifiers.

Private RFC1918 addressing may remain when it materially explains the architecture. Review exact staged content before every public commit.

## Failure and Troubleshooting
Failure can be useful evidence. When meaningful, document:
**Expected → Observed → Investigated → Root Cause → Change → Validation**

Do not manufacture failures for appearance.

## Writing Standard
Published work represents work I actually performed and understand. If I did not run, observe, test, analyze, or learn it, the artifact cannot claim that I did.

Every technical claim published under my name should be something I can explain and defend in an interview.

## Training and SANS Material
Training is an input, not a public artifact. Do not publish proprietary books, copied labs, answer keys, exam questions, copyrighted training screenshots, proprietary VM images, or proprietary datasets.

When training teaches a useful capability: **Learn → Apply independently → Publish original proof → Integrate strong capabilities into flagships.**

## Flagship Projects
The body-of-proof repo demonstrates breadth; separate flagship repos demonstrate depth:
- Enterprise Network Engineering & Troubleshooting
- Applied Cybersecurity / GFACT Capstone
- Financial Crime Investigation
- GSEC Security Operations Capstone
- Cyber-Enabled Financial Crime Investigation
- Enterprise DFIR Investigation

Create flagships when enough real work exists to justify them.

## Gaps and Backlog
`GAPS.md` records weaknesses exposed while performing or explaining work. `BACKLOG.md` tracks capabilities, projects, and employer requirements still needing strong proof.

## Status
- **QUEUED** — planned, not started
- **ACTIVE** — currently being built
- **THIN** — some evidence exists; stronger proof needed
- **PROVEN** — published evidence demonstrates the capability and I can explain it

## Lab Scope and Safety
Offensive testing is only against systems I own or am explicitly authorized to test. Production and family systems are not targets. Scope must be known before testing begins.

## End State
Build evidence that I can understand and troubleshoot systems/networks, secure them, detect suspicious behavior, investigate technical and financial evidence, reconstruct incidents, hunt adversaries, understand attacks from defensive and offensive perspectives, and communicate findings and business impact.

**Prove it.**
