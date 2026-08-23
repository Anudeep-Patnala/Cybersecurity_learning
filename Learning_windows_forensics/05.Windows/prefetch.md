# Windows Artifacts: Prefetch

![Prefetch](prefetch.png)

## 1. What is Prefetch?

Prefetch is one of the most useful Windows forensic artifacts for
identifying **program execution**.

Prefetch was originally created by Microsoft to **improve system
performance**, not for forensic investigations.

Its purpose is to help Windows load applications faster by remembering
key pieces of data that a program commonly needs during startup.

The basic idea is:

```text
Application runs
      ↓
Windows records commonly used data
      ↓
Prefetch information is created
      ↓
Windows can use this information during future launches
```

From a forensic perspective, Prefetch is valuable because it can provide
evidence that a program executed on a Windows system.

---

## 2. Why is Prefetch Important in Forensics?

Prefetch can help investigators answer questions such as:

- Did this tool run?
- When did it run?
- How many times did it run?
- What files were associated with the execution?
- Was a suspicious executable launched during the incident?

This can be particularly useful when investigating:

- Malware
- Attacker tools
- Scripts
- Persistence mechanisms
- Unauthorized software

---

## 3. Location of Prefetch Files

Prefetch files are normally stored in:

```text
C:\Windows\Prefetch
```

Prefetch files use the:

```text
.PF
```

extension.

---

## 4. Prefetch Availability

Most Windows workstation operating systems have Prefetch enabled by
default.

Windows Server systems typically do not.

This is important during forensic investigations because if Prefetch files
are missing from a server, it does **not automatically mean that the
evidence was deleted**.

It may simply mean that Prefetch was not enabled on that system.

---

## 5. What Information Can Prefetch Contain?

A Prefetch file is generally created based on a combination of:

- Application name
- Application path
- Command-line context

Different executions of the same program may therefore produce different
Prefetch entries depending on how and from where the program was launched.

Prefetch files can contain useful information such as:

- Executable name
- Run count
- Last execution times
- Referenced files
- Referenced directories
- Volume information

---

# 6. Checking Whether Prefetch is Enabled

Before relying on Prefetch evidence, we should confirm whether Prefetch
was enabled on the system.

The relevant Registry setting is commonly found under:

```text
HKLM\SYSTEM\CurrentControlSet\Control\SessionManager\MemoryManagement\PrefetchParameters
```

The value we are interested in is:

```text
EnablePrefetcher
```

This allows investigators to determine whether Prefetch was enabled on
the system being investigated.

---

# 7. Collecting Prefetch Files

If Prefetch is enabled, we can collect the contents of:

```text
C:\Windows\Prefetch
```

The collected files can then be parsed on a forensic workstation.

For this analysis, we use **PECmd**, one of Eric Zimmerman's forensic
tools.

---

# 8. PECmd

PECmd is used to parse Windows Prefetch files.

It can parse known versions of Windows Prefetch files, including
compressed Prefetch files introduced in newer versions of Windows.

This is important because older Prefetch parsers may not properly process
compressed Prefetch files from Windows 8 and newer systems.

---

## Advantages of PECmd

PECmd can:

- Parse Prefetch files
- Process an entire Prefetch directory
- Handle compressed Prefetch files
- Generate CSV output
- Produce timeline-focused information

Processing an entire directory is particularly useful during a forensic
investigation because older standalone tools may only parse one Prefetch
file at a time.

---

# 9. Parsing Prefetch with PECmd

If the Prefetch folder was collected using KAPE, PECmd can be used to
parse the collected Prefetch files.

Example:

```text
PECmd.exe -d C:\Cases\Case_01\Working\Kape01\C\Windows\Prefetch --csv C:\Cases\Case_01\Exports --csvf Prefetch.csv
```

---

## Breaking Down the Command

### PECmd.exe

This is the Prefetch parsing tool being executed.

### `-d`

The `-d` option specifies the directory containing the Prefetch files.

In this example:

```text
C:\Cases\Case_01\Working\Kape01\C\Windows\Prefetch
```

### `--csv`

The `--csv` option specifies where the parsed output should be saved.

In this example:

```text
C:\Cases\Case_01\Exports
```

### `--csvf`

The `--csvf` option specifies the name of the CSV output file.

In this example:

```text
Prefetch.csv
```

---

# 10. PECmd Output

When PECmd finishes processing the Prefetch files, it usually produces
two useful CSV outputs.

One provides detailed information about individual Prefetch files.

The other provides timestamp-focused information that can be useful for
timeline analysis.

The timeline output is especially useful when the approximate time of an
incident is known.

Investigators can then look for programs that executed around that
specific timeframe.

---

# 11. Timeline Explorer

After parsing the Prefetch files, the CSV output can be opened in
**Timeline Explorer**.

Timeline Explorer makes it easier to:

- Sort data
- Filter data
- Search data
- Analyze timestamps
- Review activity around a specific timeframe

It is more convenient for this type of analysis than using Excel.

---

# 12. Example Timeline Analysis

Suppose we know the approximate start time of an attack or emulation.

We can filter the Prefetch timeline around that timeframe.

We may find several unusual executables launched within a short period,
such as:

- PowerShell
- Command Prompt
- Script interpreters
- Archive utilities
- Attacker tools

For example, seeing several suspicious executions within approximately
30 seconds can help investigators build a clearer picture of what
happened.

```text
Suspicious execution
        ↓
PowerShell
        ↓
Script interpreter
        ↓
Archive utility
        ↓
Attacker tool
```

This type of execution sequence can become an important part of the
forensic timeline.

---

# 13. Correlating Prefetch With Other Artifacts

Prefetch should not be treated as the only source of evidence.

It is important to correlate Prefetch information with other forensic
artifacts.

Useful artifacts include:

- Windows Event Logs
- Amcache
- Shimcache
- UserAssist
- Registry Run Keys
- Scheduled Tasks
- PowerShell logs
- File-system timestamps
- `$MFT`

For example, if Prefetch shows that a suspicious executable was
executed, investigators can look for:

- Process creation events
- File creation activity
- Persistence entries
- Command history
- Other related activity

This helps build a more complete picture of the incident.

---

# 14. Prefetch and Attacker Cleanup

Prefetch can also provide clues about attacker cleanup activity.

For example, investigators may notice that:

- Prefetch files are missing
- Prefetch has been disabled
- Prefetch files were selectively deleted

This may be significant during an investigation.

However, we should not immediately assume that an attacker deleted the
evidence.

Other possibilities include:

- Prefetch was never enabled
- The system is a Windows Server system
- System cleanup tools removed the files
- Normal system activity affected the files

Therefore, the findings should always be validated using other forensic
artifacts.

---

# 15. Forensic Workflow

A basic Prefetch investigation can be summarized as:

```text
Collect Prefetch files
        ↓
Confirm Prefetch was enabled
        ↓
Parse using PECmd
        ↓
Generate CSV output
        ↓
Open output in Timeline Explorer
        ↓
Filter around the incident timeframe
        ↓
Identify suspicious executions
        ↓
Correlate with other artifacts
        ↓
Build the forensic timeline
```

---

# 16. Key Takeaways

- Learned what Windows Prefetch is.
- Learned why Windows creates Prefetch files.
- Learned that Prefetch was designed for performance improvement.
- Learned why Prefetch is valuable for digital forensics.
- Learned the location of Prefetch files.
- Learned that Prefetch files use the `.PF` extension.
- Learned that Windows Server systems typically do not have Prefetch
  enabled by default.
- Learned how to check the `EnablePrefetcher` Registry value.
- Learned how to collect Prefetch files.
- Learned how to use PECmd to parse Prefetch files.
- Learned how to process an entire Prefetch directory.
- Learned about compressed Prefetch files in newer Windows versions.
- Learned how to generate CSV output using PECmd.
- Learned how to analyze the output using Timeline Explorer.
- Learned how Prefetch can help identify program execution.
- Learned how to investigate execution activity around an incident
  timeframe.
- Learned the importance of correlating Prefetch with other forensic
  artifacts.
- Learned how missing or deleted Prefetch files may provide useful
  investigative context.

---

# Tools Used

- PECmd
- Timeline Explorer
- KAPE
- Windows Registry

---

# Learning Status

**Status: Completed**

This lesson helped me understand how Prefetch can be used as a Windows
forensic artifact to identify program execution, analyze execution
timestamps, investigate suspicious activity, and build a larger forensic
timeline by correlating Prefetch with other artifacts.
