# Windows Artifacts: NTFS Time Stamps

## Overview

I studied NTFS timestamps and how they can be used in Windows
forensics to reconstruct file and system activity.

## NTFS

NTFS (New Technology File System) stores metadata and timestamps
associated with files and directories.

## MACB Timestamps

MACB represents four important timestamps:

- **M — Modified:** When file contents were modified
- **A — Access:** When the file was accessed
- **C — Changed:** When file metadata changed
- **B — Birth:** When the file was created

These timestamps help investigators establish the sequence of
events on a system.

## NTFS Attributes

Two important NTFS attributes are:

### `$STANDARD_INFORMATION`

Contains file metadata and MACB timestamps.

### `$FILE_NAME`

Contains the filename, size, and another set of MACB timestamps.

Comparing `$STANDARD_INFORMATION` and `$FILE_NAME` timestamps can
help identify inconsistencies or possible timestamp manipulation.

## File Operations

I studied how different operations affect NTFS timestamps:

- File creation
- File access
- File modification
- File renaming
- File copying
- File movement
- File deletion

Different operations can modify different timestamps, and the
behavior can vary between Windows versions.

## Anti-Forensics

I also studied **timestomping**, an anti-forensic technique used
to manipulate file timestamps.

Comparing `$STANDARD_INFORMATION` and `$FILE_NAME` timestamps can
help identify suspicious inconsistencies.

## Timeline Analysis

NTFS timestamps can be used to build a forensic timeline:

```text
Creation → Access → Modification → Rename/Move → Deletion
