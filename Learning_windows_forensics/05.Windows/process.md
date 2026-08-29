# Windows Processes & Process Tree — DFIR

## Introduction to Windows Processes

When Windows starts, it does not simply launch a collection of random programs. Instead, it creates a highly structured **process tree**, where each process has a parent process that launched it.

Understanding these **parent-child relationships** is one of the most important skills in Windows forensics because attackers can abuse legitimate Windows processes to hide malicious activity.

The main objective is:

> **Understand what normal Windows activity looks like so that abnormal activity can be identified during an investigation.**

---

# 1. System Informer

**System Informer** is a free, multi-purpose Windows tool used for:

- Monitoring system resources
- Debugging software
- Detecting malware
- Monitoring CPU usage
- Monitoring memory usage
- Monitoring disk activity
- Monitoring network activity
- Process management
- Service control

It supports **Windows 10 and higher** and works with both **32-bit and 64-bit systems**.

System Informer can be used to view the process tree on a **live Windows system**.

For memory forensics, tools such as:

- **Volatility**
- **MemProcFS**

can be used to analyze processes from memory.

---

# 2. What Is Normal?

Before identifying suspicious processes, an analyst needs to understand what a normal Windows system looks like.

Common Windows processes include:

- `System`
- `smss.exe`
- `wininit.exe`
- `RuntimeBroker.exe`
- `taskhostw.exe`
- `winlogon.exe`
- `explorer.exe`
- `csrss.exe`
- `services.exe`
- `svchost.exe`
- `lsaiso.exe`
- `lsass.exe`

These processes perform important operating-system, security, authentication, service, and user-interface functions.

### Important Principle

Seeing a process with a familiar Windows name does **not automatically mean that it is safe**.

Analysts should look for anomalies such as:

- Misspelled process names
- Unexpected file paths
- Unusual parent processes
- Strange command lines
- Processes running from temporary folders
- Processes running from user-writable locations

The goal is to establish a **baseline of normal Windows activity**.

Once a baseline is understood, suspicious activity becomes easier to identify.

---

# 3. Normal Windows Process Tree

A simplified Windows process tree looks like:

```text
System (PID 4)
│
└── smss.exe
    ├── csrss.exe
    │
    └── wininit.exe
        ├── services.exe
        │   ├── svchost.exe
        │   ├── svchost.exe
        │   ├── spoolsv.exe
        │   └── Other Services
        │
        └── lsass.exe
```

Another important relationship is:

```text
System
└── smss.exe
    └── wininit.exe
        ├── services.exe
        └── lsass.exe
```

Understanding these relationships is extremely useful during DFIR investigations.

---

# 4. System — PID 4

## Process

```text
System
```

### PID

```text
4
```

### Parent

```text
Windows Kernel
```

The `System` process is created directly by the Windows kernel. It represents kernel-mode activity and forms the foundation for many operating-system functions.

### Responsibilities

- Manages kernel-mode threads
- Handles drivers
- Handles memory
- Handles hardware
- Performs low-level operating-system functions

### Forensic Notes

- Normally always has **PID 4**
- Created by the Windows kernel
- Exists for the entire system uptime
- Many driver-related activities appear associated with PID 4
- It normally has no user-mode parent process

---

# 5. smss.exe — Session Manager Subsystem

## Process

```text
smss.exe
```

### Parent

```text
System (PID 4)
```

Process relationship:

```text
System
└── smss.exe
```

`smss.exe` is the **first user-mode process created during Windows boot**.

### Responsibilities

- Creates user sessions
- Starts essential Windows subsystems
- Initializes environment variables
- Launches critical startup processes

Think of `smss.exe` as the process that prepares the Windows environment before the rest of the system becomes operational.

### Expected Location

```text
C:\Windows\System32\smss.exe
```

### Forensic Notes

Normally:

```text
Parent = PID 4
```

It should launch important processes such as:

```text
smss.exe
├── csrss.exe
└── wininit.exe
```

### Suspicious Condition

If `smss.exe` is running from a location other than:

```text
C:\Windows\System32\smss.exe
```

it should be considered **extremely suspicious**.

---

# 6. csrss.exe — Client Server Runtime Process

## Process

```text
csrss.exe
```

### Parent

```text
smss.exe
```

Process relationship:

```text
smss.exe
└── csrss.exe
```

### Responsibilities

`csrss.exe` handles:

- Console windows
- Thread creation
- Parts of the Windows subsystem
- Command prompt interactions

Without `csrss.exe`, Windows cannot function correctly.

### Expected Location

```text
C:\Windows\System32\csrss.exe
```

### Expected Parent

```text
smss.exe
```

### Look-Alike Process Names

Attackers may create processes with names that look similar to legitimate Windows processes.

Examples:

```text
csrsss.exe
crss.exe
csrss1.exe
```

These names may be used in an attempt to trick an analyst into thinking the process is legitimate.

### DFIR Tip

Do not rely only on the process name.

Verify:

- Process name
- Parent process
- File path
- Command line
- Digital signature
- Overall behavior

---

# 7. wininit.exe — Windows Initialization Process

## Process

```text
wininit.exe
```

### Parent

```text
smss.exe
```

Process relationship:

```text
smss.exe
└── wininit.exe
```

After the basic startup process is complete, `wininit.exe` takes over system initialization.

### Responsibilities

It launches important service and security-related processes such as:

```text
wininit.exe
├── services.exe
└── lsass.exe
```

The material also identifies `lsm.exe` as part of the traditional startup relationship.

### Expected Location

```text
C:\Windows\System32\wininit.exe
```

### Expected Parent

```text
smss.exe
```

---

# 8. services.exe — Service Control Manager

## Process

```text
services.exe
```

### Parent

```text
wininit.exe
```

Process relationship:

```text
wininit.exe
└── services.exe
```

`services.exe` is the **Service Control Manager (SCM)**.

It is one of the most important processes on a Windows system.

Think of `services.exe` as the **Operations Manager** for Windows services.

### Responsibilities

- Start services
- Stop services
- Monitor services
- Restart failed services
- Manage Windows services

Every Windows service is ultimately managed by `services.exe`.

### Examples of Services

Examples include:

- Windows Update
- DHCP Client
- DNS Client
- Print Spooler
- Security Software
- Atomic Red Team Service, if installed

---

# 9. Services and Persistence

Services are important from a DFIR perspective because attackers can abuse them for persistence.

Attackers may use services because they can:

- Run automatically
- Run as `SYSTEM`
- Survive system reboots

A common service-based persistence example is:

```text
sc create EvilService ...
```

### Expected Relationship

```text
wininit.exe
└── services.exe
```

An analyst should investigate unexpected services and their associated executable paths.

---

# 10. svchost.exe — Service Host

## Process

```text
svchost.exe
```

### Parent

```text
services.exe
```

Process relationship:

```text
services.exe
└── svchost.exe
```

Windows services do not normally all run directly inside `services.exe`.

Instead, Windows groups services into containers called:

```text
svchost.exe
```

Think of `svchost.exe` as an **office building**.

The individual Windows services are the employees working inside that building.

### Example

```text
svchost.exe
├── DHCP Service
├── DNS Client
├── Windows Time
└── Event Log
```

Modern Windows systems may have **dozens of `svchost.exe` processes**.

Therefore:

> Seeing many `svchost.exe` processes is normal.

### Expected Location

```text
C:\Windows\System32\svchost.exe
```

### Expected Parent

```text
services.exe
```

### Suspicious Locations

A `svchost.exe` running from locations such as:

```text
C:\Users\
C:\Temp\
Downloads\
```

should be treated as highly suspicious.

The legitimate Windows `svchost.exe` should normally be located in:

```text
C:\Windows\System32\
```

---

# 11. lsass.exe — Local Security Authority Subsystem Service

## Process

```text
lsass.exe
```

### Parent

```text
wininit.exe
```

Process relationship:

```text
wininit.exe
└── lsass.exe
```

`lsass.exe` can be considered the **security brain of Windows**.

### Responsibilities

It handles:

- User authentication
- Password validation
- Kerberos tickets
- NTLM authentication
- Security tokens
- Security policy

A simplified authentication flow is:

```text
Username
   ↓
lsass.exe
   ↓
Authentication
```

### Expected Location

```text
C:\Windows\System32\lsass.exe
```

### Expected Parent

```text
wininit.exe
```

---

# 12. Why LSASS Is Important in DFIR

LSASS is one of the most targeted Windows processes by attackers.

Its memory may contain sensitive authentication information.

The material mentions tools such as:

- **Mimikatz**
- **NanoDump**
- **ProcDump**

which can be used to dump LSASS memory.

Therefore, analysts should pay close attention to:

- The LSASS process
- Its parent process
- Its location
- Processes interacting with LSASS
- Suspicious memory-access activity

---

# 13. spoolsv.exe — Print Spooler

## Process

```text
spoolsv.exe
```

### Parent

```text
services.exe
```

Process relationship:

```text
services.exe
└── spoolsv.exe
```

### Purpose

`spoolsv.exe` manages print jobs.

Basic workflow:

```text
User
 ↓
Printer Queue
 ↓
spoolsv.exe
 ↓
Printer
```

### Forensic Importance

The Print Spooler has historically been targeted by attackers because of vulnerabilities.

One famous example is:

```text
PrintNightmare
```

Many organizations disable the Print Spooler service on servers where printing is unnecessary.

### Expected Parent

```text
services.exe
```

---

# 14. winlogon.exe

## Process

```text
winlogon.exe
```

`winlogon.exe` is responsible for managing:

- Interactive user logons
- Lock screens
- User authentication events

It is part of the normal Windows process environment.

The process tree should be considered when investigating abnormal activity involving logon-related processes.

---

# 15. explorer.exe

## Process

```text
explorer.exe
```

After a user logs in, `explorer.exe` is launched as part of the Windows user shell initialization.

### Responsibilities

It provides the Windows user interface, including:

- Desktop
- Taskbar
- Start menu
- File Explorer

It is one of the most familiar processes on a Windows desktop system.

### DFIR Consideration

An analyst should not only check whether `explorer.exe` exists.

They should also verify:

- Its parent process
- Its executable path
- Its command line
- Its behavior

An `explorer.exe` process launched from an unusual location may indicate suspicious activity.

---

# 16. RuntimeBroker.exe

`RuntimeBroker.exe` is associated with modern Windows applications.

### Responsibilities

It manages permissions for **Universal Windows Platform (UWP)** applications.

It helps control access to resources such as:

- Microphone
- Camera
- Files

It is normally part of a Windows system.

---

# 17. taskhostw.exe

`taskhostw.exe` is commonly launched by `svchost.exe`.

It hosts:

- Scheduled tasks
- Background tasks
- Background components used by Windows
- Components used by installed applications

---

# 18. lsaiso.exe

`lsaiso.exe` is associated with **Credential Guard** when Credential Guard is enabled.

### Parent

```text
wininit.exe
```

### Purpose

It helps protect sensitive authentication information by isolating credentials from the rest of the operating system.

---

# 19. csrss.exe, services.exe, svchost.exe and Security Processes

Several processes are especially important when understanding the Windows process tree.

```text
System
│
└── smss.exe
    ├── csrss.exe
    │
    └── wininit.exe
        ├── services.exe
        │   ├── svchost.exe
        │   └── spoolsv.exe
        │
        └── lsass.exe
```

This relationship provides a baseline for investigating Windows systems.

---

# 20. Windows Startup Process Tree

The Windows startup process tree can be represented as:

```text
System (PID 4)
│
└── smss.exe
    ├── csrss.exe
    │
    └── wininit.exe
        ├── services.exe
        │   ├── svchost.exe
        │   ├── svchost.exe
        │   ├── spoolsv.exe
        │   └── Other Services
        │
        └── lsass.exe
```

---

# 21. Process Tree — Why It Matters

The most important forensic question is:

> **Who launched this process?**

A process may have a legitimate name but still be suspicious if its parent process is unexpected.

### Example of a Normal Relationship

```text
wininit.exe
└── services.exe
    └── svchost.exe
```

This is an expected Windows relationship.

### Example of a Suspicious Relationship

```text
winword.exe
└── powershell.exe
    └── cmd.exe
        └── svchost.exe
```

This process chain should be investigated because it represents an unusual relationship between applications and system processes.

Another suspicious example:

```text
chrome.exe
└── lsass.exe
```

This relationship should not normally occur.

---

# 22. Indicators of Suspicious Processes

When examining a process, do not focus only on the process name.

Check the following:

## Process Name

Look for:

- Misspelled names
- Look-alike names
- Unexpected process names

Example:

```text
csrss.exe   → Expected
csrsss.exe  → Suspicious
crss.exe    → Suspicious
csrss1.exe  → Suspicious
```

## Parent Process

Ask:

```text
Who launched this process?
```

For example:

```text
Expected:

wininit.exe
└── services.exe
```

An unexpected parent process can indicate malicious activity.

## File Path

Verify that the executable is running from the expected Windows directory.

Example:

```text
C:\Windows\System32\svchost.exe
```

is expected.

Whereas:

```text
C:\Users\...\svchost.exe
C:\Temp\svchost.exe
Downloads\svchost.exe
```

should be investigated.

## Command Line

Analyze the command line used to launch the process.

Unexpected arguments or execution chains may indicate malicious behavior.

## Digital Signature

Verify the digital signature of the executable.

A legitimate process name does not guarantee that the executable itself is legitimate.

## Overall Behavior

The analyst should consider the process in context.

Look at:

- Parent process
- Child processes
- File location
- Command line
- Digital signature
- Overall process behavior

---

# 23. Process Names Can Be Abused

Attackers may create malware with names that closely resemble legitimate Windows processes.

For example:

```text
Legitimate:
csrss.exe

Possible malicious look-alikes:
csrsss.exe
crss.exe
csrss1.exe
```

This technique attempts to take advantage of an analyst quickly scanning process names.

Therefore:

> **Verify the process instead of trusting its name.**

---

# 24. Expected Process Locations

Important expected locations from the material:

```text
System
    → Windows Kernel

smss.exe
    → C:\Windows\System32\smss.exe

csrss.exe
    → C:\Windows\System32\csrss.exe

wininit.exe
    → C:\Windows\System32\wininit.exe

svchost.exe
    → C:\Windows\System32\svchost.exe

lsass.exe
    → C:\Windows\System32\lsass.exe
```

Unexpected executable locations should be investigated.

---

# 25. Process Tree Analysis in DFIR

When investigating a Windows system, analysts should compare the observed process tree against a known baseline.

### Questions to Ask

```text
1. Is the process name correct?
2. Who is the parent process?
3. Is the parent-child relationship expected?
4. Is the executable located in the expected directory?
5. What command line was used?
6. Is the executable digitally signed?
7. Does the process behave normally?
```

This approach can help identify:

- Malware
- Persistence mechanisms
- Abnormal execution
- Attacker activity

---

# 26. Four Startup Processes Every DFIR Analyst Should Memorize

A critical process chain is:

```text
System (PID 4)
        ↓
smss.exe
        ↓
wininit.exe
        ├── services.exe
        └── lsass.exe
```

### Memory Aid

```text
SYSTEM
   ↓
Starts Windows

SMSS
   ↓
Creates sessions

WININIT
   ↓
Initializes Windows

SERVICES
   ↓
Manages services

LSASS
   ↓
Handles security
```

A shorter memory aid:

> **System starts Windows → SMSS creates sessions → WININIT starts the operating system → SERVICES manages services → LSASS handles security.**

---

# 27. Quick Process Reference

| Process | Parent | Main Purpose | Expected Location |
|---|---|---|---|
| `System` | Windows Kernel | Kernel activity, drivers, memory and hardware | Kernel |
| `smss.exe` | `System` | Creates sessions and starts subsystems | `C:\Windows\System32\smss.exe` |
| `csrss.exe` | `smss.exe` | Console and Windows subsystem functions | `C:\Windows\System32\csrss.exe` |
| `wininit.exe` | `smss.exe` | System initialization | `C:\Windows\System32\wininit.exe` |
| `services.exe` | `wininit.exe` | Manages Windows services | Windows System32 |
| `svchost.exe` | `services.exe` | Hosts Windows services | `C:\Windows\System32\svchost.exe` |
| `lsass.exe` | `wininit.exe` | Authentication and security | `C:\Windows\System32\lsass.exe` |
| `lsaiso.exe` | `wininit.exe` | Credential Guard component | Windows System32 |
| `spoolsv.exe` | `services.exe` | Print Spooler | Windows System32 |
| `winlogon.exe` | `smss.exe` | Logon and authentication events | Windows System32 |
| `explorer.exe` | User shell initialization | Desktop and File Explorer | Windows System32 |
| `taskhostw.exe` | Commonly `svchost.exe` | Hosts tasks/background components | Windows System32 |
| `RuntimeBroker.exe` | Windows application-related processes | Application permissions | Windows System32 |

---

# 28. Normal vs Suspicious

## Normal

```text
System
└── smss.exe
    └── wininit.exe
        └── services.exe
            └── svchost.exe
```

Expected Windows process relationships should generally resemble the normal process tree.

## Suspicious

```text
winword.exe
└── powershell.exe
    └── cmd.exe
        └── svchost.exe
```

or:

```text
chrome.exe
└── lsass.exe
```

These relationships should be investigated because the parent-child relationship is abnormal.

---

# 29. Important DFIR Lessons

### Lesson 1 — Know the Baseline

Before searching for malware, understand normal Windows processes.

### Lesson 2 — Do Not Trust Process Names

Malware can use names that resemble legitimate Windows processes.

### Lesson 3 — Check Parent-Child Relationships

The parent process can provide important evidence about how a process was launched.

### Lesson 4 — Check the File Path

A legitimate process running from an unexpected location may be malicious.

### Lesson 5 — Check the Command Line

Command-line arguments can reveal suspicious execution.

### Lesson 6 — Verify Digital Signatures

A process should not be considered legitimate solely because it has a familiar name.

### Lesson 7 — Look at Overall Behavior

The process should be analyzed in context rather than in isolation.

---

# 30. Final DFIR Checklist

When you encounter a suspicious process:

```text
┌───────────────────────────────┐
│      PROCESS INVESTIGATION    │
├───────────────────────────────┤
│ ✓ Process name                │
│ ✓ Parent process              │
│ ✓ Parent-child relationship   │
│ ✓ Executable path             │
│ ✓ Command line                │
│ ✓ Digital signature           │
│ ✓ Overall behavior            │
└───────────────────────────────┘
```

The most important question remains:

> **Who launched this process?**

---

# 31. Key Takeaways

- Windows creates a structured process tree during startup.
- Each process normally has an expected parent.
- `System` normally has PID 4.
- `smss.exe` is the first important user-mode process created during boot.
- `smss.exe` creates important Windows subsystems.
- `wininit.exe` initializes the Windows environment.
- `services.exe` manages Windows services.
- `svchost.exe` hosts Windows services.
- Multiple `svchost.exe` processes are normal.
- `lsass.exe` handles authentication and security.
- LSASS is an important target during credential theft.
- `spoolsv.exe` manages the Print Spooler.
- `explorer.exe` provides the Windows desktop and File Explorer.
- `RuntimeBroker.exe` manages permissions for modern Windows applications.
- `taskhostw.exe` hosts scheduled tasks and background components.
- `lsaiso.exe` supports Credential Guard when enabled.
- Attackers may use misspelled process names to disguise malware.
- Unexpected process locations are important indicators.
- Unexpected parent-child relationships can indicate malicious activity.
- Process names alone are not enough for forensic analysis.
- Analysts should verify the parent process, path, command line, signature, and behavior.
- Establishing a normal baseline makes suspicious activity easier to identify.

---

# 🧠 DFIR Memory Cheat Sheet

```text
SYSTEM
  ↓
SMSS
  ↓
WININIT
  ├── SERVICES
  │     ├── SVCHOST
  │     └── SPOOLSV
  │
  └── LSASS
```

### Remember

```text
SYSTEM   → Starts Windows
SMSS     → Creates sessions
WININIT  → Initializes Windows
SERVICES → Manages services
LSASS    → Handles security
```

> **Know the tree. Know what's normal. Investigate what's different.**
