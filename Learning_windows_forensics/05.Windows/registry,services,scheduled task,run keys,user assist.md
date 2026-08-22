# Windows Registry Forensics

## Overview

I studied the Windows Registry and its importance in digital
forensics.

The Windows Registry contains configuration and historical
information that can help investigators identify:

- User activity
- Connected USB devices
- Installed and uninstalled software
- Program execution
- Auto-start locations
- Services
- Recently accessed files
- Network configuration
- Malware persistence

The Registry was designed for Windows configuration, but it also
preserves historical traces that can be valuable during forensic
investigations.

---

## 1. Windows Registry

The Windows Registry is a hierarchical database used by Windows
to store configuration information.

It consists of:

- Keys
- Subkeys
- Values
- Data
- Registry Hives

A Registry Hive is a collection of Registry keys, subkeys, and
values stored together in a file on disk.

---

## 2. Registry Root Keys

Windows has five primary Registry root keys.

### HKEY_CURRENT_USER (HKCU)

Contains configuration and activity related to the currently
logged-in user.

Examples include:

- User settings
- Control Panel preferences
- Application settings
- Explorer activity
- Recently accessed files

### HKEY_USERS (HKU)

Contains the actively loaded user profiles.

`HKCU` is essentially a shortcut to the currently active user's
profile under `HKU`.

### HKEY_LOCAL_MACHINE (HKLM)

Contains system-wide configuration.

Examples include:

- Hardware
- Installed software
- Drivers
- Services
- Startup settings
- Security settings

### HKEY_CLASSES_ROOT (HKCR)

Contains information related to:

- File associations
- Object linking
- Which application opens a particular file type

### HKEY_CURRENT_CONFIG (HKCC)

Contains information about the hardware profile currently being
used by the system during startup.

---

## 3. Registry Hives

Important Registry hives studied:

```text
C:\Windows\System32\config\SYSTEM
C:\Windows\System32\config\SOFTWARE
C:\Windows\System32\config\SECURITY
C:\Windows\System32\config\SAM
C:\Users\<username>\NTUSER.DAT
C:\Users\<username>\AppData\Local\Microsoft\Windows\UsrClass.dat
```

These hives can contain evidence related to:

- User activity
- Installed software
- Services
- USB devices
- Persistence
- Program execution
- Files viewed in Explorer

---

## 4. Registry Editor

The built-in Windows Registry utility is:

```text
regedit.exe
```

Registry Editor allows users to view and modify the live Registry.

For forensic investigations, analysis is generally performed on
copied/offline Registry hives because interacting with the live
Registry can potentially alter evidence.

---

# 5. Registry Analysis Tools

## Registry Explorer

Registry Explorer is a GUI-based forensic tool for analyzing
offline Registry hives.

It can:

- Load multiple Registry hives
- Search across hives
- Search using strings
- Search using regular expressions
- Filter results
- Export data
- View Registry information
- Examine LastWrite times

I used Registry Explorer to process:

- SYSTEM
- SECURITY
- SOFTWARE
- SAM
- NTUSER.DAT
- UsrClass.dat

---

## RegRipper

RegRipper is a plugin-based tool used to parse Registry hives
and extract known forensic artifacts into readable reports.

It helps investigators analyze multiple Registry artifacts without
manually navigating every Registry path.

### RegRipper Command-Line Analysis

I studied how Registry hives can be recursively processed using
PowerShell.

Example:

```powershell
for /r %i in (*) do (
    rip.exe -r %i -a > %i.txt
)
```

The generated text reports can then be analyzed using Notepad++.

### RegRipper GUI

The GUI workflow studied was:

1. Run `rr.exe` as Administrator.
2. Select the Registry hive.
3. Select the output location.
4. Select `Rip!`.
5. Analyze the generated report.

---

## 6. Notepad++ Analysis

The RegRipper-generated `.txt` files can be opened and searched
using Notepad++.

Searching multiple reports makes it easier to identify:

- Persistence
- Program execution
- Services
- Scheduled Tasks
- Suspicious software
- Other Registry artifacts

---

# 7. Registry Forensic Artifacts

## Run Keys

Run Keys are important Windows Registry artifacts used for
automatic program execution.

They are also commonly abused as persistence mechanisms.

Important locations:

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Run

HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKLM\Software\Microsoft\Windows\CurrentVersion\Run

HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

Each Run Key entry can contain:

- Application name
- Command to execute
- Parameters

Run Key analysis can help determine which programs are configured
to start automatically.

Attackers may also modify or delete Run Key entries to hide their
activity.

---

## AtomicRedTeam Persistence

During the practical analysis, I used Registry Explorer to
navigate to the Run Key location.

A suspicious:

```text
AtomicRedTeam.exe
```

entry was identified as a persistence mechanism.

The same information could also be searched in RegRipper output
using:

```text
CurrentVersion\Run
```

---

# 8. Windows Services

Windows Services are long-running applications that operate in
the background.

They can:

- Start automatically
- Run in the background
- Be paused
- Be restarted

Services are normally used for legitimate Windows functionality,
but attackers can abuse them for:

- Persistence
- Malicious code execution
- Privileged execution
- Hiding malicious operations
- Maintaining access

Important Registry location:

```text
HKLM\SYSTEM\CurrentControlSet\Services
```

Service entries contain information such as:

- Service name
- Display name
- Executable path
- Start type
- Permissions

Attackers may disguise malicious services using names that resemble
legitimate Windows services.

Services can also be modified or deleted to hide evidence.

### RegRipper Service Analysis

I learned that RegRipper output can be searched for:

```text
svc v.
```

This can help identify installed services and associated
timestamps.

---

# 9. Scheduled Tasks

Scheduled Tasks allow Windows to automatically execute programs
or scripts at specified times or intervals.

They are commonly used for legitimate system maintenance but can
also be abused by attackers for:

- Persistence
- Automated execution
- Malicious code execution
- Data exfiltration
- Covering tracks

Scheduled Tasks record information such as:

- Task name
- Triggers
- Actions
- Execution times
- User account

### Scheduled Task Files

Scheduled Task XML files are stored in:

```text
C:\Windows\System32\Tasks
```

### Registry Locations

Scheduled Task information is also stored under:

```text
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tasks

HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree
```

The same information can also be found in RegRipper output.

I also learned that the KAPE collection can be used to locate
Scheduled Task artifacts.

---

# 10. Scheduled Task Analysis

One challenge is distinguishing legitimate Scheduled Tasks from
malicious ones.

Many applications and Windows components create legitimate tasks,
so the task must be examined in context.

Attackers may:

- Modify tasks
- Disable tasks
- Delete tasks
- Use tasks for persistence
- Use tasks to execute malicious programs
- Use tasks to hide their activity

Therefore, Scheduled Task evidence should be correlated with other
forensic artifacts.

---

# 11. Case Study: Run Key Persistence

## Scenario

A law firm experienced a data breach where sensitive client
information was leaked.

Investigators suspected that a compromised workstation was being
used to exfiltrate data but initially did not know how persistence
was maintained.

## Initial Findings

Investigators found:

- Suspicious outbound network connections
- Antivirus alerts for a malicious executable
- Repeated malware appearance after removal
- Evidence of compromised credentials

## Run Key Investigation

Investigators used:

- Autoruns
- Registry Explorer

They examined:

```text
HKLM\Software\Microsoft\Windows\CurrentVersion\Run

HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

A suspicious entry named:

```text
Updater
```

was found.

It executed:

```text
C:\Users\Public\Documents\Updater.exe
```

The executable was identified as the same malware detected by
antivirus.

It executed whenever the user logged in, causing the malware to
reappear after removal.

## Timeline Reconstruction

The creation and modification timestamps of the Run Key showed
that the `Updater` entry was created shortly after the first
successful compromise.

This suggested that the Run Key was used to establish persistence
shortly after the initial breach.

## Additional Malicious Run Key

Another entry named:

```text
SystemMonitor
```

was associated with a keylogger.

The keylogger captured keystrokes and transmitted them to the
attacker's server.

## Corroborating Evidence

Investigators correlated the Run Key evidence with:

- Windows Event Logs
- Network Logs
- File-System Logs
- Prefetch
- Scheduled Tasks

The evidence showed that the Run Keys were actively used to
launch malicious software.

## Conclusion

Run Key analysis helped investigators:

- Identify persistence
- Identify unauthorized software
- Reconstruct the attack timeline
- Understand attacker activity
- Correlate multiple forensic artifacts

---

# 12. Case Study: Scheduled Task Persistence

## Scenario

A manufacturing company discovered that critical design files
were accessed and transferred to an unauthorized external server.

The activity occurred during a weekend when employees were not
scheduled to work.

## Initial Findings

Investigators found:

- Large outbound data transfers
- Repeated access to design files
- Unauthorized file-transfer software

The activity suggested that an automated process was responsible.

## Scheduled Task Investigation

Investigators used:

- Task Scheduler Viewer
- Registry Explorer

They examined:

```text
C:\Windows\System32\Tasks
```

and:

```text
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tasks
```

## Malicious Scheduled Task

A task named:

```text
SystemMaintenance
```

was found.

It executed a hidden executable from a non-standard directory.

The executable was identified as unauthorized file-transfer
software.

The task was configured to run at regular intervals during
weekends, matching the timeline of the data transfers.

## Timeline Reconstruction

The task was created shortly after the engineer's last legitimate
login on Friday evening.

This suggested that stolen credentials may have been used to create
the task.

## Additional Malicious Task

Another task named:

```text
Windows Update Check
```

was discovered.

Instead of checking for Windows updates, it executed a script
designed to:

- Delete logs
- Clear evidence
- Conceal malicious activity

The task ran immediately after the data transfer operations.

## Corroborating Evidence

The Scheduled Task evidence was correlated with:

- Windows Event Logs
- Network Logs
- File Access Logs
- Prefetch
- Shim Cache

The timestamps matched the outbound network activity and repeated
access to the design files.

Prefetch and Application Compatibility Cache entries also
confirmed execution of the unauthorized tools.

## Conclusion

Scheduled Task analysis helped investigators:

- Identify persistence
- Identify automated malicious activity
- Reconstruct the attack timeline
- Identify data exfiltration
- Identify attempts to cover tracks
- Confirm unauthorized access

---

# 13. UserAssist

UserAssist is a Windows Registry artifact that records execution
of GUI-based applications.

It can provide information about:

- Applications opened
- Frequency of execution
- Last execution time
- Executable paths
- User activity

UserAssist can be useful in:

- Insider-threat investigations
- Malware investigations
- Data-exfiltration investigations
- User activity reconstruction

## UserAssist Registry Path

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist
```

UserAssist contains GUID-based subkeys.

The values recording executed programs are stored using:

```text
ROT13
```

ROT13 is a simple substitution cipher that can be decoded to
identify the original values.

Each entry can contain:

- Last execution timestamp
- Execution count
- Executable path

UserAssist can therefore help establish whether and when an
application was executed.

---

# 14. Artifact Correlation

Registry analysis should not be performed in isolation.

I learned to correlate Registry evidence with other Windows
forensic artifacts such as:

- Windows Event Logs
- Prefetch
- Scheduled Tasks
- Browser History
- PowerShell Logs
- File-System Timestamps
- Memory Artifacts
- Network Activity
- File-System Changes
- Shim Cache

Correlating multiple sources helps investigators reconstruct:

- User activity
- Program execution
- Attacker behavior
- Persistence
- System events
- Attack timelines

---

# 15. Tools Studied

- Registry Editor (`regedit.exe`)
- Registry Explorer
- RegRipper
- PowerShell
- Notepad++
- Autoruns
- Task Scheduler Viewer
- KAPE

---

# Key Takeaways

- Learned the structure of the Windows Registry.
- Studied Registry keys, subkeys, values, data, and hives.
- Studied all five primary Registry root keys.
- Learned the locations of important Registry hives.
- Learned how to analyze offline Registry hives.
- Studied Registry Explorer.
- Studied RegRipper.
- Learned command-line and GUI-based Registry analysis.
- Learned how to search RegRipper reports using Notepad++.
- Studied Run Keys and RunOnce keys.
- Identified `AtomicRedTeam.exe` as a persistence example.
- Studied Windows Services as forensic artifacts.
- Studied Scheduled Tasks and TaskCache.
- Studied KAPE collection locations for Scheduled Tasks.
- Studied UserAssist and ROT13 encoding.
- Analyzed Run Key persistence.
- Analyzed Scheduled Task persistence.
- Studied how persistence can be used for malicious execution.
- Learned how multiple forensic artifacts can be correlated to
  reconstruct an incident.

---

# Learning Status

**Status: Completed**

This module helped me understand how Windows Registry artifacts
can be examined using forensic tools and correlated with other
Windows artifacts to investigate user activity, program execution,
persistence, and attacker behavior.
