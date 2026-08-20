# Prepare Forensic Triage USB

## Overview

I learned how to prepare a dedicated forensic triage USB drive
for endpoint forensic collection and live-response activities.

A properly prepared triage USB allows investigators to quickly
begin evidence collection using trusted and tested tools while
minimizing unnecessary interaction with the target system.

The USB can contain forensic tools, collected evidence,
documentation, scripts, hashes, memory captures, and other
forensic artifacts.

## Why Prepare a Triage USB in Advance?

During a real investigation, time is important. Investigators
should not be downloading and testing forensic tools for the
first time while responding to a live system.

A prepared triage USB provides:

- Trusted and tested forensic tools
- Faster evidence collection
- Consistent collection procedures
- Reduced interaction with the target system
- A predefined structure for collected evidence
- A more defensible forensic process

## 🛠️ Forensic Tools

The triage USB can contain portable forensic tools such as:

- KAPE
- FTK Imager
- DumpIt
- DIE
- Hashing utilities

### Tool Purposes

**KAPE**

Used for collecting and processing forensic artifacts.

**FTK Imager**

Used for forensic acquisition and evidence examination.

**DumpIt**

Used for capturing volatile memory/RAM.

**DIE**

Used as an encrypted disk detection tool.

**Hashing Utilities**

Used for generating hashes that can help verify the integrity
of collected files.

## 💾 Choosing the USB Drive

The USB drive becomes part of the forensic toolkit and may contain:

- Forensic tools
- Collected evidence
- Documentation
- Scripts
- Hashes
- Memory captures
- Event log exports
- Browser data
- Triage collections

Therefore, the USB should be:

- Reliable
- Fast
- Clearly labeled
- Large enough for the expected collection

## USB 3.0 / USB 3.1

USB 3.0 or USB 3.1 flash drives are suitable choices for
forensic artifact collection.

Modern forensic artifacts can become large, especially when
collecting:

- Memory captures
- Event logs
- Browser data
- Enterprise triage collections

Using a slow USB 2.0 drive can significantly increase collection
time.

Faster storage reduces acquisition time and improves the overall
efficiency of an investigation.

## External SSD

External SSDs can be especially useful for larger forensic
collections because they provide:

- Higher transfer speeds
- Better durability
- Greater storage capacity

For large collections involving memory captures, timelines,
event logs, and enterprise artifact acquisition, an external SSD
may be more suitable than a standard flash drive.

## USB Reliability

For small triage collections, a high-quality USB flash drive may
be sufficient.

However, extremely cheap or unreliable USB drives should be
avoided because they may:

- Fail during collection
- Corrupt data
- Experience write issues
- Cause incomplete collections
- Result in evidence loss
- Delay incident response

In forensic work, reliability is more important than saving a
small amount of money on storage media.

## 📁 File System Selection

The file system used on the triage USB is also important.

### exFAT

I learned that exFAT is a suitable choice for portable forensic
media because it:

- Supports very large files
- Works well with modern Windows systems
- Is suitable for portable triage media
- Avoids the file-size limitation found in FAT32

### FAT32

FAT32 has a maximum file size of 4 GB.

This can make FAT32 unsuitable for large forensic artifacts such
as:

- Memory dumps
- Disk images
- Other large evidence files

### NTFS

NTFS is also commonly used and provides additional features such
as:

- Permissions
- Compression

However, exFAT can be preferable for portable triage media because
of its simplicity and compatibility.

## 🔐 Write Blockers

In more advanced forensic environments, hardware write blockers
or dedicated forensic acquisition devices may be used when
collecting evidence directly from disks.

For live artifact collection and triage response, a properly
prepared USB 3.x flash drive or external SSD can be a practical
solution.

## 🧹 Cleaning and Formatting the USB

Before preparing the triage USB, I learned how to clean and
format the USB drive.

The material demonstrates the use of:

**Active@ KillDisk**

Active@ KillDisk is used to erase and overwrite the contents of
a storage device.

### Procedure

1. Connect the USB drive.
2. Open Active@ KillDisk.
3. Identify the correct USB disk.
4. Select the correct disk from the left-hand panel.
5. Select **Erase Disk**.
6. Select **One Pass Zeros**.
7. Enter the required key phrase.
8. Start the erase operation.

> Care must be taken to select the correct disk because an erase
> operation removes the existing contents of the selected device.

## 🏷️ Labeling the USB

After preparing the USB, the drive can be labeled:

`TRIAGE`

A clear label makes it easier to identify the forensic media
during an investigation.

## 📂 Triage USB Directory Structure

I learned to create separate directories for forensic tools and
collected evidence.

The basic structure is:

```text
TRIAGE
│
├── Tools
│
└── Evidence
