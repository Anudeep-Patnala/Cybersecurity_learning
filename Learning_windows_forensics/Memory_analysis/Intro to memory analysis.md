# Windows Memory Analysis

## 📌 Overview

Windows memory is one of the richest sources of forensic evidence because it captures the live state of a system at a specific moment in time.

Memory artifacts can be obtained from:

- Physical RAM
- `hiberfil.sys`
- `pagefile.sys`
- `swapfile.sys`

These sources may contain evidence related to:

- Running processes
- User activity
- Network connections
- Credentials
- Malware
- Injected code
- Decrypted content
- Command-and-control communications
- Encryption keys

Unlike traditional disk artifacts, memory can reveal evidence that may never have been written to the file system.

Memory analysis can help investigators determine:

- What processes were running
- Which users were logged in
- What network connections were active
- Whether malicious code was executing
- How an attacker interacted with the system
- What persistence mechanisms may have been present

Combining memory analysis with traditional disk-based forensic artifacts provides a more complete picture of a security incident.

---

## 🧠 Sources of Memory

### RAM

RAM is volatile memory that requires power to maintain stored information.

It temporarily stores information actively being used or processed by the computer, including:

- Operating system components
- Applications
- User data
- Active processes

When the computer is powered off, the information stored in RAM is generally lost.

---

### hiberfil.sys

`hiberfil.sys` is a Windows system file used during hibernation.

When Windows enters hibernation, it saves the current state of system memory to this file. This includes information related to:

- Open applications
- Running processes
- System state
- Files being accessed

The file is normally located on the Windows system drive and can be useful for forensic investigations after a system has been shut down.

---

### pagefile.sys

`pagefile.sys` is the Windows paging file, also known as a virtual-memory or swap file.

When physical RAM becomes limited, Windows can move some memory contents to `pagefile.sys`.

This process allows Windows to continue operating when physical memory is insufficient.

Forensic investigators may find remnants of previously active data within the page file.

---

### swapfile.sys

`swapfile.sys` is another Windows memory-management file.

It is associated with modern Windows versions, including Windows 10 and Windows 11, and works alongside Windows memory-management and compressed-memory mechanisms.

It can contain memory pages that Windows has moved out of physical RAM.

---

## 🔎 Why Memory Analysis Matters

Memory analysis is particularly valuable during incident response because RAM can contain evidence that may not be available from disk.

It can help investigators identify:

- Active malware
- Suspicious processes
- Network activity
- User activity
- Memory-resident code
- Sensitive information
- Evidence of an ongoing attack

Because RAM is volatile, memory acquisition should be performed as soon as possible during an investigation.

---

## 📌 Key Takeaways

- RAM is a volatile source of forensic evidence.
- `hiberfil.sys` can preserve system memory during hibernation.
- `pagefile.sys` contains memory pages moved from physical RAM.
- `swapfile.sys` supports Windows memory-management functions.
- Memory can reveal evidence that may not exist on disk.
- Memory should be captured as early as possible during an incident.
- Memory analysis combined with disk forensics provides a more complete investigation.
