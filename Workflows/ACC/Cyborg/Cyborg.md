---
WorkflowId: Cyborg
WorkflowName: Cyborg Setup (New Machine Provisioning)
Type: Workflow_Utility
Domain: Cyborg
Category: DevOps — Machine Setup
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-25
EntryPoint: true
Purpose: >
  Naya Cyborg (computer/machine) ka complete setup karne ka master workflow.
  Cyborg ID ek unique machine identifier hai (e.g., C1094) — koi 2 machines ki ID kabhi same nahi hogi.
  Yeh workflow 6 phases me machine ko fully provision karta hai:
  Phase 1 — System Config: Windows dark theme enable + solid black wallpaper
  Phase 2 — Dev Tools: Python & PHP latest version install (system default location)
  Phase 3 — Manifest: manifest.json download from remote, CyborgID inject, save to /Aliens/
  Phase 4 — Git Repos: /Aliens/ me required repos clone + Cyborg ID branches create
  Phase 5 — Alien Folder Duplication: .Alien repo me Alien folder ko CyborgID naam se duplicate
  Phase 6 — VS Code Config: chat.agent.maxRequests = 50000 set
Invocation:
  Signature:
    - Workflow("Cyborg", "C1094", "Default setup kr do")
    - Workflow("Cyborg", "C1094", "Default setup kr do, saath me kuch or repo ko bhi clone kr k set kr do repo: Course, Course_ACLE, Wiki, Role, PyOS, PySDK")
  TargetsMeaning: >
    Cyborg ID — unique machine/computer identifier (e.g., C1094, C2001, C0007).
    Yeh ID branch names ke liye use hogi. Har machine ka ID unique hota hai.
    Sirf 1 Cyborg ID allowed per run (no comma-separation).
  DescriptionMeaning: >
    Setup instructions. "Default setup" = core repos only.
    Extended setup: Description me additional repo names specify karo
    (e.g., "repo: Course, Course_ACLE, Wiki, Role, PyOS, PySDK").
    Required — empty/missing => fail fast.
Safety:
  NoDelete: true
  NoOverwrite: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
  BranchSafety: >
    New branch creation only — existing branches overwrite nahi hongi.
    Force push / reset / rebase permanently banned.
Requires:
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "Workflows/ACC/Git/Git_Clone.md"
  - "Workflows/ACC/Git/Git_Branch.md"
NonNegotiables:
  - NoDelete: true
  - ExistingFolderSkip: true
  - ExistingBranchSkip: true
  - NeverForce: true
---

# Cyborg.md

# [00] Philosophy (NON-NEGOTIABLE)
```
SAFETY > SPEED > CONVENIENCE
```
- Naya machine setup reliable aur repeatable hona chahiye.
- Existing data/repos/branches kabhi overwrite nahi honge.
- Agar koi step fail ho, to cleanly report karo aur aage badho (per-repo independent).
- Cyborg ID unique hai — duplicate branch creation se data conflict nahi hoga.

---

# [01] Strict Scope Lock
Yeh workflow sirf yeh karta hai:
- Windows system settings configure karna (dark theme + wallpaper)
- Python & PHP latest version install karna (system default paths)
- Manifest.json download + CyborgID inject karna
- Required repos ko /Aliens/ me clone karna
- Specified repos me Cyborg ID ke naam se nayi branch create karna (main se)
- .Alien repo me Alien folder ko CyborgID naam se duplicate karna
- VS Code settings configure karna
- Setup verification aur status report

Forbidden:
- Existing repos/folders overwrite/delete karna
- Existing branches overwrite karna
- Git history manipulation (rebase/reset/force)
- Koi bhi unrelated file content changes
- Scope se bahar koi kaam

---

# [02] Inputs

## Targets (Cyborg ID)
- Ek unique machine/computer identifier (e.g., `C1094`, `C2001`, `C0007`)
- Format: alphanumeric string, typically `C` + numeric digits
- Comma-separated NAHI — sirf 1 Cyborg ID per run
- Empty/invalid => `CYBORG_ERR_INVALID_ID`

### Cyborg ID Validation Rules
- Must NOT be empty
- Must NOT contain spaces, slashes, or special characters (except hyphen `-` aur underscore `_`)
- Must be a valid Git branch name
- Must NOT be `main`, `master`, `develop`, `dev`, `staging`, `production` (reserved branch names)

## Description
- Required (non-empty)
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`
- Controls setup scope:
  - **Default setup**: sirf core repos clone + branch
  - **Extended setup**: Description me additional repos specify karo with `repo:` keyword

### Description Parsing
```
Default:
  "Default setup kr do"
  => Core repos only (Section 03 Default Repo List)

Extended:
  "Default setup kr do, saath me kuch or repo ko bhi clone kr k set kr do repo: Course, Course_ACLE, Wiki, Role, PyOS, PySDK"
  => Core repos + Additional repos (Course, Course_ACLE, Wiki, Role, PyOS, PySDK)
```

**Additional Repo Extraction:**
- Description me `repo:` keyword ke baad comma-separated repo names
- Trim spaces around names
- Each name = folder name = repo name on remote
- Additional repos hamesha `main` branch par rahenge (no Cyborg branch)

---

# [03] Repo Configuration (SSOT)

## 3.1 Default Repo List (Core — Always Cloned)
Yeh repos har Cyborg setup me clone honge:

### Group A: Branch = Cyborg ID (new branch from main)
In repos me `main` branch se ek nayi branch create hogi Cyborg ID ke naam se.
Cyborg ID branch active/checked-out rahegi clone ke baad.

| Repo Name | Remote Repo Name | Branch Strategy |
|-----------|-----------------|-----------------|
| .Alien | .Alien | Cyborg ID branch from main |
| WebBrand | WebBrand | Cyborg ID branch from main |
| WebOS | WebOS | Cyborg ID branch from main |
| WebSDK | WebSDK | Cyborg ID branch from main |
| WebApp | WebApp | Cyborg ID branch from main |
| Docs | Docs | Cyborg ID branch from main |

### Group B: Branch = main (stay on main, no new branch)
In repos me sirf clone hoga, branch create nahi hogi.

| Repo Name | Remote Repo Name | Branch Strategy |
|-----------|-----------------|-----------------|
| Cyborg | Cyborg | main (no new branch) |
| Copilot | Copilot | main (no new branch) |
| .github | .github | main (no new branch) |
| Project | Project | main (no new branch) |

## 3.2 Additional Repos (From Description — Optional)
Description me `repo:` keyword ke baad jo repos specify hon, woh bhi clone honge.
Additional repos ALWAYS `main` branch par rahenge (no Cyborg branch).

Common additional repos (examples):
- Course, Course_ACLE, Wiki, Role, PyOS, PySDK
- Ya koi bhi valid repo name jo remote par exist kare

## 3.3 Remote URL Template
```
https://github.com/AliensCompany/{RepoName}.git
```
- Organization: `AliensCompany`
- Protocol: HTTPS (default)
- Repo name = folder name (exact match)

## 3.4 Git Account (SSOT — NON-NEGOTIABLE)
```
Account: ianilcyborg@gmail.com
```
- Saare Git operations (clone, pull, push, fetch) **sirf aur sirf** `ianilcyborg@gmail.com` account se honge.
- Kisi bhi dusre GitHub account se pull/push/clone karna **permanently forbidden** hai.
- Cyborg setup ke dauran `git config` se yeh identity set hogi har cloned repo me:
  ```bash
  git config user.email "ianilcyborg@gmail.com"
  ```
- Agar kisi repo me already koi aur account configured ho, to overwrite karo is account se.
- Push/Pull fail hone par **credentials check** me bhi sirf yeh account valid hai.

---

# [04] Manifest Gate
1) Read `/Aliens/manifest.json` (agar exist kare)
2) Note: naye machine par manifest exist nahi karega — yeh acceptable hai
3) Agar manifest exist kare:
   - Verify `AllowShellCommands: true`
   - Check approval mode
4) Agar manifest exist nahi kare:
   - Proceed with default safe settings (NoDelete: true, manual-like caution)
   - Log: "Manifest not found — using safe defaults for fresh machine setup"
5) Internet required — verify connectivity

---

# [05] Pre-Setup Validation

### Step 5.1: Validate Cyborg ID
```
Input: Targets = "C1094"
```
- Trim whitespace
- Check not empty => `CYBORG_ERR_INVALID_ID`
- Check valid characters (alphanumeric + hyphen + underscore)
- Check not a reserved branch name (main, master, develop, etc.)
- Check valid Git branch name: `git check-ref-format --branch {CyborgID}`

### Step 5.2: Validate Description
- Check not empty => `ACC_ERR_DESCRIPTION_REQUIRED`
- Parse for `repo:` keyword to extract additional repos (if any)

### Step 5.3: Build Full Repo List
1. Start with Default Repos (Group A + Group B from Section 03)
2. Parse additional repos from Description (if `repo:` keyword found)
3. Deduplicate (agar koi repo default + additional dono me ho)
4. Final list ready with each repo's branch strategy tagged

### Step 5.4: Verify /Aliens/ Directory
- `/Aliens/` must exist and be writable
- Agar nahi => `CYBORG_ERR_ALIENS_ROOT_MISSING`

### Step 5.5: Verify Git Installed
```bash
git --version
```
- Agar Git nahi mila => `CYBORG_ERR_GIT_NOT_INSTALLED`

### Step 5.6: Verify Internet Connectivity (Best Effort)
```bash
git ls-remote https://github.com/AliensCompany/.github.git HEAD
```
- Agar fail => `CYBORG_ERR_NETWORK_FAILED`
- Warning: authentication bhi verify ho jayegi

---

# [06] Phase 1 — Windows System Configuration

## 6.1 Enable Dark Theme
Windows ko Dark/App theme par switch karna.

```powershell
# Set App theme to Dark
Set-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" -Name "AppsUseLightTheme" -Value 0 -Type DWord

# Set System theme to Dark
Set-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" -Name "SystemUsesLightTheme" -Value 0 -Type DWord
```

- Value `0` = Dark, `1` = Light
- Registry path: `HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize`
- Agar registry key access fail => `CYBORG_ERR_REGISTRY_ACCESS` (warning, continue)

### Verification:
```powershell
$apps = Get-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" -Name "AppsUseLightTheme"
$system = Get-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" -Name "SystemUsesLightTheme"
# Both should be 0
```

## 6.2 Set Wallpaper to Solid Black
Desktop wallpaper ko solid black color me set karna.

```powershell
# Step 1: Remove current wallpaper (set to blank = solid color takes effect)
Set-ItemProperty -Path "HKCU:\Control Panel\Desktop" -Name "WallPaper" -Value ""

# Step 2: Set solid background color to Black (RGB: 0,0,0)
Set-ItemProperty -Path "HKCU:\Control Panel\Colors" -Name "Background" -Value "0 0 0"

# Step 3: Apply changes via SystemParametersInfo
Add-Type -TypeDefinition @"
using System;
using System.Runtime.InteropServices;
public class Wallpaper {
    [DllImport("user32.dll", CharSet = CharSet.Auto)]
    public static extern int SystemParametersInfo(int uAction, int uParam, string lpvParam, int fuWinIni);
    public const int SPI_SETDESKWALLPAPER = 0x0014;
    public const int SPIF_UPDATEINIFILE = 0x01;
    public const int SPIF_SENDCHANGE = 0x02;
}
"@
[Wallpaper]::SystemParametersInfo([Wallpaper]::SPI_SETDESKWALLPAPER, 0, "", [Wallpaper]::SPIF_UPDATEINIFILE -bor [Wallpaper]::SPIF_SENDCHANGE)
```

- Black = RGB(0, 0, 0) => registry value `"0 0 0"`
- WallPaper empty string = no image wallpaper, solid color take effect
- Agar fail => `CYBORG_ERR_WALLPAPER_FAILED` (warning, continue)

### Verification:
```powershell
$wp = Get-ItemProperty -Path "HKCU:\Control Panel\Desktop" -Name "WallPaper"
$bg = Get-ItemProperty -Path "HKCU:\Control Panel\Colors" -Name "Background"
# WallPaper should be "" (empty), Background should be "0 0 0"
```

---

# [07] Phase 2 — Developer Tools Installation

## 7.1 Install Python (Latest — System Default Location)
Python latest stable version install karna. Installation default system path par hogi (NOT in /Aliens/).

### Strategy: winget (preferred)
```powershell
# Check if Python already installed
python --version 2>$null
```

**Case A: Python already installed**
- Log current version
- Check if latest => skip or note
- Mark: `PYTHON_ALREADY_INSTALLED`

**Case B: Python not installed**
```powershell
# Install via winget (Microsoft's package manager)
winget install Python.Python.3 --accept-source-agreements --accept-package-agreements
```

### Fallback: Manual download guidance
Agar winget available nahi hai:
- Log: "winget not available. Install Python manually from https://python.org"
- Mark: `CYBORG_WARN_MANUAL_INSTALL_NEEDED`

### Post-Install Verification:
```powershell
# Verify Python
python --version

# Verify pip
pip --version
```

### Important Notes:
- Installation path: system default (e.g., `C:\Users\{User}\AppData\Local\Programs\Python\`)
- "Add to PATH" must be enabled
- /Aliens/ folder me install NAHI karna

## 7.2 Install PHP (Latest — System Default Location)
PHP latest stable version install karna. Installation default system path par hogi (NOT in /Aliens/).

### Strategy: winget (preferred)
```powershell
# Check if PHP already installed
php --version 2>$null
```

**Case A: PHP already installed**
- Log current version
- Mark: `PHP_ALREADY_INSTALLED`

**Case B: PHP not installed**
```powershell
# Install via winget
winget install PHP.PHP --accept-source-agreements --accept-package-agreements
```

### Fallback: Manual download guidance
Agar winget available nahi hai:
- Log: "winget not available. Install PHP manually from https://php.net"
- Mark: `CYBORG_WARN_MANUAL_INSTALL_NEEDED`

### Post-Install Verification:
```powershell
# Verify PHP
php --version
```

### Important Notes:
- Installation path: system default (e.g., `C:\php\` or winget managed)
- Ensure php is in PATH
- /Aliens/ folder me install NAHI karna

---

# [08] Phase 3 — Manifest Download & CyborgID Injection

## 8.1 Download manifest.json
Remote server se manifest.json download karna.

### Source URL:
```
https://anilcyborg.com/Aliens/manifest.json
```

### Download Command:
```powershell
# Download manifest.json to /Aliens/
Invoke-WebRequest -Uri "https://anilcyborg.com/Aliens/manifest.json" -OutFile "C:\Aliens\manifest.json" -UseBasicParsing
```

### Pre-Download Check:
```powershell
# Check if manifest.json already exists
Test-Path "C:\Aliens\manifest.json"
```
- Agar already exists:
  - Backup existing: `Copy-Item "C:\Aliens\manifest.json" "C:\Aliens\manifest.json.bak.{timestamp}"`
  - Then download fresh copy (overwrite allowed — manifest is config, not code)
- Agar not exists:
  - Direct download

### Download Validation:
- File size > 0 bytes
- Valid JSON format check:
  ```powershell
  Get-Content "C:\Aliens\manifest.json" | ConvertFrom-Json
  ```
- Agar invalid JSON => `CYBORG_ERR_MANIFEST_INVALID`
- Agar download fail => `CYBORG_ERR_MANIFEST_DOWNLOAD_FAILED`

## 8.2 Inject CyborgID into manifest.json
Downloaded manifest me `C1001` (default/template CyborgID) ko actual CyborgID se replace karna.

### Replace Logic:
```powershell
# Read manifest content
$content = Get-Content "C:\Aliens\manifest.json" -Raw

# Replace all occurrences of C1001 with actual CyborgID
$content = $content -replace "C1001", "{CyborgID}"

# Write back
Set-Content "C:\Aliens\manifest.json" -Value $content -Encoding UTF8
```

### Important Rules:
- Replace **ALL** occurrences of `C1001` in the file (not just first)
- Preserve file encoding (UTF-8)
- Preserve JSON structure (no formatting changes)
- CyborgID = Targets input (e.g., `C1094`)

### Post-Replace Verification:
```powershell
# Verify C1001 is gone
$content = Get-Content "C:\Aliens\manifest.json" -Raw
if ($content -match "C1001") { Write-Warning "C1001 still found in manifest!" }

# Verify CyborgID is present
if ($content -match "{CyborgID}") { Write-Host "CyborgID injected successfully" }

# Verify still valid JSON
$content | ConvertFrom-Json
```

---

# [09] Phase 4 — Git Clone Execution (Per Repo — Sequential)

Processing order: Group B (main-only) pehle, phir Group A (Cyborg branch wale), phir Additional repos.
Reason: Group B repos (Cyborg, Copilot, .github, Project) me se kuch setup/config repos hain jo pehle hone chahiye.

### Git Identity Setup (Before Any Clone — One Time)
```bash
# Global Git identity set karo (har repo ke liye apply hogi)
git config --global user.email "ianilcyborg@gmail.com"
git config --global user.name "Anil Cyborg"
```
- Yeh step sirf 1 baar hoga, sab clones se pehle.
- Ensures har commit/push `ianilcyborg@gmail.com` identity se ho.

### Per Repo Steps:

#### Step 9.1: Check Folder Exists
```powershell
Test-Path "C:\Aliens\{RepoName}"
```
- Agar folder EXISTS:
  - Log: "Folder /Aliens/{RepoName} already exists — SKIPPED clone"
  - Mark: `CLONE_SKIPPED_EXISTS`
  - **DO NOT delete** (NoDelete enforced)
  - Jump to Step 10 (branch creation) — kyunke repo already hai to branch banana attempt karo

- Agar folder NOT exists:
  - Proceed to Step 9.2

#### Step 9.2: Clone Repo
```bash
git clone https://github.com/AliensCompany/{RepoName}.git /Aliens/{RepoName}
```

Clone flags:
- Full clone (no shallow) — default
- No specific branch flag — default branch clone hogi

#### Step 9.3: Clone Result Handling

| Result | Action |
|--------|--------|
| Success | Mark `CLONE_SUCCESS`, proceed to Step 9.4 |
| Auth Failure | Mark `CLONE_ERR_AUTH`, skip repo |
| Repo Not Found (404) | Mark `CLONE_ERR_NOT_FOUND`, skip repo |
| Network Failure | Mark `CLONE_ERR_NETWORK`, skip repo |
| Other Error | Mark `CLONE_ERR_UNKNOWN`, capture error, skip repo |

#### Step 9.4: Post-Clone Verification
```bash
# Verify .git folder exists
test -d /Aliens/{RepoName}/.git

# Verify remote
git -C /Aliens/{RepoName} remote -v

# Verify current branch
git -C /Aliens/{RepoName} branch --show-current
```

---

# [10] Branch Creation (Delegated to `Git_Branch`)

Yeh step SIRF Group A repos ke liye hai (branch = Cyborg ID).
Group B aur Additional repos par yeh step SKIP hoga.

**Inline branch logic hata di gayi hai.** Branch creation ab `Git_Branch`
workflow delegate karti hai (SSOT: `Workflows/ACC/Git/Git_Branch.md`).
Yeh Cyborg setup + Check-in + standalone re-bootstrap — teeno jagah se
same implementation use karta hai, drift ka chance zero.

### Step 10.1: Invoke Git_Branch once (all Group A repos processed internally)
```
Workflow("Git_Branch", "{CyborgID}", "Cyborg setup — Phase 4 branch provisioning")
```

### Step 10.2: Git_Branch contract guarantees (recap — do not re-implement here)
- Idempotent: existing branch ko re-use karta hai, kabhi recreate nahi.
- Additive-only: koi branch delete / rename / force-push nahi.
- 4-case handler (A local+remote / B remote-only / C local-only / D neither).
- Cyborg + .github + WebOS repos untouched (safety-locked).
- Dirty working tree: stash-and-restore (Git_Stash contract), warn on pop conflict.
- Per-repo isolated failure: ek repo ka fail dusre ko rokta nahi.

### Step 10.3: Aggregate Git_Branch report
- Git_Branch ka structured per-repo report (status: `GIT_BRANCH_OK_*` / `GIT_BRANCH_ERR_*` / `GIT_BRANCH_SKIP_*`) Cyborg setup ke Phase 4 section me include karo.
- Koi bhi `GIT_BRANCH_ERR_*` SOFT warning hai — Cyborg setup aage badhta rahega (Phase 5 → 6). HARD fail sirf tab jab `Git_Branch` identity gate fail ho.

### Step 10.4: Verify at least .Alien on CyborgID branch (pre-condition for Phase 5)
```bash
git -C /Aliens/.Alien branch --show-current
```
- Expect: `{CyborgID}`
- Mismatch => `CYBORG_ERR_BRANCH_MISMATCH` (Phase 5 blocked, user action required)

---

# [11] Phase 5 — .Alien Folder Duplication (CyborgID)

## Purpose
`.Alien` repo ke andar ek `Alien` naam ka folder hota hai (developer template folder).
Is folder ko duplicate (copy) karna hai aur copy ka naam CyborgID rakhna hai.
Yeh step clone + branch creation ke baad hoga.

## Pre-Condition
- `.Alien` repo clone ho chuka ho (Phase 4 complete)
- `.Alien` repo me Cyborg branch checked-out ho (Phase 4 Branch step complete)
- `/Aliens/.Alien/Alien/` folder exist kare

### Step 11.1: Verify Source Folder Exists
```powershell
Test-Path "C:\Aliens\.Alien\Alien"
```
- Agar folder NOT exists:
  - Mark: `CYBORG_ERR_ALIEN_FOLDER_MISSING`
  - Log: "Source folder /Aliens/.Alien/Alien/ not found — cannot duplicate"
  - Skip this phase (warning, not fatal)

### Step 11.2: Check Destination Folder Does NOT Exist
```powershell
Test-Path "C:\Aliens\.Alien\{CyborgID}"
```
- Agar destination folder EXISTS:
  - Mark: `CYBORG_WARN_FOLDER_ALREADY_EXISTS`
  - Log: "Folder /Aliens/.Alien/{CyborgID}/ already exists — SKIPPED duplication"
  - **DO NOT overwrite/delete** (NoDelete enforced)
  - Skip this step

### Step 11.3: Copy/Duplicate Folder
```powershell
Copy-Item -Path "C:\Aliens\.Alien\Alien" -Destination "C:\Aliens\.Alien\{CyborgID}" -Recurse
```
- Full recursive copy (all files + subfolders)
- Preserve file timestamps
- Agar copy fail => `CYBORG_ERR_FOLDER_COPY_FAILED`

### Step 11.4: Verify Duplication
```powershell
# Verify destination exists
Test-Path "C:\Aliens\.Alien\{CyborgID}"

# Compare file count (should be equal)
$sourceCount = (Get-ChildItem "C:\Aliens\.Alien\Alien" -Recurse -File).Count
$destCount = (Get-ChildItem "C:\Aliens\.Alien\{CyborgID}" -Recurse -File).Count
# $sourceCount should equal $destCount
```
- Mark: `FOLDER_DUPLICATED` agar verification pass
- Agar file count mismatch => `CYBORG_WARN_COPY_INCOMPLETE`

### Step 11.5: Git Add + Commit (Optional — On Cyborg Branch)
Duplicated folder ko git me track karna:
```bash
git -C /Aliens/.Alien add "{CyborgID}/"
git -C /Aliens/.Alien commit -m "Cyborg setup: duplicated Alien folder as {CyborgID}"
git -C /Aliens/.Alien push origin {CyborgID}
```
- Commit message clearly states the purpose
- Push to Cyborg branch (NOT main)
- Push fail => warning only (local commit exists)

---

# [12] Phase 6 — VS Code Configuration

## Purpose
VS Code ki settings me `chat.agent.maxRequests` ko `50000` set karna.
Default value `50` hota hai, jo limited hai.

### Step 12.1: Locate VS Code Settings
VS Code user settings file location (Windows):
```
%APPDATA%\Code\User\settings.json
```
PowerShell path:
```powershell
$settingsPath = "$env:APPDATA\Code\User\settings.json"
```

### Step 12.2: Check Settings File Exists
```powershell
Test-Path $settingsPath
```
- Agar NOT exists:
  - Create directory if needed:
    ```powershell
    New-Item -ItemType Directory -Path "$env:APPDATA\Code\User" -Force
    ```
  - Create with minimal content:
    ```powershell
    '{"chat.agent.maxRequests": 50000}' | Set-Content $settingsPath -Encoding UTF8
    ```
  - Mark: `VSCODE_SETTINGS_CREATED`
  - Skip to verification

### Step 12.3: Read + Update Existing Settings
```powershell
# Read existing settings
$settings = Get-Content $settingsPath -Raw | ConvertFrom-Json

# Set / Update the property
$settings | Add-Member -NotePropertyName "chat.agent.maxRequests" -NotePropertyValue 50000 -Force

# Write back (preserve formatting)
$settings | ConvertTo-Json -Depth 100 | Set-Content $settingsPath -Encoding UTF8
```

### Important Rules:
- Existing settings preserve karna — sirf `chat.agent.maxRequests` add/update karna
- Koi dusri setting modify nahi karna
- JSON format valid rakhna
- Agar settings JSON invalid/corrupt ho => `CYBORG_ERR_VSCODE_SETTINGS_CORRUPT` (warning, try creating fresh)

### Step 12.4: Verification
```powershell
$verify = Get-Content $settingsPath -Raw | ConvertFrom-Json
$value = $verify."chat.agent.maxRequests"
if ($value -eq 50000) { Write-Host "VS Code maxRequests set to 50000 — OK" }
else { Write-Warning "maxRequests value mismatch: $value" }
```
- Mark: `VSCODE_SETTINGS_UPDATED` agar success

---

# [13] Post-Setup Verification

### Step 13.1: Full Repo Status Summary
Har repo ke liye verify karo:

```bash
# For each cloned repo:
git -C /Aliens/{RepoName} status --short
git -C /Aliens/{RepoName} branch --show-current
git -C /Aliens/{RepoName} remote -v
```

### Step 13.2: Branch Verification Matrix
Generate a verification table:

| Repo | Clone Status | Expected Branch | Active Branch | Match |
|------|-------------|-----------------|---------------|-------|
| .Alien | SUCCESS | C1094 | C1094 | YES |
| WebBrand | SUCCESS | C1094 | C1094 | YES |
| WebOS | SUCCESS | C1094 | C1094 | YES |
| WebSDK | SUCCESS | C1094 | C1094 | YES |
| WebApp | SUCCESS | C1094 | C1094 | YES |
| Docs | SUCCESS | C1094 | C1094 | YES |
| Cyborg | SUCCESS | main | main | YES |
| Copilot | SUCCESS | main | main | YES |
| .github | SUCCESS | main | main | YES |
| Project | SUCCESS | main | main | YES |

---

# [14] Error Codes

| Code | Meaning | Action |
|------|---------|--------|
| **Validation Errors** | | |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | Fail fast |
| `CYBORG_ERR_INVALID_ID` | Cyborg ID empty/invalid/reserved | Fail fast |
| `CYBORG_ERR_ALIENS_ROOT_MISSING` | /Aliens/ directory not found | Fail fast |
| `CYBORG_ERR_GIT_NOT_INSTALLED` | Git not available | Fail fast |
| `CYBORG_ERR_NETWORK_FAILED` | Cannot reach remote | Fail fast |
| **Phase 1 — System Config** | | |
| `CYBORG_ERR_REGISTRY_ACCESS` | Registry key access failed | Warning, continue |
| `CYBORG_ERR_WALLPAPER_FAILED` | Wallpaper set failed | Warning, continue |
| **Phase 2 — Dev Tools** | | |
| `PYTHON_ALREADY_INSTALLED` | Python already present | Log version, continue |
| `PHP_ALREADY_INSTALLED` | PHP already present | Log version, continue |
| `CYBORG_WARN_MANUAL_INSTALL_NEEDED` | winget not available, manual install required | Warning, continue |
| `CYBORG_ERR_PYTHON_INSTALL_FAILED` | Python installation failed | Warning, continue |
| `CYBORG_ERR_PHP_INSTALL_FAILED` | PHP installation failed | Warning, continue |
| **Phase 3 — Manifest** | | |
| `CYBORG_ERR_MANIFEST_DOWNLOAD_FAILED` | manifest.json download failed | Warning, continue |
| `CYBORG_ERR_MANIFEST_INVALID` | Downloaded manifest is not valid JSON | Warning, continue |
| `MANIFEST_DOWNLOADED` | manifest.json downloaded successfully | Continue |
| `MANIFEST_CYBORG_INJECTED` | CyborgID injected into manifest | Continue |
| **Phase 4 — Git Clone** | | |
| `CLONE_SUCCESS` | Repo cloned successfully | Continue |
| `CLONE_SKIPPED_EXISTS` | Folder already existed, clone skipped | Continue to branch step |
| `CLONE_ERR_AUTH` | Authentication failed during clone | Skip repo |
| `CLONE_ERR_NOT_FOUND` | Remote repo not found (404) | Skip repo |
| `CLONE_ERR_NETWORK` | Network error during clone | Skip repo |
| `CLONE_ERR_UNKNOWN` | Other clone error | Skip repo |
| `CYBORG_ERR_NO_DEFAULT_BRANCH` | main/master branch not found in repo | Skip branch, warning |
| `CYBORG_ERR_PUSH_FAILED` | Could not push new branch to remote | Warning only |
| `CYBORG_ERR_BRANCH_MISMATCH` | Active branch != expected after setup | Warning |
| `BRANCH_CREATED` | New branch created from main | Continue |
| `BRANCH_SKIPPED_EXISTS` | Branch already existed | Checkout existing |
| **Phase 5 — .Alien Folder** | | |
| `CYBORG_ERR_ALIEN_FOLDER_MISSING` | Source folder /Aliens/.Alien/Alien/ not found | Warning, skip phase |
| `CYBORG_WARN_FOLDER_ALREADY_EXISTS` | Destination folder already exists | Skip, no overwrite |
| `CYBORG_ERR_FOLDER_COPY_FAILED` | Folder copy operation failed | Warning, continue |
| `CYBORG_WARN_COPY_INCOMPLETE` | File count mismatch after copy | Warning |
| `FOLDER_DUPLICATED` | Alien folder duplicated as CyborgID | Continue |
| **Phase 6 — VS Code** | | |
| `VSCODE_SETTINGS_CREATED` | settings.json created from scratch | Continue |
| `VSCODE_SETTINGS_UPDATED` | maxRequests updated in existing settings | Continue |
| `CYBORG_ERR_VSCODE_SETTINGS_CORRUPT` | settings.json invalid JSON | Warning, try fresh |

---

# [15] Permanently Forbidden Operations
```
Deleting existing folders/repos
Overwriting existing folders/repos
Force pushing (--force / --force-with-lease)
Rebase (any form)
Reset --hard (any form)
Deleting branches (local or remote)
Cloning outside /Aliens/
Using reserved branch names as Cyborg ID
Running multiple Cyborg IDs in single invocation
Installing dev tools inside /Aliens/ folder
Modifying Windows system files beyond theme/wallpaper
Changing VS Code settings beyond chat.agent.maxRequests
```

---

# [16] Execution Flow Summary (Quick Reference)

```
START
  │
  ├── [05] Validate Cyborg ID + Description
  │     ├── Parse additional repos from Description
  │     └── Build full repo list with branch strategies
  │
  ├── [06] Phase 1 — Windows System Config
  │     ├── Enable Dark Theme (registry)
  │     └── Set Wallpaper = Solid Black (registry + SystemParametersInfo)
  │
  ├── [07] Phase 2 — Dev Tools Installation
  │     ├── Install Python latest (winget / manual guidance)
  │     └── Install PHP latest (winget / manual guidance)
  │
  ├── [08] Phase 3 — Manifest Download + CyborgID Injection
  │     ├── Download manifest.json from anilcyborg.com
  │     ├── Backup existing manifest (if any)
  │     └── Replace C1001 → {CyborgID} in manifest
  │
  ├── [09] Phase 4 — Clone Phase (sequential, per repo)
  │     ├── Group B repos first (main-only: Cyborg, Copilot, .github, Project)
  │     ├── Group A repos next (Cyborg branch: .Alien, WebBrand, WebOS, WebSDK, WebApp, Docs)
  │     └── Additional repos last (main-only)
  │
  ├── [10] Branch Phase (Group A repos only)
  │     ├── Checkout main
  │     ├── Pull latest
  │     ├── Create branch {CyborgID} from main
  │     ├── Push branch to remote
  │     └── Verify active branch
  │
  ├── [11] Phase 5 — .Alien Folder Duplication
  │     ├── Copy /Aliens/.Alien/Alien/ → /Aliens/.Alien/{CyborgID}/
  │     ├── Verify file count match
  │     └── Git add + commit + push on Cyborg branch
  │
  ├── [12] Phase 6 — VS Code Configuration
  │     └── Set chat.agent.maxRequests = 50000
  │
  ├── [13] Post-Setup Verification
  │     └── Generate full status + branch verification matrix
  │
  └── [17] Status Report
END
```

---

# [17] Status Report (Per Run)

```
=== Cyborg Setup Report ===
Cyborg ID: {CyborgID}
Timestamp: {ISO-8601}
Description: {Description}
Setup Type: {Default | Extended}

--- Phase 1: System Config ---
| Setting | Status |
|---------|--------|
| Dark Theme | ENABLED |
| Wallpaper (Solid Black) | SET |

--- Phase 2: Dev Tools ---
| Tool | Status | Version |
|------|--------|---------|
| Python | INSTALLED / ALREADY_INSTALLED | 3.x.x |
| PHP | INSTALLED / ALREADY_INSTALLED | 8.x.x |

--- Phase 3: Manifest ---
| Step | Status |
|------|--------|
| Download manifest.json | SUCCESS |
| CyborgID Injection (C1001 → {CyborgID}) | SUCCESS |

--- Phase 4: Clone Status ---
| Repo | Clone Status | Notes |
|------|-------------|-------|
| .github | SUCCESS | - |
| Project | SUCCESS | - |
| Cyborg | SUCCESS | - |
| Copilot | SUCCESS | - |
| .Alien | SUCCESS | - |
| WebBrand | SUCCESS | - |
| WebOS | SUCCESS | - |
| WebSDK | SUCCESS | - |
| WebApp | SUCCESS | - |
| Docs | SUCCESS | - |

--- Branch Status ---
| Repo | Branch | Status | Pushed |
|------|--------|--------|--------|
| .Alien | {CyborgID} | CREATED | YES |
| WebBrand | {CyborgID} | CREATED | YES |
| WebOS | {CyborgID} | CREATED | YES |
| WebSDK | {CyborgID} | CREATED | YES |
| WebApp | {CyborgID} | CREATED | YES |
| Docs | {CyborgID} | CREATED | YES |
| Cyborg | main | KEPT | - |
| Copilot | main | KEPT | - |
| .github | main | KEPT | - |
| Project | main | KEPT | - |

--- Additional Repos (if any) ---
| Repo | Clone Status | Branch |
|------|-------------|--------|
| Course | SUCCESS | main |
| Course_ACLE | SUCCESS | main |

--- Phase 5: .Alien Folder Duplication ---
| Source | Destination | Status | Files |
|--------|------------|--------|-------|
| Alien | {CyborgID} | DUPLICATED | {N} files |

--- Phase 6: VS Code Config ---
| Setting | Value | Status |
|---------|-------|--------|
| chat.agent.maxRequests | 50000 | UPDATED |

=== Summary ===
Total repos: {N} | Success: {S} | Skipped: {K} | Failed: {F}
Phases completed: {X}/6
Cyborg {CyborgID} setup: {COMPLETE | PARTIAL}
```

---

# [18] Done Criteria
- Cyborg ID validated
- Phase 1: Windows dark theme enabled + wallpaper solid black
- Phase 2: Python + PHP latest version installed (or already present confirmed)
- Phase 3: manifest.json downloaded, C1001 replaced with CyborgID
- Phase 4: All default repos clone attempted (success/skip individually)
- Phase 4: All additional repos (if any) clone attempted
- Phase 4: Group A repos — Cyborg ID branch created from main + pushed + verified
- Phase 4: Group B + Additional repos — confirmed on main branch
- Phase 5: .Alien/Alien/ folder duplicated as .Alien/{CyborgID}/
- Phase 6: VS Code chat.agent.maxRequests set to 50000
- No existing folder/branch overwritten
- No data loss
- Full status report generated with all phases
- Cyborg machine ready for development
