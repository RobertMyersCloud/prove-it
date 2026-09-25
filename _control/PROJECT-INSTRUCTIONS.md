# Prove-It — Project Instructions

## Purpose

This repo is my body of proof.

Studying happens somewhere else. SANS notes, CCNA study, Linux study, books, videos, walkthroughs, and raw learning do not belong here.

This repo answers one question:

**Can I actually do the work?**

Every proving session must leave something in this repo that did not exist when I started. If nothing gets committed, I studied. I did not prove anything.

---

## No Skipped Floorboards

I am not skipping fundamentals to chase high-level cybersecurity work.

If I hit something I do not understand, I pull the floorboards up and figure out what is underneath it.

The cybersecurity ladder is climbed in order:

**Foundation → Offense → Defense → Forensics**

Something being taught out of order does not change where it belongs.

If SANS, CCNA, a book, a lab, or anything else teaches me an offensive concept while Foundation is still open, I learn it, record it in the backlog, and prove it when that rung opens.

The goal is eventually to take an attack and walk it backward from the evidence all the way to the person sitting in the chair, explaining what happened and how I know.

---

## The Three Runs

A Foundation cluster is not PROVEN because I completed it once.

### Run 1 — Guided

I can use documentation, notes, instruction, search, or other learning resources.

I operate the system myself.

The goal is to understand what I am doing, what the commands or tools are doing, and why the result happened.

### Run 2 — Coached

I start from the objective instead of a walkthrough.

I attempt the work myself first.

If I get stuck, I troubleshoot before asking for help. Assistance is allowed, but I should need less of it than Run 1.

The goal is to show that I am beginning to own the skill.

### Run 3 — Solo

I receive the objective and acceptance criteria only.

No walkthrough.

No copied commands.

No step-by-step help.

I build, troubleshoot, validate, and explain the result myself.

If I cannot do that, the skill is not PROVEN yet.

---

## Status

Every proof item has a status.

**QUEUED** — I have not started proving it.

**ACTIVE** — I am currently working it.

**THIN** — I have evidence or experience, but it is not enough to call the skill proven.

**PROVEN** — The evidence and required runs demonstrate that I can perform and explain the skill.

A GAP is different from a status.

A **GAP** means the proving process exposed something I do not actually understand or cannot perform independently.

The gap is recorded in `GAPS.md`, routed to a specific study block, learned outside the proving session, and then tested again.

---

## Backlog vs. Gap

`BACKLOG.md` is for knowledge or work I already have but have not properly captured or proven in this repo.

`GAPS.md` is for weaknesses discovered while proving.

Backlog means:

> I have done or learned this before, but GitHub cannot prove it yet.

Gap means:

> I thought I understood this, but the proving process showed me that I do not understand it well enough yet.

---

## Evidence Standard

A screenshot alone is not proof.

A README alone is not proof.

A command alone is not proof.

Strong proof connects:

**Objective → Action → Machine-generated evidence → Explanation → Validation**

Evidence can include:

- terminal output
- configuration files
- logs
- packet captures
- command history where appropriate
- screenshots
- exported data
- scripts
- system state before and after a change
- troubleshooting evidence
- diagrams supported by actual configuration
- written findings

Every piece of evidence should exist for a reason.

---

## Failure Is Evidence

I do not hide mistakes just to make a lab look clean.

If something fails, I troubleshoot it.

When the failure teaches something useful, the artifact should show:

**What I expected → What actually happened → What I checked → What I found → What I changed → How I proved the fix**

A clean result is useful.

A documented troubleshooting process can be even more useful.

---

## Writing Rule

The writeups must sound like me and represent work I actually performed.

Editing and drafting tools can be used to organize, clarify, shorten, and improve technical accuracy.

Those tools cannot fabricate my experience.

If I did not run it, see it, test it, or learn it, the writeup cannot claim that I did.

I must understand every technical claim published under my name.

The final artifact represents my actual work and my understanding.

---

## Assistance by Run

### Guided

Full instruction and learning resources are allowed.

I still operate the system and perform the work myself.

### Coached

I start from the objective and make a real attempt myself.

If I get stuck, I troubleshoot first.

Assistance is allowed after that, but the goal is to need less help than I did during the guided run.

### Solo

I receive the objective and acceptance criteria only.

I perform the work, troubleshoot problems, validate the result, and collect the evidence independently.

If I cannot complete the objective independently, the skill is not PROVEN yet.

---

## Training Material

SANS, certification courses, books, videos, and other training are inputs to this project, not artifacts themselves.

I do not publish proprietary course material, exam questions, copied labs, answer keys, books, or copyrighted training content.

When training teaches me something useful, I reproduce the skill independently in my own environment and capture my own evidence.

---

## Lab Scope and Safety

Offensive testing is performed only against systems I own or systems I have explicit authorization to test.

Production and family systems are not targets.

The lab boundary and target scope must be known before offensive testing begins.

If there is uncertainty about scope, the test stops until scope is clear.

---

## The End State

This project starts with fundamentals, but fundamentals are not the finish line.

I am building toward the ability to understand an event from every side:

**How the system normally works.**

**How it was attacked.**

**What the defender could see.**

**What evidence remained.**

**How an investigator could reconstruct what happened.**

No skipped steps.

No hand-waving.

Prove it.