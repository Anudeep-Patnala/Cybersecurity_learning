# Threat Emulation and Data Acquisition 

## Overview

I learned how cyber threat emulation can be used in a controlled
Windows laboratory environment to generate realistic activity and
then investigate the forensic artifacts created by that activity.

The main objective was to understand the relationship between:

Threat Activity → Artifact Generation → Evidence Acquisition → Analysis

Instead of studying forensic artifacts only from theory, I performed
controlled activities on a Windows virtual machine and then collected
the resulting evidence for forensic analysis.

## Cyber Threat Emulation

Cyber threat emulation involves safely simulating realistic
attacker behavior inside a controlled laboratory environment.

The purpose of the exercise was not to perform attacks against
real systems, but to intentionally generate forensic artifacts
that could later be collected and analyzed.

The simulated activity can generate artifacts such as:

- Windows Event Logs
- Registry modifications
- Scheduled Tasks
- Browser History
- Prefetch Files
- Jump Lists
- PowerShell Logs
- File-System Changes
- File-System Timestamps
- Network Activity
- Memory Artifacts
- Persistence-related artifacts

## Atomic Red Team

Atomic Red Team is an open-source adversary emulation framework
used to safely test and validate defensive capabilities.

It provides small, focused tests called "atomics". These tests
simulate individual attacker techniques mapped to the MITRE ATT&CK
framework.

In this lab, Atomic Red Team was used primarily as a forensic
artifact generation platform.

The simulated activities allowed me to observe how attacker
techniques can produce forensic traces on a Windows system.

## 🧪 Lab Environment

The exercise was performed inside an isolated virtual laboratory.

### Victim Machine

- Windows 11
- Oracle VirtualBox
- Atomic Red Team
- PowerShell

### Forensic Collection Environment

- Forensic Triage USB
- KAPE
- DumpIt
- HashMyFiles
- Forensic Workstation

## ⚔️ Threat Emulation

I used the Atomic Red Team attack simulation in the Windows victim
machine to generate controlled security-related activity.

The exercise demonstrated how activities such as:

- PowerShell execution
- Credential-access attempts
- Persistence mechanisms
- Scheduled tasks
- Registry modifications
- Suspicious process activity

can create artifacts throughout the Windows operating system.

## PowerShell

PowerShell was used as part of the threat-emulation process.

I learned how PowerShell activity can itself become a source of
forensic evidence through PowerShell logs and other Windows
artifacts.

## 🔬 Artifact Generation

One of the most important concepts I learned was the direct
relationship between an activity and the artifacts it creates.

For example:

Activity
→ Process execution
→ Event logs
→ File-system changes
→ Registry changes
→ Other forensic traces

This helped me understand how forensic investigators can work
backward from artifacts to reconstruct activity on a system.

## 💾 Triage Collection

After performing the controlled threat-emulation activity, I
used the prepared forensic triage USB to collect evidence from
the victim machine.

The triage USB contained the required forensic tools.

The collected evidence was stored in the USB's Evidence folder.

## 🧠 Memory Acquisition

I learned how to acquire volatile memory from the Windows victim
machine using DumpIt.

The DumpIt utility was executed from the Tools directory using
PowerShell with administrative privileges.

Example command used in the lab:

```powershell
.\DumpIt.exe /Q /O E:\Evidence\memory.dmp
