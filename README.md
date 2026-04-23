# HTB Labs — Attack Intelligence Repository
 
**Environment:** Hack The Box Starting Point + Progressive Machines
**Status:** Active — updated continuously as machines are completed
 
---
 
## What This Repository Is
 
This is a structured security learning repository built on top of Hack The Box lab machines.
 
Each machine completed produces a single intelligence report — not a walkthrough, not a command log.
The reports reconstruct **why** the attack worked, not just **what** was run.
 
The focus is on building reusable attacker thinking:
- Recognizing vulnerability patterns before exploitation
- Understanding root causes at the system level
- Mapping attack paths to defensive controls
---
 
## What You Will Find Here
 
For every machine completed, one write-up file containing:
 
**Attack Decision Chain**
The logical sequence of decisions that moved the attack forward — each step justified by what changed in the attacker's understanding of the target.
 
**Pattern Recognition Engine**
Abstract, reusable patterns extracted from each machine. Not machine-specific observations — portable mental models that apply across different services, environments, and difficulty levels.
 
**Root Cause Analysis**
Why the system was vulnerable at a technical and design level — not just what misconfiguration was present, but why that class of failure exists in real environments.
 
**Blue Team Mapping**
Specific fixes, class-level controls, and exact log events or monitoring signals that would detect the attack — written for a security analyst, not a student.
 
**Mistakes & Decision Corrections**
Real errors made during each machine — documented with root cause and a portable future rule. This section only exists when a real mistake occurred.
 
---
 
## Repository Structure
 
```text
htb-labs/
│
├── README.md
│
├── Fawn.md
│
├── Redeemer.md
│
├── Appointment.md
│
├── Sequel.md
│
└── [new machines added continuously]
```
 
One folder per machine. One file per folder. No noise.
 
---
 
## What This Is Not
 
- Not a step-by-step reproduction guide
- Not a command reference or cheat sheet
- Not a CTF solution dump
---
 
## Disclaimer
 
All activity performed on legal, isolated lab environments provided by Hack The Box.
No real-world systems targeted. For educational and portfolio purposes only.
 