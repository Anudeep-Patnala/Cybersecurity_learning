# Windows Artifacts: $MFT and $USN

## Introduction

In this lesson, I studied two very important **NTFS file system artifacts**:

- `$MFT` — Master File Table
- `$USN Journal` — Update Sequence Number Journal

These artifacts are extremely useful in Windows forensics because they help investigators understand:

- What files and folders existed on a Windows system
- Where files were located
- When files were created or modified
- When files were renamed
- Whether files were deleted
- What changes happened to files over time
- Whether files may have been overwritten or their records reused

The `$MFT` mainly tells us about the **metadata and current record of files**, while the `$USN Journal` helps us understand **changes and activity that happened to files over time**. :contentReference[oaicite:1]{index=1}

---

# 1. $MFT — Master File Table

## What is the $MFT?

The **Master File Table ($MFT)** is the central database of the **NTFS file system**.

Every file and directory on an NTFS volume has an entry in the MFT.

Each MFT entry can contain information such as:

- File name
- File path
- File size
- Timestamps
- Permissions
- File attributes
- Other file metadata

In simple terms:

> If NTFS needs to keep track of a file or directory, there is an MFT record associated with it.

This makes the MFT one of the most important sources of evidence during Windows forensic investigations. :contentReference[oaicite:2]{index=2}

---

# 2. What Can the MFT Tell an Investigator?

The MFT can help answer questions such as:

- Did this file exist?
- Where was the file stored?
- When was it created?
- When was it modified?
- Was it renamed?
- Does the file still exist?
- Was the file deleted?

One important forensic advantage of the MFT is that when a file is deleted, its MFT record may remain for some period of time.

The record can eventually be reused, but until that happens, investigators may still be able to recover information such as:

- File names
- File paths
- Timestamps
- Other metadata

This means that a file that is no longer visible in Windows Explorer may still leave evidence in the MFT. :contentReference[oaicite:3]{index=3}

---

# 3. Parsing the MFT

For this analysis, I used **MFTECmd**, one of **Eric Zimmerman's forensic tools**.

MFTECmd can take the raw `$MFT` file and convert its information into a **CSV file**.

The CSV can then be opened using:

- Excel
- Timeline Explorer

**Timeline Explorer** is another Eric Zimmerman tool designed to make large forensic CSV files easier to:

- Search
- Filter
- Sort
- Review
- Analyze

---

# 4. MFT Parsing Command

The MFT can be parsed using:

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$MFT --csv C:\Cases\Case_01\Exports --csvf MFT.csv
```

## Breaking Down the Command

### `MFTECmd.exe`

The forensic tool used to parse the MFT.

### `-f`

Specifies the location of the raw `$MFT` file.

```text
C:\Cases\Case_01\Working\Kape01\C\$MFT
```

### `--csv`

Specifies the directory where the parsed CSV output should be saved.

```text
C:\Cases\Case_01\Exports
```

### `--csvf`

Specifies the name of the output CSV file.

```text
MFT.csv
```

So the process is:

```text
Raw $MFT
   ↓
MFTECmd
   ↓
MFT.csv
   ↓
Timeline Explorer
   ↓
Forensic analysis
```

:contentReference[oaicite:4]{index=4}

---

# 5. Searching MFT Data

After creating the CSV, the results can be opened in **Timeline Explorer**.

For example, if an attack emulation created a file or script containing a specific name such as:

```text
ART-attack
```

I can search for that term in Timeline Explorer.

This can help identify related MFT records.

```text
MFT.csv
   ↓
Timeline Explorer
   ↓
Search "ART-attack"
   ↓
Find related MFT entries
   ↓
Investigate the file
```

---

# 6. Investigating a Specific MFT Record

Once an interesting MFT entry is found, we can pivot back to **MFTECmd** and examine that specific record in more detail.

For example:

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$MFT --de217946
```

The `--de` option allows us to display details for a specific MFT entry number.

This can be useful when we want to:

- Document a specific file record
- Examine timestamps
- Review file attributes
- Investigate the file record
- Determine whether the file still exists

:contentReference[oaicite:5]{index=5}

---

# 7. $USN Journal

The second artifact covered in this lesson is the **$USN Journal**.

USN stands for:

**Update Sequence Number**

The USN Journal records **file system change activity**.

While the MFT mainly provides information about files and directories, the USN Journal helps show **what happened to those files**.

---

# 8. What Does the USN Journal Record?

The USN Journal can record activities such as:

- File creation
- File modification
- File closing
- File renaming
- Metadata changes
- File deletion

This makes it extremely valuable during forensic investigations.

The important difference is:

```text
$MFT
↓
File metadata / record information

$USN Journal
↓
File system change activity
```

:contentReference[oaicite:6]{index=6}

---

# 9. Why the USN Journal Is Important

The USN Journal is especially useful when investigating:

- Deleted files
- File modification
- File renaming
- Anti-forensic activity
- Malware activity
- Attacker staging
- File cleanup

For example, the USN Journal may show that:

- A file existed
- A file was deleted
- Prefetch files were removed
- A user overwrote a file before deleting it
- Files were staged in a temporary directory
- Files were archived
- Files were exfiltrated and then removed
- Malware changed file attributes

It can also help identify files that were marked as:

- Hidden
- System files

These attributes can be used to reduce visibility in Windows Explorer. :contentReference[oaicite:7]{index=7}

---

# 10. Location of the USN Journal

The USN Journal is located under:

```text
$Extend\$UsnJrnl
```

The specific data we are interested in is stored in the:

```text
$J
```

stream.

So the important location is:

```text
$Extend\$UsnJrnl:$J
```

---

# 11. Parsing the USN Journal

MFTECmd can also be used to parse the USN Journal.

The command used in the lesson is:

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$Extend\$J -m C:\Cases\Case_01\Working\Kape01\C\$MFT --csv C:\Cases\Case_01\Exports --csvf $J.csv
```

---

# 12. Breaking Down the USN Command

### `-f`

The `-f` option points to the `$J` file.

```text
C:\Cases\Case_01\Working\Kape01\C\$Extend\$J
```

### `-m`

The `-m` option points to the `$MFT`.

```text
C:\Cases\Case_01\Working\Kape01\C\$MFT
```

Providing the MFT allows MFTECmd to enrich the USN Journal output with additional:

- File metadata
- File paths
- Other information where possible

### `--csv`

Specifies where the CSV output should be saved.

```text
C:\Cases\Case_01\Exports
```

### `--csvf`

Specifies the output filename:

```text
$J.csv
```

The overall process is:

```text
$J
 +
$MFT
 ↓
MFTECmd
 ↓
$J.csv
 ↓
Timeline Explorer
 ↓
USN Journal analysis
```

:contentReference[oaicite:8]{index=8}

---

# 13. Searching the USN Journal

After parsing the USN Journal, the output can be opened in **Timeline Explorer**.

For example, we can search for a filename such as:

```text
deleteme_
```

This can help us identify file system activity related to that file.

The USN Journal may show events indicating that the file was:

```text
Created
   ↓
Closed
   ↓
Deleted
```

Even if the file is no longer present on the system, the USN Journal may still preserve evidence that:

- The file existed
- The file was created
- The file was closed
- The file was deleted

:contentReference[oaicite:9]{index=9}

---

# 14. Pivoting From USN Journal to the MFT

The USN Journal contains entry information that can be used to pivot back into the MFT.

For example:

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$MFT --de 144196
```

This allows us to examine the corresponding MFT entry.

---

# 15. MFT Record Reuse

When investigating a deleted file, we may initially find that its MFT record still appears to exist.

However, closer examination may show that the filename associated with the MFT record is now different.

This can indicate that:

- The original MFT record was reused
- The original record was overwritten
- The original deleted file may no longer be recoverable

This is an important forensic concept.

```text
Original file
      ↓
File deleted
      ↓
MFT record remains
      ↓
Record eventually reused
      ↓
New file uses the record
      ↓
Original information may no longer be recoverable
```

:contentReference[oaicite:10]{index=10}

---

# 16. $MFT vs $USN Journal

The two artifacts provide different types of information.

| Artifact | Main Purpose |
|---|---|
| **$MFT** | Provides file and directory metadata |
| **$USN Journal** | Records file system change activity |

### $MFT helps answer:

- What file was recorded?
- What was its name?
- Where was it located?
- What metadata was associated with it?
- Does its record still exist?

### $USN Journal helps answer:

- Was the file created?
- Was it modified?
- Was it renamed?
- Was it deleted?
- What changes happened to the file?

---

# 17. Why They Are Stronger Together

The **MFT and USN Journal are strongest when analyzed together**.

The MFT gives us information about:

```text
File metadata
File records
File names
File paths
Timestamps
Attributes
```

The USN Journal gives us information about:

```text
File creation
File modification
File closing
File renaming
File deletion
Other file system changes
```

Combining both allows investigators to build a much clearer picture of what happened to files over time.

---

# 18. Example Investigation

Suppose an attacker creates a malicious file.

The investigation could look like:

```text
Attacker creates malicious file
          ↓
$MFT records the file
          ↓
USN Journal records creation activity
          ↓
Attacker modifies the file
          ↓
USN Journal records modification
          ↓
Attacker renames the file
          ↓
USN Journal records rename activity
          ↓
Attacker deletes the file
          ↓
USN Journal records deletion
          ↓
MFT record may still remain
          ↓
Investigator correlates both artifacts
```

This can help reconstruct the attacker's file activity even when the original file is no longer available.

---

# 19. Anti-Forensics Investigation

The `$USN Journal` can be particularly useful for identifying **anti-forensic behavior**.

For example:

```text
Malware created
      ↓
Malware executed
      ↓
Files staged
      ↓
Files archived
      ↓
Files removed
```

The files may no longer exist, but the USN Journal may preserve evidence of some of the file system operations.

Examples include:

- File deletion
- Prefetch deletion
- File overwriting
- Temporary file staging
- File renaming
- Attribute changes

This can help investigators understand attempts to remove or hide evidence.

---

# 20. Deleted File Investigation

Deleted files are one of the most useful scenarios for combining `$MFT` and `$USN Journal`.

For example:

```text
File created
      ↓
File closed
      ↓
File deleted
```

The file may no longer be visible in Windows Explorer.

However:

```text
$USN Journal
      ↓
Shows file system activity

$MFT
      ↓
May still contain the file record
```

This provides investigators with additional evidence about the deleted file.

---

# 21. Forensic Timeline

Both `$MFT` and `$USN Journal` are valuable for building forensic timelines.

A timeline may show:

```text
File Created
      ↓
File Modified
      ↓
File Renamed
      ↓
File Archived
      ↓
File Moved
      ↓
File Deleted
```

By correlating these events with other artifacts, investigators can better understand what happened during an incident.

---

# 22. Tools Used

| Tool | Purpose |
|---|---|
| **MFTECmd** | Parse `$MFT` and `$USN Journal` data |
| **Timeline Explorer** | Search, filter, and analyze CSV forensic data |
| **Excel** | Open and review CSV output |

---

# 23. Important Commands

## Parse $MFT

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$MFT --csv C:\Cases\Case_01\Exports --csvf MFT.csv
```

## Examine a Specific MFT Entry

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$MFT --de217946
```

## Parse $USN Journal

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$Extend\$J -m C:\Cases\Case_01\Working\Kape01\C\$MFT --csv C:\Cases\Case_01\Exports --csvf $J.csv
```

## Examine a Specific MFT Entry From USN Correlation

```text
MFTECmd.exe -f C:\Cases\Case_01\Working\Kape01\C\$MFT --de 144196
```

---

# 24. Important Locations

### Master File Table

```text
C:\$MFT
```

### USN Journal

```text
$Extend\$UsnJrnl
```

### USN Journal Data Stream

```text
$J
```

---

# 25. Important Concepts

## $MFT

The central database of the NTFS file system.

## MFT Record

An entry representing a file or directory on an NTFS volume.

## $USN Journal

A journal that records file system change activity.

## $J Stream

The part of the USN Journal containing the relevant journal data.

## MFT Record Reuse

When an old MFT record is reused for another file, information about the original file may no longer be recoverable.

## Timeline Correlation

Combining MFT, USN Journal, and other forensic artifacts to reconstruct what happened over time.

---

# 26. Key Takeaways

- `$MFT` stands for **Master File Table**.
- `$USN` stands for **Update Sequence Number**.
- Both are important **NTFS forensic artifacts**.
- Every file and directory on an NTFS volume has an MFT entry.
- The MFT stores important file metadata.
- MFT data can help identify files that existed on the system.
- Deleted files may still leave MFT records behind.
- MFT records can eventually be reused or overwritten.
- The USN Journal records file system change activity.
- USN Journal activity can include:
  - File creation
  - File modification
  - File closing
  - File renaming
  - Metadata changes
  - File deletion
- The USN Journal is especially useful for investigating deleted files.
- The USN Journal can help identify anti-forensic activity.
- It can show evidence of files being staged, archived, and removed.
- It can also show changes to file attributes such as hidden or system.
- **MFTECmd** can parse both `$MFT` and `$USN Journal`.
- **Timeline Explorer** can be used to analyze the resulting CSV files.
- The `-m` option allows MFTECmd to use the MFT when parsing the USN Journal.
- This allows the USN output to be enriched with file metadata and paths where possible.
- The MFT and USN Journal are strongest when analyzed together.

---

# 27. Final Understanding

The main difference I learned is:

```text
$MFT
↓
Tells me about files and directories
and their metadata.

$USN Journal
↓
Tells me about changes and activity
that happened to those files.
```

Together:

```text
$MFT
   +
$USN Journal
   ↓
File System Timeline
   ↓
Deleted File Investigation
   ↓
Attack Staging Analysis
   ↓
Anti-Forensics Detection
   ↓
Incident Reconstruction
```

The most important lesson is that **the MFT and USN Journal should be analyzed together**.

The MFT helps investigators understand **file metadata and records**, while the USN Journal helps reconstruct **file system operations over time**.

Together, they are extremely valuable for:

- Building forensic timelines
- Identifying deleted files
- Investigating attacker staging
- Detecting anti-forensic behavior
- Understanding file system activity
- Reconstructing what happened on a Windows system

---

## Learning Status

**Status: Completed**

I learned:

- What the `$MFT` is
- What the `$USN Journal` is
- How NTFS uses the MFT
- What information MFT records contain
- How deleted files can leave MFT records behind
- How MFT records can eventually be reused
- How to parse `$MFT` using MFTECmd
- How to analyze MFT output using Timeline Explorer
- How to investigate a specific MFT record
- What the USN Journal records
- Where the `$J` stream is located
- How to parse `$J` using MFTECmd
- Why the MFT is supplied using the `-m` option
- How to search USN Journal results in Timeline Explorer
- How to investigate deleted files
- How to pivot from USN entries back to MFT records
- How MFT and USN data can be correlated
- How these artifacts help investigate attacker staging and anti-forensic activity
- How `$MFT` and `$USN Journal` can be used together to reconstruct file system activity
