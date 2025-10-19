# Microsoft False Positive Submission Template

Use this template to submit GameLauncher to Microsoft for false positive review.

---

## Submission URL

**Primary:** https://www.microsoft.com/en-us/wdsi/filesubmission

**Alternative (Microsoft Defender Security Intelligence):**
https://www.microsoft.com/en-us/wdsi/submission

---

## Submission Form Fields

### 1. Submission Type
**Select:** `Software developer` or `End user`

---

### 2. Email Address
**Enter your email** for status updates and communication

---

### 3. File Upload
**Upload:** `GameLauncher.exe` from your release build

**Note:** Maximum file size is typically 256 MB. GameLauncher should be well under this limit.

---

### 4. Product Name
```
Game Launcher
```

---

### 5. Product Version
```
[Enter your version, e.g., 1.0.4]
```

---

### 6. Detection Name
```
Trojan:Script/Wacatac.B!ml
```

**Note:** Replace with the actual detection name shown in Windows Defender. Common variations:
- `Trojan:Script/Wacatac.B!ml`
- `Trojan:Win32/Wacatac.B!ml`
- `Win32/Suspicious`
- `Trojan:Win32/Sabsik.FL.B!ml`

---

### 7. Company Name (Optional)
```
[Your company name or "Open Source Project"]
```

---

### 8. Detailed Description

**Copy and paste this template (customize as needed):**

```
Subject: False Positive Detection - Game Launcher v[VERSION]

Dear Microsoft Security Intelligence Team,

I am submitting GameLauncher.exe for false positive review. This legitimate
open-source game launcher is being incorrectly flagged by Windows Defender
as malicious software.

DETECTION DETAILS:
- Product Name: Game Launcher
- Version: [Your version]
- Detection Name: Trojan:Script/Wacatac.B!ml (or other detection)
- File: GameLauncher.exe
- SHA-256: [Optional: Include file hash]

PRODUCT DESCRIPTION:
GameLauncher is an open-source Windows desktop application designed to manage
game updates and display news content. It is based on the publicly available
Unity Game Launcher framework (https://gamelauncher.gitbook.io/documentation/).

The application uses binary diff patching to efficiently update game files,
similar to how Steam, Epic Games Launcher, and other legitimate game platforms
operate.

WHY THIS IS A FALSE POSITIVE:
The detection is triggered by legitimate software update mechanisms that
superficially resemble malware behavior patterns:

1. Binary Diff Patching System:
   - Downloads version information from remote servers
   - Applies binary patches to local game files for efficient updates
   - Similar to Steam's patching system

2. Self-Update Mechanism:
   - Launcher can update itself without full reinstallation
   - Uses a separate SelfPatcher.exe helper process
   - Common pattern used by Chrome, Firefox, and other auto-updating software

3. Process Management:
   - Launches game executables with user-configured arguments
   - Standard launcher functionality (same as Steam, Origin, etc.)

VERIFICATION OF LEGITIMACY:
- Source Code: https://github.com/AsloWaves/game-launcher
- Based On: https://github.com/yasirkula/UnitySimpleFileBrowser
- Documentation: https://gamelauncher.gitbook.io/documentation/
- License: Open source (all code publicly viewable)

RECENT SECURITY IMPROVEMENTS:
To reduce false positive triggers, we have implemented:
- Replaced aggressive process termination with graceful exit (Environment.Exit)
- Disabled single-file compression (EnableCompressionInSingleFile=false)
- Enabled debugging symbols (DebuggerSupport=true)
- Reduced security permission requests in application manifest
- Enabled Win32 manifest with proper UAC declarations

TECHNICAL DETAILS:
- Framework: .NET 8.0 with WPF
- Architecture: Windows x64 desktop application
- Distribution: Self-contained single-file executable
- Purpose: Game update management and news display
- Network Activity: Only connects to user-configured game server URLs

USER IMPACT:
Many users are unable to download or run GameLauncher due to this false
positive, which significantly impacts our ability to distribute this
legitimate software. The detection is particularly problematic because:
- Users assume their systems are infected
- SmartScreen blocks downloads entirely
- Enterprise environments automatically quarantine the file

REQUEST:
Please review GameLauncher.exe and whitelist it in Windows Defender's
detection database. The source code is fully available for audit at the
GitHub repository linked above.

If you need any additional information, code samples, or technical details,
please contact me at this email address.

Thank you for your time and assistance in resolving this false positive.

Best regards,
[Your name]
[Your role, e.g., "Developer" or "Project Maintainer"]

---

REFERENCES:
- GitHub Repository: https://github.com/AsloWaves/game-launcher
- Documentation: https://gamelauncher.gitbook.io/documentation/
- False Positive Information: [Link to your WINDOWS_DEFENDER_FALSE_POSITIVE.md]
```

---

### 9. Additional Information (Optional)

**Include if requested:**

```
TECHNICAL IMPLEMENTATION DETAILS:

Self-Patcher Mechanism:
The SelfPatcher.exe component is a helper utility that enables the launcher
to update itself. The workflow is:

1. Main launcher detects available update
2. Downloads new launcher version to temporary location
3. Spawns SelfPatcher.exe with instructions file
4. Main launcher exits gracefully (Environment.Exit)
5. SelfPatcher.exe replaces old files with new ones
6. SelfPatcher.exe relaunches updated launcher
7. SelfPatcher.exe exits

This is identical to the update mechanism used by Google Chrome, Mozilla
Firefox, and Microsoft Edge.

Network Communication:
The launcher only communicates with user-configured game servers specified
in LauncherConfig.cs (HOST_URL variable). Default behavior:
- GET requests to: {HOST_URL}/Launcher/VersionInfo.info
- GET requests to: {HOST_URL}/News/{Environment}/{Language}_News.txt
- No telemetry or analytics collected
- No connections to third-party servers
- All URLs configurable by end users

File Operations:
The launcher operates exclusively within its installation directory and
the user's game directory. It does not:
- Modify system files
- Access Windows registry (except for standard .NET Framework operations)
- Install drivers or kernel components
- Access files outside its designated directories
- Perform privilege escalation

Code Signing Status:
Currently unsigned. We are in the process of obtaining a code signing
certificate (estimated completion: [date]). Once signed, future releases
will include an Extended Validation certificate from [DigiCert/Sectigo/SSL.com].
```

---

## After Submission

### Expected Timeline
- **Initial Response:** 24-48 hours
- **Analysis Complete:** 2-5 business days
- **Definition Update:** 1-2 weeks after approval

### Tracking Your Submission
- Check the email you provided for updates
- Microsoft will send:
  - Submission confirmation
  - Analysis status updates
  - Final determination

### If Approved
- Windows Defender definitions will be updated globally
- Your software will no longer trigger false positives
- Update may take 7-14 days to propagate to all users

### If Denied
Common reasons for denial:
1. **Insufficient information** → Resubmit with more technical details
2. **Suspicious patterns remain** → May require code signing certificate
3. **Heuristic override not possible** → Consider architectural changes

**If denied, you can:**
- Request reconsideration with additional evidence
- Submit with code-signed executable (much higher approval rate)
- Contact Microsoft Security Intelligence via Twitter: @wdsi

---

## Tips for Successful Submission

### ✅ Do:
- Be professional and detailed in your description
- Provide source code links for verification
- Explain the technical necessity of flagged behaviors
- Include recent security improvements
- Mention similar legitimate software (Steam, Chrome, etc.)
- Offer to provide additional information if needed

### ❌ Don't:
- Be aggressive or confrontational in tone
- Submit without explaining why it's legitimate
- Omit source code verification links
- Submit without company/project name
- Use informal language or excessive technical jargon

---

## Alternative: User-Submitted Reports

**Users can also submit false positive reports:**

1. Go to: https://www.microsoft.com/en-us/wdsi/filesubmission
2. Select **"End user"** as submission type
3. Upload the GameLauncher.exe they downloaded
4. Select **"I think this file is safe"**
5. Provide brief description: "This is a legitimate game launcher from [your game name]. Source code: [GitHub link]"

**Multiple user submissions increase visibility** and accelerate Microsoft's review process.

---

## Additional Resources

**Microsoft Documentation:**
- Submit files: https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/submit-files-deep-analysis
- Understand submissions: https://www.microsoft.com/en-us/wdsi/help/understanding-submissions

**Community Support:**
- Microsoft Defender Security Intelligence Twitter: @wdsi
- Microsoft Security Blog: https://www.microsoft.com/en-us/security/blog/

---

**Template Version:** 1.0
**Last Updated:** 2025-01-XX
**For GameLauncher Version:** 1.0.4+
