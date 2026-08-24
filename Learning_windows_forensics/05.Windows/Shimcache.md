# Windows Artifacts: Shimcache

## 1. What is Shimcache?

**Shimcache** is also known as:

- **Application Compatibility Cache**
- **AppCompatCache**

It is an important Windows forensic artifact.

Shimcache is part of the **Windows Application Compatibility Framework**. Windows uses this framework to help older applications work correctly on newer versions of Windows.

Many applications were originally designed for older versions of Windows and may not work correctly on newer operating systems. To solve this problem, Windows can apply **compatibility fixes**, commonly called **shims**.

These shims can make changes such as:

- Emulating older Windows behavior
- Adjusting permissions
- Redirecting resources
- Applying other compatibility modifications

Windows maintains the **Application Compatibility Cache** to support this process.

From a normal Windows perspective, Shimcache helps maintain application compatibility.

From a **digital forensics perspective**, Shimcache is useful because it can provide evidence that:

- An executable existed on the system
- An executable may have been executed
- A suspicious application may have been present
- An attacker tool may have been staged or launched

> Shimcache was created for Windows application compatibility, not specifically for forensic investigations.

---

## 2. Where is Shimcache Stored?

Shimcache is stored in the **SYSTEM Registry hive**.

The Registry location is:

```text
HKLM\SYSTEM\CurrentControlSet\Control\SessionManager\AppCompatCache
```

Because the artifact is stored in the **SYSTEM hive**, it is **system-wide rather than user-specific**.

This means Shimcache can contain information related to applications associated with multiple users on the machine.

---

## 3. What Information Can Shimcache Provide?

Shimcache can provide information that helps investigators understand application activity on a Windows system.

It can help answer questions such as:

- Did this executable exist on the system?
- Was this suspicious file likely executed?
- Was malware present on the machine?
- Were attacker tools staged or launched?
- Did a deleted executable previously exist?
- Did the user interact with portable applications or scripts?

Shimcache can therefore be useful during investigations involving malware, suspicious applications, attacker tools, and deleted files.

---

## 4. Important Limitation: Shimcache Is Not an Execution Log

One of the most important things to understand about Shimcache is:

> **The presence of a Shimcache entry does not always prove that a file was executed.**

A file can appear in Shimcache for reasons other than execution.

For example, a file may appear because:

- Windows accessed the file
- Windows Explorer browsed to the file or folder
- The operating system interacted with the file
- Antivirus software scanned the file

Therefore:

```text
Shimcache entry
       ≠
Guaranteed execution
```

Modern Windows versions and advanced parsing tools can provide additional indicators that help investigators differentiate between files that were simply accessed and files that were likely executed.

---

## 5. Shimcache and System Shutdown

Another important characteristic of Shimcache is that the cache is typically **written to disk during system shutdown**.

This means recently executed or accessed applications may not immediately appear in the stored Shimcache data if the system has not yet been:

- Shut down
- Rebooted

Therefore:

```text
No Shimcache entry
       ≠
Program was never executed
```

The absence of an entry should not automatically be interpreted as proof that the program was never run.

Investigators need to consider the system's shutdown and reboot history when interpreting Shimcache evidence.

---

## 6. Why Shimcache Is Useful in Malware Investigations

Shimcache can be especially useful during malware investigations.

One reason is that Shimcache entries may remain even after a malicious executable has been deleted from the filesystem.

For example:

```text
Malware exists on disk
        ↓
Malware is executed / accessed
        ↓
Shimcache records information
        ↓
Attacker deletes malware
        ↓
File disappears from filesystem
        ↓
Shimcache entry may still remain
```

This means investigators may be able to find evidence that an executable previously existed on the system even though the actual file is no longer present.

This can also be useful when investigating **anti-forensic activity**.

---

## 7. Tools Used to Analyze Shimcache

The main tools used in this lesson are:

- **Registry Explorer**
- **AppCompatCacheParser**
- **Timeline Explorer**

### Registry Explorer

Registry Explorer can be used to manually navigate to the Shimcache Registry key inside the SYSTEM hive.

The relevant key is:

```text
HKLM\SYSTEM\CurrentControlSet\Control\SessionManager\AppCompatCache
```

This allows investigators to examine the raw Shimcache entries.

However, manually analyzing raw Shimcache data can be difficult because the structure can vary between Windows versions.

---

### AppCompatCacheParser

**AppCompatCacheParser** is a tool from **Eric Zimmerman** that can parse Shimcache data.

It simplifies the process of extracting and analyzing Shimcache information from the SYSTEM Registry hive.

---

### Timeline Explorer

After AppCompatCacheParser generates the CSV output, the results can be opened in **Timeline Explorer**.

Timeline Explorer makes it easier to:

- Sort the data
- Filter the data
- Search for specific entries
- Analyze Shimcache results
- Correlate entries with a forensic timeline

---

## 8. Parsing Shimcache with AppCompatCacheParser

The lesson uses the following command:

```text
AppCompatCacheParser.exe -f C:\Cases\Case_01\Working\Clean_Hives\SYSTEM_clean --csv C:\Cases\Case_01\Exports --csvf Shimcache.csv
```

### Breaking Down the Command

#### AppCompatCacheParser.exe

This is the executable used to parse the Shimcache data.

#### `-f`

The `-f` option specifies the **SYSTEM Registry hive** that will be analyzed.

In this example:

```text
C:\Cases\Case_01\Working\Clean_Hives\SYSTEM_clean
```

#### `--csv`

The `--csv` option tells the tool to export the parsed results in **CSV format**.

The output is saved to:

```text
C:\Cases\Case_01\Exports
```

#### `--csvf`

The `--csvf` option specifies the name of the output CSV file.

In this example:

```text
Shimcache.csv
```

So the overall process is:

```text
SYSTEM Registry Hive
        ↓
AppCompatCacheParser
        ↓
Shimcache.csv
        ↓
Timeline Explorer
        ↓
Analysis
```

---

## 9. Viewing the Output

After running AppCompatCacheParser, the resulting CSV file can be found in the specified export directory.

The lesson's example shows an `Exports` folder containing files such as:

```text
Prefetch.csv
Prefetch_Timeline.csv
Shimcache.csv
```

The Shimcache output is:

```text
Shimcache.csv
```

This CSV can then be opened and analyzed using Timeline Explorer.

---

## 10. Analyzing Shimcache with Timeline Explorer

Timeline Explorer is useful because raw CSV data can be difficult to analyze manually.

It allows investigators to:

- Sort entries
- Filter entries
- Search for filenames
- Examine suspicious activity
- Place findings into a forensic timeline

### Searching for a Suspicious Executable

If an investigator suspects that a malicious executable or attack script was used during an incident, they can search for the executable's filename in Timeline Explorer.

For example:

```text
Suspicious executable
        ↓
Search filename in Timeline Explorer
        ↓
Find Shimcache entry
        ↓
Review associated information
        ↓
Compare with other forensic artifacts
```

This helps investigators quickly locate relevant Shimcache entries.

---

## 11. Accessed vs. Executed Files

Modern Shimcache parsing tools can help differentiate between:

### Files that were simply accessed

A file may appear because:

- Windows Explorer browsed to it
- Antivirus software scanned it
- Windows interacted with it

### Files that were likely executed

Some parsing tools can identify indicators that suggest actual execution behavior.

This distinction is extremely important because:

```text
File appears in Shimcache
```

does not automatically mean:

```text
File was executed
```

---

## 12. Example: Windows Explorer

Consider a file that appears in Shimcache.

It may not have been executed.

For example, Windows Explorer may have simply browsed to the folder containing the file.

Therefore:

```text
Explorer browsed to file
        ↓
File appears in Shimcache
        ↓
Does NOT automatically prove execution
```

---

## 13. Example: Antivirus Scanning

Antivirus software may also access files while scanning the system.

Therefore, a file may appear in Shimcache even if it was never manually executed.

For example:

```text
Antivirus scans file
        ↓
Windows accesses file
        ↓
Shimcache entry appears
        ↓
Execution is NOT automatically proven
```

This is why Shimcache findings should always be interpreted carefully.

---

## 14. Correlating Shimcache With Other Artifacts

Shimcache should **never be analyzed in isolation**.

It becomes much more valuable when correlated with other Windows forensic artifacts.

Important artifacts include:

- **Prefetch**
- **BAM**
- **UserAssist**
- **Amcache**
- **Event Logs**
- **MFT**
- **USN Journal**
- **PowerShell Logs**
- **Scheduled Tasks**
- **Registry Run Keys**

---

## 15. Example of Artifact Correlation

Suppose Shimcache contains a suspicious executable path.

By itself, this does not necessarily prove that the executable was executed.

However, suppose:

```text
Shimcache
    ↓
Suspicious.exe found
    +
Prefetch
    ↓
Suspicious.exe execution evidence
```

Now investigators have much stronger evidence that:

```text
Suspicious.exe
was actually executed
```

This is the main reason why forensic investigators correlate multiple artifacts.

---

## 16. Shimcache and Anti-Forensics

Shimcache can also be useful when investigating **anti-forensic activity**.

Attackers may attempt to remove evidence by deleting malicious files from the filesystem.

For example:

```text
Attacker places malware
        ↓
Malware is executed
        ↓
Evidence is created
        ↓
Attacker deletes malware
        ↓
Malware file disappears
        ↓
Shimcache entry may remain
```

Therefore, Shimcache may provide evidence that an executable previously existed or may have been launched even though the executable is no longer present on disk.

---

## 17. Forensic Investigation Questions

Shimcache can help investigators investigate questions such as:

### Did an executable exist?

Shimcache may contain information about the executable.

### Was a suspicious file likely executed?

Shimcache can provide supporting evidence, especially when combined with other execution artifacts.

### Was malware present?

Shimcache entries can reveal evidence of suspicious or malicious executables.

### Were attacker tools staged or launched?

Shimcache can help identify attacker tools that interacted with the system.

### Did a deleted executable previously exist?

Shimcache entries may remain after the executable has been deleted.

### Did the user interact with portable applications or scripts?

Shimcache may contain entries related to these applications.

---

## 18. Important Limitations

When analyzing Shimcache, remember the following:

- Shimcache is **not a traditional execution log**.
- A Shimcache entry does not automatically prove execution.
- Files can appear because Windows accessed them.
- Windows Explorer can cause files to appear in the cache.
- Antivirus software can access files and cause them to appear.
- Shimcache is typically written to disk during system shutdown.
- Recently executed applications may not appear before shutdown or reboot.
- The absence of a Shimcache entry does not prove that a program was never executed.
- The structure of Shimcache varies between Windows versions.
- Raw Shimcache data can be difficult to analyze manually.

---

## 19. Basic Shimcache Investigation Workflow

A simple workflow for analyzing Shimcache is:

```text
Acquire SYSTEM Registry Hive
            ↓
Open SYSTEM Hive
            ↓
Locate AppCompatCache
            ↓
HKLM\SYSTEM\CurrentControlSet\
Control\SessionManager\AppCompatCache
            ↓
Analyze using Registry Explorer
            ↓
Parse using AppCompatCacheParser
            ↓
Export to CSV
            ↓
Open Shimcache.csv in Timeline Explorer
            ↓
Search / Filter suspicious filenames
            ↓
Determine whether files were accessed
or likely executed
            ↓
Correlate with other artifacts
            ↓
Build forensic timeline
```

---

## 20. Important Registry Location

```text
HKLM\SYSTEM\CurrentControlSet\Control\SessionManager\AppCompatCache
```

This is the main Registry location to remember for Shimcache.

---

## 21. Tools Summary

| Tool | Purpose |
|---|---|
| **Registry Explorer** | Manually examine the AppCompatCache Registry key |
| **AppCompatCacheParser** | Parse Shimcache data from the SYSTEM hive |
| **Timeline Explorer** | Search, filter, sort, and analyze parsed Shimcache CSV data |

---

## 22. Important Concepts to Remember

### Shimcache

Application Compatibility Cache used by Windows to support application compatibility.

### AppCompatCache

Another name for Shimcache.

### Shims

Compatibility fixes applied by Windows to help older applications run correctly.

### SYSTEM Hive

The Registry hive containing the Shimcache information.

### AppCompatCache Registry Key

```text
HKLM\SYSTEM\CurrentControlSet\Control\SessionManager\AppCompatCache
```

### AppCompatCacheParser

Tool used to parse Shimcache information.

### Timeline Explorer

Tool used to analyze the parsed CSV output.

---

## 23. Key Takeaways

- **Shimcache** is also called the **Application Compatibility Cache** or **AppCompatCache**.
- It is part of the Windows compatibility framework.
- Windows uses compatibility fixes called **shims** to help applications work correctly.
- Shimcache is stored in the **SYSTEM Registry hive**.
- The main Registry path is:

```text
HKLM\SYSTEM\CurrentControlSet\Control\SessionManager\AppCompatCache
```

- Shimcache is system-wide rather than user-specific.
- It can provide evidence that an executable existed on the system.
- It can provide evidence that an executable may have been executed.
- It is **not a traditional execution log**.
- A Shimcache entry does not automatically prove execution.
- Windows Explorer browsing can cause files to appear.
- Antivirus scanning can cause files to appear.
- Shimcache is typically written to disk during system shutdown.
- Recently executed programs may not appear until the system shuts down or reboots.
- The absence of an entry does not prove that a program was never executed.
- Shimcache can remain after a malicious executable has been deleted.
- This makes it useful during anti-forensic investigations.
- **Registry Explorer** can be used to examine Shimcache manually.
- **AppCompatCacheParser** can parse the SYSTEM hive.
- The output can be exported as `Shimcache.csv`.
- **Timeline Explorer** can be used to search, filter, and analyze the results.
- Shimcache should always be correlated with other forensic artifacts.
- Correlation with artifacts such as Prefetch and BAM can provide stronger evidence of execution.

---

## Learning Status

**Status: Completed**

I learned about **Shimcache / Application Compatibility Cache**, why Windows uses it, where it is stored, and how it can be useful during Windows forensic investigations.

I also learned how to:

- Locate Shimcache in the SYSTEM Registry hive
- Examine Shimcache using Registry Explorer
- Parse Shimcache using AppCompatCacheParser
- Export the results to CSV
- Analyze the results using Timeline Explorer
- Search for suspicious executable names
- Understand the difference between file access and likely execution
- Correlate Shimcache with other Windows forensic artifacts
- Use Shimcache as supporting evidence during malware and anti-forensic investigations

The most important point I learned is:

> **A Shimcache entry alone does not prove that a file was executed. It should be correlated with other forensic artifacts to build stronger evidence.**
