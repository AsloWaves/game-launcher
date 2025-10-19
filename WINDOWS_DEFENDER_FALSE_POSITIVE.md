# Windows Defender False Positive Information

## Overview

**GameLauncher may trigger Windows Defender false positive detections**, most commonly:
- **Trojan:Script/Wacatac.B!ml** (Machine Learning heuristic detection)
- **Win32/Suspicious** (Generic suspicious behavior detection)

**This is NOT malware.** This is a legitimate open-source game launcher that uses binary diff patching for efficient game updates.

---

## Why This Happens

Windows Defender's machine learning algorithms flag GameLauncher because it uses legitimate update mechanisms that **superficially resemble malware behavior patterns**:

### 1. **Binary Diff Patching System**
- Downloads version information from remote servers
- Applies binary patches to local game files
- Updates files without full reinstallation

**Why it's flagged:** Download → Modify files → Similar to malware droppers

### 2. **Self-Patching Mechanism**
- Launcher can update itself without user intervention
- Temporarily spawns a separate updater process (SelfPatcher.exe)
- Replaces the running launcher executable

**Why it's flagged:** Self-modification → Common rootkit/trojan behavior

### 3. **Process Management**
- Launches game executables with custom arguments
- Monitors and manages running game processes
- Performs single-instance checks

**Why it's flagged:** Process spawning + file modification → Suspicious chain

### 4. **Single-File Executable Build**
- Published as self-contained executable with embedded dependencies
- Contains compressed .NET runtime and libraries

**Why it's flagged:** Compression + self-extraction → Obfuscation technique

---

## Technical Details

### Code Patterns Triggering Detection

**File:** `SelfPatcherCore/SelfPatcher.cs`
- File attribute manipulation to bypass read-only flags
- Persistent retry loops for file operations (handles locked files)
- Process spawning followed by self-termination
- Instruction parsing from remote-downloaded files

**File:** `GameLauncher/Models/Patcher/PatcherFunctions.cs`
- Network downloads from configurable HOST_URL
- Dynamic executable launching with runtime arguments
- Version comparison and automatic file replacement

These are **legitimate game launcher operations**, but they match patterns used by malware for code injection and file modification.

---

## Verification This Is Legitimate

### 1. **Open Source Code**
- **Repository:** https://github.com/AsloWaves/game-launcher
- **Based On:** https://github.com/yasirkula/UnitySimpleFileBrowser
- **Documentation:** https://gamelauncher.gitbook.io/documentation/

All source code is publicly available for inspection.

### 2. **VirusTotal Scan**
Upload the executable to [VirusTotal](https://www.virustotal.com/) for multi-engine scanning:
- Most reputable AV engines (50+/70) should report clean
- False positives are primarily from heuristic/ML detections

### 3. **Digital Signature** (Planned)
Future releases will be code-signed with an Extended Validation (EV) certificate to eliminate false positives.

---

## Installation Instructions

### Method 1: Allow Through SmartScreen (Recommended)

**When Windows Defender blocks the download:**

1. Download `GameLauncher_vX.X.X.zip` from the [Releases page](https://github.com/AsloWaves/game-launcher/releases)
2. Extract the ZIP file to a folder (e.g., `C:\Games\GameLauncher`)
3. When you see **"Windows protected your PC"**:
   - Click **"More info"**
   - Click **"Run anyway"**
4. The launcher will start normally

**Screenshot Guide:**
```
┌─────────────────────────────────────────┐
│  Windows protected your PC              │
│                                         │
│  Windows Defender SmartScreen prevented │
│  an unrecognized app from starting.    │
│                                         │
│  ┌─────────────────┐                   │
│  │   More info     │ ← Click here first│
│  └─────────────────┘                   │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│  Run anyway                             │ ← Then click here
│                                         │
│  App: GameLauncher.exe                  │
│  Publisher: Unknown publisher           │
└─────────────────────────────────────────┘
```

---

### Method 2: Add Windows Defender Exclusion

**For permanent exclusion:**

1. Press **Win + I** to open Windows Settings
2. Go to **Update & Security → Windows Security**
3. Click **Virus & threat protection**
4. Under "Virus & threat protection settings," click **Manage settings**
5. Scroll down to **Exclusions** and click **Add or remove exclusions**
6. Click **Add an exclusion → Folder**
7. Select the folder where you extracted GameLauncher (e.g., `C:\Games\GameLauncher`)
8. Click **Select Folder**

**Screenshot Guide:**
```
Settings → Update & Security → Windows Security
    ↓
Virus & threat protection → Manage settings
    ↓
Exclusions → Add or remove exclusions
    ↓
Add an exclusion → Folder → Select GameLauncher folder
```

---

### Method 3: Temporarily Disable Real-Time Protection (Not Recommended)

**Only use if Methods 1-2 don't work:**

1. Open **Windows Security**
2. Go to **Virus & threat protection**
3. Under "Virus & threat protection settings," click **Manage settings**
4. Turn off **Real-time protection** temporarily
5. Extract and run GameLauncher
6. **Re-enable Real-time protection immediately after**

⚠️ **Warning:** This leaves your system vulnerable. Only disable temporarily.

---

## Microsoft False Positive Submission

We have submitted GameLauncher to Microsoft for false positive review:
- **Submission Date:** [To be filled]
- **Case Number:** [To be filled]
- **Status:** Pending review

Microsoft typically responds within 24-48 hours. Once approved, future Windows Defender updates will recognize GameLauncher as legitimate software.

**You can also submit it yourself:**
1. Go to: https://www.microsoft.com/en-us/wdsi/filesubmission
2. Select "Software developer" or "End user"
3. Upload `GameLauncher.exe` and mark as false positive
4. Reference this repository in the description

---

## Future Plans to Eliminate False Positives

### Short-Term (Current Release)
- ✅ Replaced aggressive process termination methods
- ✅ Disabled single-file compression
- ✅ Enabled debugging symbols
- ✅ Reduced security permission requests
- ✅ Submitted to Microsoft for whitelist review

### Medium-Term (Next 1-2 Releases)
- 🔄 Code signing with Standard Code Signing Certificate ($180-400/year)
- 🔄 Enhanced manifest with publisher information
- 🔄 Telemetry to track false positive rates

### Long-Term (6+ Months)
- 📅 Upgrade to Extended Validation (EV) Code Signing Certificate ($300-600/year)
- 📅 Microsoft Store distribution (automatic trust)
- 📅 Optional web-based update mechanism (reduces self-patching triggers)

---

## FAQ

### Q: Is this really safe to run?
**A:** Yes. The entire source code is available at https://github.com/AsloWaves/game-launcher for inspection. The detection is a false positive caused by legitimate update mechanisms.

### Q: Why don't you just sign the code?
**A:** Code signing certificates cost $180-600/year. As an open-source project, we're working on obtaining one. In the meantime, the source code transparency provides verification.

### Q: Will this always trigger Windows Defender?
**A:** No. Detection rates vary based on Windows Defender definition updates. After Microsoft's whitelist review, detection should decrease significantly.

### Q: Can I use this without disabling antivirus?
**A:** Yes. Use Method 1 (click "Run anyway") or Method 2 (add folder exclusion). You don't need to disable your antivirus.

### Q: What data does this launcher collect?
**A:** The launcher only connects to the HOST_URL specified in `LauncherConfig.cs` to check for game/launcher updates and fetch news. No telemetry or personal data is collected. You can verify this in the source code.

### Q: Is my game data safe?
**A:** Yes. The launcher only modifies files in its own directory and the game installation directory. It does not access system files or other applications.

---

## Contact & Support

**Issue Tracker:** https://github.com/AsloWaves/game-launcher/issues
**Documentation:** https://gamelauncher.gitbook.io/documentation/
**Original Project:** https://github.com/yasirkula/UnitySimpleFileBrowser

If you have concerns about the false positive detection, please open an issue on GitHub with:
- Your Windows version
- Windows Defender version (Settings → Update & Security → Windows Security → About)
- Exact detection name (e.g., "Trojan:Script/Wacatac.B!ml")
- Screenshot of the warning (if possible)

---

## For Security Researchers

If you're a security researcher investigating this detection, please:

1. **Review the source code** at https://github.com/AsloWaves/game-launcher
2. **Check the commit history** to verify development authenticity
3. **Run static analysis** on the compiled binary
4. **Compare hash signatures** against published releases
5. **Contact us** via GitHub Issues with any concerns

We welcome security audits and responsible disclosure of any actual vulnerabilities.

---

**Last Updated:** 2025-01-XX
**Applies to Versions:** v1.0.4 and later
