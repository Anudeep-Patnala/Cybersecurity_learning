# Build a Velociraptor Off-Line Collector

## Introduction

The **Velociraptor Offline Collector** is a standalone version of Velociraptor used to collect forensic artifacts from a system **without requiring continuous communication with a Velociraptor server**.

This is especially useful when network connectivity is limited or unavailable, such as:

- Air-gapped systems
- Systems that cannot reach the Velociraptor server
- Restricted or disconnected environments
- Sensitive systems where investigators want to minimize changes
- Incident response investigations
- Digital forensic investigations

Instead of installing a permanent Velociraptor client that communicates with a central server, the Offline Collector packages:

- The Velociraptor executable
- Predefined artifact collection instructions

into a **single standalone executable**.

This allows the collector to operate independently without needing a live connection to a server.

---

## What Can the Offline Collector Collect?

During the build process, the investigator chooses which forensic artifacts should be collected.

Examples include:

- Windows Event Logs
- Registry hives
- Prefetch files
- UserAssist data
- Browser artifacts
- Scheduled Tasks
- Running processes
- Network connections
- Memory captures

The selected collection instructions are embedded directly into the standalone collector executable.

---

## How the Offline Collector Works

The general process is:

```text
Build Collector
      ↓
Select Forensic Artifacts
      ↓
Embed Collection Instructions
      ↓
Generate Standalone Executable
      ↓
Transfer Collector to Target
      ↓
Run Collector
      ↓
Collect Configured Artifacts
      ↓
Create Compressed Archive
      ↓
Transfer Evidence for Analysis
```

The collector does not need a live connection back to the Velociraptor server because the collection logic is already included inside the executable.

---

## Transferring the Collector

After creating the collector, it can be transferred to the target system using an appropriate method.

Possible methods include:

- USB drive
- Email to a trusted administrator
- Network share
- Remote management solution

The collector is typically executed with **administrative privileges**.

Once executed, it automatically collects the configured forensic artifacts.

This makes the collection process largely automated and helps investigators obtain a consistent set of evidence across multiple systems without manually collecting individual files and logs.

---

## Collection Results

After collection is complete, the Offline Collector stores the results in a **compressed archive**, usually a ZIP file.

The archive can be stored on:

- The local system
- Removable media

The collected archive can then be transferred to the investigator's workstation for analysis.

The collected data can be:

- Imported back into Velociraptor
- Examined using other forensic tools

The evidence can be used to:

- Identify indicators of compromise
- Reconstruct attacker activity
- Build forensic timelines
- Support incident response investigations

---

## Advantages of the Offline Collector

The main advantages are:

- Fast forensic collection
- Repeatable collection process
- Standardized evidence collection
- No continuous network connectivity required
- No permanently installed agent required
- Helps reduce the chance of missing important artifacts
- Uses the same collection methodology across systems

The Offline Collector can be useful to both:

- Incident responders
- Digital forensic investigators

It can be used for collecting evidence from:

- Windows
- Linux
- macOS

---

# Installing Velociraptor

To install a local version of Velociraptor on the Windows forensics VM, go to the Velociraptor GitHub repository and open the releases.

Velociraptor GitHub:

https://github.com/velocidex/velociraptor

Download:

```text
Windows-amd64.msi
```

---

## Install Velociraptor

Once the MSI file is downloaded:

1. Double-click the `.msi` file.
2. Install Velociraptor.
3. Open Command Prompt or PowerShell.
4. Navigate to:

```text
C:\Program Files\Velociraptor
```

5. Run:

```powershell
.\Velociraptor.exe gui
```

A terminal window should open and Velociraptor should open in your browser.

---

# Building the Offline Collector

## Step 1 — Open the Server Tab

To build the Offline Collector:

1. Open the Velociraptor web interface.
2. Select the **Server** tab.
3. Select **Build Offline Collector**.

---

## Step 2 — Search for Kape

In the search bar:

```text
Kape
```

Search for:

```text
Windows.KapeFiles.Targets
```

Select it.

---

## Step 3 — Configure Parameters

Select:

```text
Configure Parameters
```

Then select:

```text
Configure
```

---

## Step 4 — Select Kape Triage

Select:

```text
Kape Triage
```

Then select:

```text
Configure Collection
```

---

## Step 5 — Specify Resources

At the resource configuration stage:

- Do not make any changes.
- Keep the default settings.

Then select:

```text
Specify Resources
```

---

## Step 6 — Review

Keep the default settings again.

Then select:

```text
Review
```

---

## Step 7 — Review the Artifacts

The Review page lists all the artifacts that will be collected.

Check the list of artifacts.

If everything looks correct, select:

```text
Launch
```

---

## Step 8 — Create the Collector

After launching:

1. Find the row:

```text
Server.Utils.CreateCollector
```

2. Highlight the `Server.Utils.CreateCollector` row in the top pane.
3. Select the generated collector in the bottom pane.
4. Download the collector.

---

## Step 9 — Locate the Collector

The generated collector file is saved in the:

```text
Downloads
```

folder.

---

## Step 10 — Run the Collector

The downloaded collector can now be transferred to the target system.

Run the collector on the target system.

It will collect the configured:

```text
Kape Triage
```

artifacts.

---

# Complete Workflow

```text
Install Velociraptor
        ↓
Download Windows-amd64.msi
        ↓
Install Velociraptor
        ↓
Go to C:\Program Files\Velociraptor
        ↓
Run .\Velociraptor.exe gui
        ↓
Open Velociraptor in Browser
        ↓
Server
        ↓
Build Offline Collector
        ↓
Search "Kape"
        ↓
Windows.KapeFiles.Targets
        ↓
Configure Parameters
        ↓
Configure
        ↓
Kape Triage
        ↓
Configure Collection
        ↓
Keep Defaults
        ↓
Specify Resources
        ↓
Keep Defaults
        ↓
Review
        ↓
Review Artifacts
        ↓
Launch
        ↓
Server.Utils.CreateCollector
        ↓
Select Collector
        ↓
Download Collector
        ↓
Collector Saved to Downloads
        ↓
Transfer to Target System
        ↓
Run Collector
        ↓
Collect Kape Triage Artifacts
```

---

# What I Learned

- The **Velociraptor Offline Collector** is a standalone forensic collection tool.
- It can collect evidence without maintaining continuous communication with a Velociraptor server.
- It is useful for **air-gapped, restricted, remote, and sensitive systems**.
- The collector contains the Velociraptor executable and predefined collection instructions.
- Investigators can choose the forensic artifacts they want to collect.
- Artifacts can include Event Logs, Registry hives, Prefetch, UserAssist, browser artifacts, scheduled tasks, processes, network connections, and memory captures.
- The collector can be transferred using USB, email, network shares, or remote management solutions.
- It is typically run with administrative privileges.
- Collection is largely automated.
- The collected evidence is stored in a compressed archive, usually a ZIP file.
- The archive can be transferred to a forensic workstation.
- The collected data can be imported into Velociraptor or analyzed using other forensic tools.
- The Offline Collector provides a fast, repeatable, and standardized way of collecting forensic evidence.
- Velociraptor can be installed on the Windows forensics VM using the `Windows-amd64.msi` installer.
- The local GUI can be started using:

```powershell
.\Velociraptor.exe gui
```

- The Offline Collector can be created from the **Server** tab.
- The required artifact configuration in this lab is:

```text
Windows.KapeFiles.Targets
        ↓
Kape Triage
```

- After reviewing the artifacts, the collector is created using:

```text
Server.Utils.CreateCollector
```

- The generated collector is downloaded and saved in the `Downloads` folder.
- The collector can then be run on the target system to collect the configured Kape Triage artifacts.

---

# Key Takeaway

The Velociraptor Offline Collector allows investigators to create a **standalone forensic collection executable** that can be transferred to a target system and run without requiring continuous communication with a Velociraptor server.

For this lab, the main configuration and collection process is:

```text
Windows.KapeFiles.Targets
        ↓
Kape Triage
        ↓
Server.Utils.CreateCollector
        ↓
Download Collector
        ↓
Run on Target System
        ↓
Collect Kape Triage Artifacts
```
