# 💻 Build the Victim Machine

## Overview

I learned how to prepare a Windows victim machine for controlled
cybersecurity and digital forensics exercises.

The victim machine was configured as an isolated test environment
where controlled activities could be performed and the resulting
Windows artifacts could later be collected and analyzed.

## What I Learned

- Preparing a Windows test environment
- Configuring a Windows virtual machine for laboratory exercises
- Creating a known clean state
- Using VM snapshots
- Preparing a system for controlled security experiments
- Generating realistic system activity for forensic analysis
- Understanding how system activity creates forensic artifacts

## 🛠️ Tools and Technologies

- Oracle VirtualBox
- Sysmon
- Atomic Red Team
- Windows Event Viewer
- PowerShell
- Command Prompt

## 🧪 Atomic Red Team

I installed and configured Atomic Red Team in the controlled
victim environment.

Atomic Red Team provides a framework for simulating known
adversary techniques in a controlled laboratory environment.
This allowed me to generate security-related activity on the
Windows system and subsequently examine the artifacts produced
by those activities.

The generated activity can be used for learning how defensive
and forensic tools detect, collect, and analyze evidence.

## 💻 Virtual Machine Snapshots

I also learned how to use VirtualBox snapshots to preserve a
known state of the victim machine.

Snapshots allow the lab environment to be restored to an earlier
state after completing controlled experiments.

## 🔍 Forensic Purpose

The victim machine acts as the system on which controlled
activities are performed.

The resulting artifacts can then be acquired and analyzed using
forensic tools as part of the investigation process.

## Key Takeaway

Building a dedicated victim machine provides a controlled and
repeatable environment for security testing and digital forensics.

Using Atomic Red Team helped me understand how simulated
adversary activity can generate artifacts that can later be
investigated during forensic analysis.
