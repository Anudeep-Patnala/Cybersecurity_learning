# 🔬 Volatility 3 – Windows Memory Analysis

## 📌 Overview

Volatility 3 is an open-source memory forensics framework used to analyze Windows memory dumps.

It uses plugins to extract forensic information such as:

- Running processes
- Process relationships
- Loaded DLLs
- Network connections
- Memory regions
- Injected code
- Windows services
- Registry artifacts
- MFT artifacts

## ⚙️ Basic Usage

The basic Volatility command structure is:

`vol.exe -f memory.dmp <plugin>`

Example:

`vol.exe -f memory.dmp windows.info`

## 🔎 Important Plugins

| Plugin | Purpose |
|---|---|
| `windows.info` | Shows OS and kernel information |
| `windows.pslist` | Lists processes |
| `windows.pstree` | Shows parent-child process relationships |
| `windows.psscan` | Scans for processes |
| `windows.dlllist` | Lists loaded DLLs |
| `windows.cmdline` | Shows process command-line arguments |
| `windows.malfind` | Finds potentially injected code |
| `windows.netscan` | Finds network connections |
| `windows.handles` | Lists process handles |
| `windows.dumpfiles` | Dumps cached files |
| `windows.vadinfo` | Shows process memory regions |
| `windows.svcscan` | Scans Windows services |
| `windows.mftscan` | Scans for MFT file objects |
| `windows.modules` | Lists loaded kernel modules |
| `windows.registry.hivelist` | Lists registry hives |

## 🧩 Process Analysis

### `windows.pslist`

The `pslist` plugin lists processes present in the memory image.

**Command:**

`vol.exe -f memory.dmp windows.pslist`

### `windows.pstree`

The `pstree` plugin displays processes in a parent-child hierarchy. It helps identify which process started another process.

**Command:**

`vol.exe -f memory.dmp windows.pstree`

**Remember:**

- `pslist` → What processes exist?
- `pstree` → Who launched whom?

## 📚 DLL Analysis

The `dlllist` plugin lists DLLs and modules loaded by a particular process.

**Command:**

`vol.exe -f memory.dmp windows.dlllist --pid <PID>`

It can help identify unusual or suspicious modules loaded into a process.

## 🦠 MalFind

The `malfind` plugin searches process memory for regions that may contain hidden or injected code.

**Command:**

`vol.exe -f memory.dmp windows.malfind`

### Suspicious Indicators

Malfind may identify memory containing:

- `MEM_PRIVATE`
- `PAGE_EXECUTE_READWRITE`
- `MZ` headers in private memory
- Executable memory without normal file backing

These characteristics may indicate techniques such as:

- DLL Injection
- Reflective DLL Injection
- Process Hollowing

### ⚠️ Important

Malfind can produce false positives because legitimate applications such as Chrome, Edge, .NET, Java, and PowerShell may also create executable private memory.

Therefore, malfind results should always be correlated with other plugins.

## 🌐 Network Analysis

The `netscan` plugin identifies network activity present in the memory capture.

It can provide information about:

- Local IP addresses
- Remote IP addresses
- Local and remote ports
- Connection state
- Protocol
- Process associated with the connection

**Command:**

`vol.exe -f memory.dmp windows.netscan`

Network results can help identify:

- Suspicious external connections
- Unexpected listening ports
- Possible command-and-control activity

Results can be correlated with:

`malfind` + `pstree` + `cmdline` + `dlllist`

## 🔄 DFIR Workflow

**Memory Image → `windows.info` → `windows.pslist` → `windows.pstree` → `windows.cmdline` / `windows.dlllist` → `windows.malfind` → `windows.netscan` → Correlate Evidence**

## 📝 Quick Revision

- `pslist` → Processes
- `pstree` → Process hierarchy
- `psscan` → Process scanning
- `dlllist` → Loaded DLLs
- `cmdline` → Process commands
- `malfind` → Suspicious/injected memory
- `netscan` → Network activity
- `vadinfo` → Memory regions
- `handles` → Open handles
- `dumpfiles` → Cached files
- `svcscan` → Windows services
- `mftscan` → MFT artifacts

## 📌 Key Takeaways

- Volatility 3 analyzes Windows memory dumps using plugins.
- `pslist` and `pstree` are useful for process analysis.
- `dlllist` helps investigate loaded modules.
- `malfind` helps identify potentially injected code.
- `netscan` helps investigate network activity.
- Individual plugin results should not be treated as proof of malware.
- Correlating multiple artifacts provides stronger forensic evidence.
