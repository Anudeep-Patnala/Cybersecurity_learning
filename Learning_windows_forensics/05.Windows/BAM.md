# Windows Artifacts: BAM

## 1. What is BAM?

**BAM** stands for **Background Activity Monitor**.

BAM is a Windows component designed to monitor and manage background activity on the system.

Its main purpose is to help Windows:

- Track background processes
- Manage system resources
- Improve system performance
- Understand which applications are running in the background
- Determine how applications affect system performance

From a normal Windows administration perspective, BAM helps identify processes that consume resources such as:

- CPU
- Memory
- Disk activity
- Other system resources

From a **digital forensics** perspective, BAM is valuable because it can provide evidence of **program execution**.

BAM tracks executable files that have run on the system and associates them with timestamps indicating activity.

> BAM was not originally created for digital forensics. Microsoft designed it for performance monitoring and resource management. However, because it records information about executed applications, investigators can use it to reconstruct user and process activity.

---

## 2. BAM and Digital Forensics

BAM can be used as a source of evidence when investigating activity on a Windows system.

It can help investigators identify executable files that were executed during a particular timeframe.

For example, if an attacker executed tools such as:

- PowerShell
- Command Prompt utilities
- Remote access tools
- Malware droppers

BAM may contain evidence showing that those executables were run.

This makes BAM useful when investigating:

- Suspicious program execution
- Malware activity
- Attacker activity
- Unauthorized tools
- Incident timelines

---

## 3. Where is BAM Stored?

BAM data is stored inside the **SYSTEM Registry hive**.

A commonly used BAM Registry location is:

```text
HKLM\SYSTEM\CurrentControlSet\Services\bam\State\UserSettings
```

On newer versions of Windows, BAM-related information can be found under:

```text
HKLM\SYSTEM\CurrentControlSet\Services\bam\UserSettings
```

Inside these Registry keys, BAM stores information about executable files that have been run by users on the system.

The entries can contain:

- Full executable paths
- User-specific execution information
- Associated timestamps
- Evidence that the executable was launched

---

## 4. BAM as an Execution Artifact

BAM is another useful Windows artifact for investigating **program execution**.

It can be used alongside other Windows forensic artifacts such as:

- Prefetch
- UserAssist
- Shimcache
- Amcache

Using multiple artifacts together gives investigators stronger evidence when determining whether a program actually executed.

---

## 5. Why BAM Can Be Useful During an Investigation

BAM can be especially useful when other forensic artifacts are limited or unavailable.

For example:

- **Prefetch may be disabled**
- **Event logs may have rolled over**
- **UserAssist may not track command-line execution**
- **Attackers may attempt to delete other artifacts**

Even in these situations, BAM may still preserve useful evidence of execution activity.

This makes BAM an important artifact to check during a Windows forensic investigation.

---

## 6. Tools Used to Analyze BAM

The main tools used to analyze BAM in this lesson are:

- **Registry Explorer**
- **RegRipper**
- **Notepad++**

---

## 7. Analyzing BAM Using Registry Explorer

**Registry Explorer** can be used to load the Windows **SYSTEM Registry hive**.

After loading the hive, we can navigate directly to the BAM-related Registry keys.

BAM entries viewed in Registry Explorer typically display:

- Executable paths
- Associated timestamps

This allows investigators to identify:

- Which executables were launched
- Approximately when the activity occurred

### Examples of Executables That May Appear

BAM entries may show execution of tools or programs such as:

- PowerShell
- `cmd.exe`
- AtomicRedTeam tools
- Scripts
- Archive utilities
- Remote administration tools
- Malware executables

The timestamps from these entries can then be compared with other forensic artifacts to help reconstruct the overall timeline of events.

---

## 8. Analyzing BAM Using RegRipper

BAM information can also be found in **RegRipper output**.

If the SYSTEM Registry hive has already been parsed using RegRipper, the output files can be opened in **Notepad++**.

Search the output for:

```text
bam
```

This allows us to quickly find BAM-related entries extracted from the Registry.

The BAM entries can then be examined to identify relevant executable paths and associated execution information.

---

## 9. Limitations of BAM

BAM is useful, but it has some important limitations.

### BAM Does Not Capture Every Process Execution

BAM does **not capture every process execution** that occurs on a Windows system.

Therefore, if an executable is not present in BAM, we cannot automatically conclude that it was never executed.

### BAM Data Can Age Out

BAM data may eventually:

- Age out
- Be overwritten
- Disappear after system cleanup
- Disappear after extended system use

Because of this, BAM should always be treated as **one artifact within a larger investigation**.

It should not be used as the sole source of evidence.

---

## 10. BAM Shows Execution, Not Necessarily Malicious Intent

Another important point is that BAM primarily provides evidence of **execution**.

Seeing an executable listed in BAM does **not automatically mean that the executable was malicious**.

If an executable appears in BAM, investigators still need to determine:

- Who executed it?
- Why was it executed?
- Was it legitimate or malicious?
- What additional activity happened afterward?

For example, a legitimate administrator may execute PowerShell or a command-line tool.

Therefore, the presence of a program in BAM must always be interpreted in context.

---

## 11. Correlating BAM With Other Forensic Artifacts

BAM findings should be correlated with other Windows forensic artifacts.

Useful artifacts include:

- Prefetch
- UserAssist
- Event Logs
- Shimcache
- Amcache
- Scheduled Tasks
- Registry Run keys
- File system timestamps
- PowerShell logs
- MFT
- USN Journal data

Using multiple artifacts helps investigators confirm findings and reconstruct what happened on the system.

---

## 12. Example of BAM Correlation

Suppose BAM shows that a suspicious executable was executed.

Instead of relying only on BAM, we can investigate other artifacts.

### Prefetch

We can use Prefetch to determine information such as execution frequency.

### Event Logs

We can examine Event Logs to identify related process creation events.

### Registry Artifacts

We can examine Registry artifacts to determine whether persistence mechanisms were established.

By combining these findings, investigators can build a stronger understanding of the activity.

---

## 13. Timeline Correlation

Another important concept when analyzing BAM is **timeline correlation**.

BAM timestamps become much more valuable when they are aligned with timestamps from other evidence sources.

During an incident, investigators may build a timeline containing events such as:

```text
Initial malware execution
        ↓
File creation activity
        ↓
Persistence establishment
        ↓
Credential access attempts
        ↓
Lateral movement
        ↓
Cleanup activity
```

BAM contributes to this timeline by helping investigators identify **when executables were launched**.

---

## 14. Example Investigation Scenario

Imagine that an investigation is being performed for a suspected malware incident.

BAM contains an entry for a suspicious executable.

The investigator can then:

```text
BAM
 ↓
Identify suspicious executable
 ↓
Check execution timestamp
 ↓
Check Prefetch
 ↓
Check Event Logs
 ↓
Check Registry artifacts
 ↓
Check other relevant forensic artifacts
 ↓
Compare timestamps
 ↓
Build the incident timeline
```

This allows the investigator to move from a single piece of evidence to a broader understanding of the incident.

---

## 15. Important BAM Information

When examining BAM, investigators should pay attention to:

### Executable Path

The full path can help determine where the executable was located.

For example:

```text
C:\Users\<username>\Downloads\suspicious.exe
```

The location of a file can provide additional context about the activity.

### Timestamp

The associated timestamp can help determine approximately when the executable activity occurred.

### User Information

User-specific information can help connect the activity to a particular user context.

### Execution Evidence

The BAM entry can provide evidence that the executable was likely launched on the system.

---

## 16. BAM Investigation Process

A basic investigation can be performed using the following process:

```text
Acquire the SYSTEM Registry hive
            ↓
Locate BAM Registry keys
            ↓
Open the SYSTEM hive in Registry Explorer
            ↓
Navigate to the BAM-related keys
            ↓
Examine BAM entries
            ↓
Identify executable paths
            ↓
Review associated timestamps
            ↓
Parse the SYSTEM hive using RegRipper
            ↓
Open the RegRipper output in Notepad++
            ↓
Search for "bam"
            ↓
Identify BAM-related entries
            ↓
Correlate BAM with other forensic artifacts
            ↓
Build the forensic timeline
```

---

## 17. Important BAM Registry Locations

### Common BAM Location

```text
HKLM\SYSTEM\CurrentControlSet\Services\bam\State\UserSettings
```

### Newer Windows Location

```text
HKLM\SYSTEM\CurrentControlSet\Services\bam\UserSettings
```

---

## 18. Examples of Activity That May Be Found

BAM may contain evidence related to the execution of:

- PowerShell
- `cmd.exe`
- AtomicRedTeam tools
- Scripts
- Archive utilities
- Remote administration tools
- Malware executables

These entries can be particularly useful when they occur around the same timeframe as other suspicious activity.

---

## 19. BAM and Incident Reconstruction

BAM can help investigators reconstruct user and process activity.

For example, BAM timestamps can be compared with:

- File creation timestamps
- Process creation events
- Persistence activity
- Credential access activity
- Lateral movement activity
- Cleanup activity

This correlation can help establish the sequence of events during an incident.

---

## 20. Important Limitations to Remember

BAM should never be treated as perfect evidence.

Remember:

- BAM does not capture every process execution.
- BAM data can age out.
- BAM data can be overwritten.
- Entries can disappear after system cleanup.
- Entries can disappear after extended system use.
- The presence of an executable in BAM does not automatically prove malicious activity.
- The absence of an executable from BAM does not automatically prove that it never ran.

Because of these limitations, investigators should always use BAM together with other forensic artifacts.

---

## 21. Key Takeaways

- **BAM** stands for **Background Activity Monitor**.
- BAM is a Windows component used to monitor and manage background activity.
- BAM helps Windows manage system resources and background processes.
- BAM was not originally designed for digital forensics.
- BAM can nevertheless provide valuable forensic evidence.
- BAM can provide evidence of program execution.
- BAM tracks executable files that have run on the system.
- BAM associates executable activity with timestamps.
- BAM data is stored in the **SYSTEM Registry hive**.
- BAM-related information can be found under:

```text
HKLM\SYSTEM\CurrentControlSet\Services\bam\State\UserSettings
```

- On newer versions of Windows, BAM can also be found under:

```text
HKLM\SYSTEM\CurrentControlSet\Services\bam\UserSettings
```

- BAM entries can contain executable paths.
- BAM entries can contain user-specific execution information.
- BAM entries can contain associated timestamps.
- BAM can help identify suspicious executable activity.
- BAM can be useful when other artifacts are unavailable or limited.
- **Registry Explorer** can be used to examine BAM Registry keys.
- **RegRipper** can be used to parse the SYSTEM Registry hive.
- **Notepad++** can be used to search RegRipper output for `bam`.
- BAM does not capture every process execution.
- BAM data can age out or be overwritten.
- BAM should not be treated as the only source of evidence.
- BAM provides evidence of execution, but execution does not automatically mean malicious intent.
- BAM findings should be correlated with other forensic artifacts.
- BAM timestamps can help investigators reconstruct an incident timeline.

---

## 22. Tools Used

| Tool | Purpose |
|---|---|
| **Registry Explorer** | Examine the SYSTEM Registry hive and BAM-related keys |
| **RegRipper** | Parse the SYSTEM Registry hive and extract Registry information |
| **Notepad++** | Search and review RegRipper output |

---

## 23. Related Windows Forensic Artifacts

BAM can be correlated with:

- **Prefetch**
- **UserAssist**
- **Shimcache**
- **Amcache**
- **Windows Event Logs**
- **Scheduled Tasks**
- **Registry Run Keys**
- **File System Timestamps**
- **PowerShell Logs**
- **MFT**
- **USN Journal**

The more independent artifacts that support the same finding, the stronger the overall forensic conclusion.

---

## 24. Final Understanding

BAM is a valuable Windows forensic artifact because it can provide evidence that executable files were run on a system.

Although BAM was created by Microsoft for background activity monitoring, performance management, and resource management, forensic investigators can use its data to investigate program execution.

The main information of interest is:

```text
Executable Path
      +
User Information
      +
Timestamp
      ↓
Evidence of Program Execution
```

BAM becomes much more useful when its findings are correlated with other forensic artifacts.

For example:

```text
BAM
 ↓
Executable execution
 ↓
Prefetch
 ↓
Event Logs
 ↓
Registry artifacts
 ↓
File system activity
 ↓
Timeline correlation
 ↓
Incident reconstruction
```

The important lesson is that **BAM should be treated as one part of a larger forensic investigation rather than as standalone proof**.

---

## Learning Status

**Status: Completed**

I learned:

- What BAM is
- What Background Activity Monitor means
- Why BAM exists in Windows
- Why BAM is useful in digital forensics
- Where BAM data is stored
- How BAM provides evidence of program execution
- How to examine BAM using Registry Explorer
- How to find BAM information in RegRipper output
- How to use Notepad++ to search RegRipper output
- How BAM timestamps can help build an incident timeline
- How to correlate BAM with other Windows forensic artifacts
- The limitations of BAM
- Why execution evidence does not automatically mean malicious activity
- Why multiple forensic artifacts should be used to validate findings
