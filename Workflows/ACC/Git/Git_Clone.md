---
WorkflowId: Git_Clone
Type: Workflow_Utility
Domain: Git
Category: Version Control — Clone
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-17
EntryPoint: true
Purpose: >
  Safe Git Clone workflow: given a remote repo URL aur target folder name, repo ko
  /Aliens/ ke under clone karo. Existing folder overwrite nahi hoga. Clone ke baad
  verify karo ke repo intact hai aur sab branches/tags aaye hain.
Invocation:
  Signature:
    - Workflow("Git_Clone", "https://github.com/AliensCompany/Course_ACLE.git", "Clone Course_ACLE repo")
    - Workflow("Git_Clone", "https://github.com/AliensCompany/WebOS.git", "Clone WebOS repo for new machine")
    - Workflow("Git_Clone", "https://github.com/AliensCompany/WebOS.git | WebOS_Dev", "Clone with custom folder name")
  TargetsMeaning: >
    Remote repo URL (HTTPS ya SSH). Comma-separated for multiple repos allowed.
    Optional: URL ke baad " | FolderName" se custom folder name de sakte ho.
    Without custom name: folder name = repo name from URL (minus .git suffix).
    All clones go into /Aliens/ root directory.
  DescriptionMeaning: >
    Human language context: kyun clone ho raha hai, kis machine/purpose ke liye.
    Required — empty/missing => fail fast. Pure informational.
Safety:
  NoDelete: true
  NoOverwrite: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Existing folder overwrite PERMANENTLY FORBIDDEN.
  - Agar target folder already exist kare => STOP, skip that repo.
  - Only HTTPS aur SSH URLs allowed.
  - Clone depth: full clone by default (shallow clone only if explicitly asked in Description).
  - LFS: agar repo me Git LFS hai, `git lfs pull` bhi run hoga.
  - Sab clones /Aliens/ ke under hi honge — kisi aur location par clone forbidden.
---

# Git_Clone

# [00] Philosophy (NON-NEGOTIABLE)
```
SAFETY > SPEED > CONVENIENCE
```
- Existing folder/data overwrite kabhi nahi.
- Clone fail ho jaye to partial folder cleanup karo (don't leave broken state).
- Har step verify karo pehle, action baad me.

# [01] Strict Scope Lock
Yeh workflow sirf yeh karta hai:
- Remote repo ko /Aliens/ ke under clone karna
- Clone ke baad verification
- LFS pull (agar applicable)

Forbidden:
- Existing folder ko overwrite/delete karna
- /Aliens/ se bahar kisi location par clone karna
- Clone ke baad code modify karna
- Branch switch karna (default branch pe rehna)
- Koi bhi Git history manipulation
- Scope se bahar koi kaam

# [02] Inputs

## Targets
- One or more remote repo URLs
- Comma-separated for multiple: `"url1, url2"`
- Each URL can optionally have ` | FolderName` suffix for custom folder name
- Supported URL formats:
  - HTTPS: `https://github.com/org/repo.git`
  - SSH: `git@github.com:org/repo.git`
  - Without `.git` suffix: also acceptable (Git handles it)

### Target Parsing
```
Single URL:
  "https://github.com/AliensCompany/Course_ACLE.git"
  => FolderName = "Course_ACLE" (extracted from URL)
  => ClonePath = "/Aliens/Course_ACLE/"

URL with custom name:
  "https://github.com/AliensCompany/WebOS.git | WebOS_Dev"
  => FolderName = "WebOS_Dev" (from pipe suffix)
  => ClonePath = "/Aliens/WebOS_Dev/"

Multiple:
  "url1, url2 | CustomName"
  => Comma split, each processed independently
```

### Folder Name Extraction from URL
1. URL ka last path segment lo (e.g., `Course_ACLE.git`)
2. `.git` suffix remove karo (e.g., `Course_ACLE`)
3. Result = folder name
4. Agar extraction fail ho (weird URL) => `GIT_ERR_INVALID_URL`

## Description
- Required (non-empty)
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`
- May contain special instructions:
  - "shallow clone" => use `--depth 1` (only if explicitly present)
  - "branch {name}" => clone specific branch with `-b {name}`

# [03] Manifest Gate
1) Read `/Aliens/manifest.json`
2) Verify `AllowShellCommands: true`
3) `AllowCreate: true` (we're creating new folder)
4) `NoDelete: true` (relevant for cleanup — see Step 7)
5) Internet required for clone — verify connectivity

# [04] URL Validation (Per Target)

### Step 1: Parse URL + FolderName
- Split by ` | ` (pipe with spaces)
- Left part = URL, Right part = FolderName (optional)
- Trim both parts
- If no pipe: extract folder name from URL

### Step 2: Validate URL Format
- Must start with `https://` or `git@` or `ssh://`
- Must contain a path/repo identifier
- Random/local paths forbidden
- File:// protocol forbidden (security risk — local file access)
- Agar invalid format => `GIT_ERR_INVALID_URL`

### Step 3: Validate Folder Name
- Must be a valid folder name (no slashes, no special chars except - and _)
- Must not be empty
- Must not be `.` or `..`
- Must not be a reserved Windows name (CON, PRN, AUX, NUL, etc.)

# [05] Pre-Clone Safety Checks (Per Target)

### Step 5.1: Check Target Folder Does NOT Exist
```bash
test -d /Aliens/{FolderName}
```
Or PowerShell:
```powershell
Test-Path "C:\Aliens\{FolderName}"
```

- Agar folder EXISTS => `GIT_ERR_FOLDER_EXISTS`
  - Report: "Folder /Aliens/{FolderName} already exists. Clone skip kiya. 
    Agar fresh clone chahiye to pehle folder rename/move karo manually."
  - **DO NOT delete existing folder** (NoDelete enforced)
  - Skip this target

- Agar folder does NOT exist => safe to clone, proceed

### Step 5.2: Verify Parent Directory Writable
- `/Aliens/` directory must exist and be writable
- Agar not writable => `GIT_ERR_NO_WRITE_PERMISSION`

### Step 5.3: Disk Space Check (Best Effort)
- Check available disk space on the volume where /Aliens/ lives
- Warning if < 1GB free space (clones can be large)
- Not blocking — just informational warning

# [06] Clone Execution (Per Target)

### Step 6.1: Build Clone Command
Default (full clone):
```bash
git clone {URL} /Aliens/{FolderName}
```

Optional flags (from Description parsing):
- If Description contains "shallow clone":
  ```bash
  git clone --depth 1 {URL} /Aliens/{FolderName}
  ```
- If Description contains "branch {name}":
  ```bash
  git clone -b {BranchName} {URL} /Aliens/{FolderName}
  ```

### Step 6.2: Execute Clone
- Run clone command
- Monitor output for progress
- Capture any errors

### Clone Result Handling:

### Case A: Success
- Clone completed, folder created with full repo
- Proceed to Step 7

### Case B: Authentication Failure
- Mark: `GIT_ERR_AUTH_FAILED`
- Report: "Authentication failed. Check credentials/SSH keys/token."
- Cleanup: remove partial folder if created (see Step 6.3)

### Case C: Repo Not Found (404)
- Mark: `GIT_ERR_REMOTE_NOT_FOUND`
- Report: "Remote repo not found at {URL}. Check URL."
- Cleanup: remove partial folder if created

### Case D: Network Failure
- Mark: `GIT_ERR_NETWORK_FAILED`
- Cleanup: remove partial folder if created

### Case E: Other Error
- Mark: `GIT_ERR_CLONE_FAILED`
- Capture error output
- Cleanup: remove partial folder if created

### Step 6.3: Partial Clone Cleanup
Agar clone fail ho jaaye aur partially created folder reh jaaye:
```bash
# Verify it's a partial/broken clone before cleanup
# Only remove if the folder was NOT there before we started (Step 5.1 verified this)
```

Important:
- Sirf tab remove karo jab:
  1. Folder Step 5.1 me exist nahi karta tha (hum ne verify kiya)
  2. Clone command fail hua
  3. Folder ab exist karta hai (partial clone left it)
- NoDelete rule exception: partial broken clone cleanup allowed kyunke yeh workflow ne khud create kiya aur incomplete hai
- Cleanup command:
  ```powershell
  Remove-Item -Recurse -Force "C:\Aliens\{FolderName}"
  ```
- Agar cleanup bhi fail ho jaye: report warning, user ko manually clean karne bolo

# [07] Post-Clone Verification (Per Target)

### Step 7.1: Verify Folder Exists
```bash
test -d /Aliens/{FolderName}/.git
```
- `.git/` folder must exist
- If missing => clone silently failed somehow

### Step 7.2: Verify Remote
```bash
git -C /Aliens/{FolderName} remote -v
```
- `origin` should point to the clone URL

### Step 7.3: Verify Branch
```bash
git -C /Aliens/{FolderName} branch --show-current
```
- Should show default branch (or the -b branch if specified)

### Step 7.4: Verify Integrity
```bash
git -C /Aliens/{FolderName} fsck --no-dangling
```
- Should report no errors
- Minor warnings acceptable (unreachable objects common after clone)

### Step 7.5: Check LFS (If Applicable)
```bash
git -C /Aliens/{FolderName} lfs ls-files 2>&1
```
- Agar LFS tracked files hain:
  ```bash
  git -C /Aliens/{FolderName} lfs pull
  ```
- Agar `git lfs` command not found: warning `GIT_WARN_LFS_NOT_INSTALLED`

### Step 7.6: Branch + Tag Summary
```bash
git -C /Aliens/{FolderName} branch -a
git -C /Aliens/{FolderName} tag -l
```
- Note: total remote branches count + tags count for report

# [08] Multi-Repo Handling
Jab multiple URLs comma-separated diye jaye:

Rules:
- Har repo independently process karo
- Ek repo fail hone se dusri repos SKIP nahi hongi
- Per-repo status maintain karo
- Processing order: targets list me jo order hai, wahi
- Parallel execution forbidden (ek time pe ek repo)

# [09] Error Codes

| Code | Meaning | Action |
|---|---|---|
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | Fail fast |
| `GIT_ERR_INVALID_URL` | URL format invalid | Skip target |
| `GIT_ERR_FOLDER_EXISTS` | Target folder already exists | Skip target (NO delete) |
| `GIT_ERR_NO_WRITE_PERMISSION` | /Aliens/ not writable | Skip target |
| `GIT_ERR_AUTH_FAILED` | Authentication failed | Skip, check creds |
| `GIT_ERR_REMOTE_NOT_FOUND` | Repo URL 404 / not found | Skip target |
| `GIT_ERR_NETWORK_FAILED` | Network error during clone | Cleanup + skip |
| `GIT_ERR_CLONE_FAILED` | Clone failed (other) | Cleanup + skip |
| `GIT_WARN_LFS_NOT_INSTALLED` | Git LFS not available | Warning only, clone still valid |
| `CLONE_SUCCESS` | Clone completed + verified | Done |

# [10] Permanently Forbidden Operations

```
Overwriting existing folders
Deleting existing folders to make room for clone
Clone to locations outside /Aliens/
file:// protocol URLs (security risk)
Modifying cloned repo after clone (this workflow = clone only)
Branch switching after clone
git reset / rebase / force after clone
```

# [11] Status Report (Per Run)

```
=== Git_Clone Report ===
Timestamp: {ISO-8601}
Description: {Description}

| Repo URL | Folder | Status | Branch | Branches | Tags | LFS |
|----------|--------|--------|--------|----------|------|-----|
| https://...Course_ACLE.git | Course_ACLE | SUCCESS | main | 3 | 5 | No |
| https://...WebOS.git | WebOS_Dev | SKIPPED | - | - | - | - |

Total: 2 targets | 1 success | 1 skipped (folder exists)
```

# [12] Done Criteria
- Har target URL processed (success/skip/fail individually)
- No existing folder overwritten
- No data loss
- Partial clone cleanup done where needed
- Successfully cloned repos verified (git fsck, remote check, branch check)
- LFS content pulled where applicable
- Status report generated
