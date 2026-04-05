---
RefId: ACC.Manifest
Type: Reference
Scope: AlienCyborg + ACC Workflows
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
AppliesTo:
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
PrimaryRuntimeFile:
  Path: "/Aliens/manifest.json"
  Format: "JSON"
NonNegotiables:
  - NoDelete: true
  - RepoSafe: true
  - SecretsNeverInGit: true
---

# ACC.Manifest.md
**Goal (Hinglish):**  
Yeh document **AlienCyborg ka SSOT workspace manifest** define karta hai. ACC Workflow engine isi manifest ko read karke decide karega:
- Agent kis mode me chale (employee / assistant)
- approvals auto ya manual hon
- prod code / database touch allow hai ya nahi
- roots/paths kya hain
- default version/theme policies
- worker ka polling behavior

> Important: Aapne clarify kiya hai: **manifest “pure AlienCyborg” ki hogi** (workflow-wise alag manifest nahi).  
> Iska matlab: ek developer ke liye ek manifest, aur saare workflows usi ko follow karenge.

---

## [01] Runtime Location (Mandatory)
SSOT manifest file (single source of truth):

- `/Aliens/manifest.json`

Notes:
- Developer folders like `/Aliens/.Alien/{Developer_Username}/Runs/` exist for artifacts/state, but manifest itself is NOT stored there.
- `Developer_Username` is read from `/Aliens/manifest.json` -> `Developer.Username`.

---

## [02] Manifest Philosophy
Manifest ka kaam:
- **control plane** (rules + permissions)
- **safety gate** (approval + no delete)
- **context builder** (paths + defaults)
- **worker behavior** (employee mode run loop)

Manifest ka kaam nahi:
- workflow ke unique steps define karna
- code style define karna (ye AliensStyle/AliensGrade me hota hai)
- project-specific unique prompts store karna (ye tasks/content me ho sakta hai)

---

## [03] Minimal Required Schema (MUST)
Neeche fields minimum required hain. Missing ho to **error** (aapka rule: “Modify only, Missing ho to error message”).

```json
{
  "SchemaVersion": "26.00.00",
  "Developer": {
    "Username": "Anil",
    "DisplayName": "AlienCyborg",
    "Role": "CoreTeam",
    "Timezone": "Asia/Kolkata"
  },
  "Mode": "assistant",
  "ApprovalMode": "manual",
  "Defaults": {
    "VersionPolicy": "latest",
    "VersionFallback": "PHP.26.00.00",
    "Theme": "_Basic",
    "Language": "hinglish"
  },
  "Paths": {
    "AliensRoot": "/Aliens",
    "WebOSRoot": "/Aliens/WebOS",
    "WebAppRoot": "/Aliens/WebApp",
    "WebBrandRoot": "/Aliens/WebBrand",
    "WebSDKRoot": "/Aliens/WebSDK",
    "DocsRoot": "/Aliens/Docs",
    "DeveloperRoot": "/Aliens/.Alien/{Developer_Username}"
  },
  "Permissions": {
    "NoDelete": true,
    "AllowProdWrite": true,
    "AllowDbTouch": true,
    "AllowedRoots": [
      "/Aliens/WebOS",
      "/Aliens/WebApp",
      "/Aliens/WebBrand",
      "/Aliens/WebSDK",
      "/Aliens/Docs",
      "/Aliens/.Alien/{Developer_Username}"
    ]
  },
  "Worker": {
    "Enabled": false,
    "PollSeconds": 15,
    "TaskFolder": "/Aliens/Project/{Developer_Username}",
    "StopWhenTaskEmpty": false
  },
  "Audit": {
    "RunsFolder": "/Aliens/.Alien/{Developer_Username}/Runs",
    "ApprovalsFolder": "/Aliens/.Alien/{Developer_Username}/Approvals",
    "ReportsFolder": "/Aliens/.Alien/{Developer_Username}/Reports"
  }
}
```

---

## [04] Field-by-Field Rules (Exact)
### 4.1 SchemaVersion (string)
- Must match your current ACC major version: `26.00.00` (or compatible range).
- If mismatch: warn + continue (recommended) OR hard fail (strict mode).

### 4.2 Developer (object)
- `Username`: folder name for `/Aliens/.Alien/{Username}`
- `DisplayName`: logs + reports me use hoga
- `Role`: `CoreTeam` (current scope) but future me `Employee`, `Contractor` add ho sakta hai
- `Timezone`: used for scheduling / timestamps

### 4.3 Mode (string)
Allowed:
- `employee` = agent continuously tasks run karega (Task folder watch)  
- `assistant` = agent bina instructions ke kuch nahi karega

Rules:
- `employee` mode me Worker.Enabled almost always true hoga (recommended).
- `assistant` mode me Worker.Enabled false rakhna safe default hai.

### 4.4 ApprovalMode (string)
Allowed:
- `auto` = approvals auto-pass (faster, but dangerous)
- `manual` = approvals file generate + human approve needed

Rules:
- Manual: workflow ko ApprovalRequest create karna mandatory hai for risky actions.
- Auto: RunRecord me “auto-approved” entry mandatory.

### 4.5 Defaults (object)
- `VersionPolicy`: `latest` recommended (aapka rule)
- `VersionFallback`: default allowed = `PHP.26.00.00`
- `Theme`: `_Basic` (aapka rule)
- `Language`: recommended `hinglish` (no Hindi script)

### 4.6 Paths (object)
Must be absolute. Must exist (recommended validation).
- AliensRoot is always `/Aliens` (as per your definition).

### 4.7 Permissions (object)
- **NoDelete**: always `true` (non-negotiable)
- **AllowProdWrite**: true/false (aapne “yes” bola tha, but manifest me explicit hona chahiye)
- **AllowDbTouch**: true/false (aapne “yes” bola tha, but manifest me explicit hona chahiye)
- **AllowedRoots**: writing only in these roots

Hard rules:
- If a workflow tries to write outside AllowedRoots => hard error.
- If any action implies delete => hard error (even if git revert possible).

### 4.8 Worker (object)
- `Enabled`: when true, agent Task folder watch karega
- `PollSeconds`: recommended 10–30 seconds
- `TaskFolder`: must be inside DeveloperRoot
- `StopWhenTaskEmpty`: if false, worker infinite watch (employee behavior)

CSV task prompt rule (hard):
- Agar Worker/Workflow CSV row process kar raha ho, row ka `Description` (planner) ya `description` (engine CSV) prompt/context hai.
- Empty/missing ho to fail with `ACC_ERR_CSV_DESCRIPTION_MISSING`.

### 4.9 Audit (object)
- RunsFolder: every workflow run ka complete record
- ApprovalsFolder: manual approvals yahan generate hongi
- ReportsFolder: summaries / analytics

---

## [05] Safety Policy Mapping
Manifest me permissions + approvalMode milkar safety decide karte hain.

Recommended logic (common engines implement):
- If `AllowDbTouch=true` AND `ApprovalMode=manual` => DB changes allowed only after approval
- If `AllowDbTouch=true` AND `ApprovalMode=auto` => allowed, but strong audit mandatory
- If `AllowDbTouch=false` => any DB touch request = hard error

Same for prod writes.

---

## [06] Secrets Rule (Mandatory)
Secrets kabhi bhi repo ya workflow outputs me nahi likhne.
Recommended:
- `/Aliens/.Alien/{Developer}/Secrets/` (gitignored)
- Workflows sirf secret “key name” store karein, value nahi.

---

## [07] Optional (Highly Recommended) Extensions
Ye fields optional hain, lekin enterprise-level control ke liye best hain:

```json
{
  "Chat": {
    "Memory": {
      "AutoWrite": true
    }
  },
  "Limits": {
    "MaxFilesPerRun": 250,
    "MaxTargetsPerRun": 100,
    "MaxWriteBytesPerFile": 2000000
  },
  "Git": {
    "Enabled": true,
    "RepoRoot": "/Aliens/.Alien/{Developer_Username}",
    "Branch": "main",
    "RequireCleanStatusBeforeRun": false
  },
  "UIQuality": {
    "PremiumDefinitionRef": "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  },
  "Telemetry": {
    "Enabled": true,
    "LocalOnly": true,
    "Anonymize": true
  }
}
```

---

## [08] Two Ready Examples
### Example A: Assistant Mode (safe)
- Mode: assistant
- ApprovalMode: manual
- Worker.Enabled: false

### Example B: Employee Mode (fast execution)
- Mode: employee
- ApprovalMode: auto (or manual if high risk)
- Worker.Enabled: true
- Task folder me CSV/Excel tasks continuously process

---

## [09] Validation Checklist (MUST)
Workflow engine (Plural/Singular common files) ko manifest validate karna chahiye:

- File exists
- JSON parse ok
- Required keys present
- NoDelete=true
- Paths start with `/Aliens`
- AllowedRoots contains intended write targets
- ApprovalMode valid
- Mode valid
- Worker config consistent (employee => enabled recommended)

---

## [10] Change Control
Manifest me change ka effect:
- turant saare workflows par apply ho jayega
Isliye recommended:
- Runs/ folder me snapshot copy save ho
- SchemaVersion bump or changelog maintain ho (optional)

---

## [11] Future Notes (2026+ Memory Plan)
Aapne bola memory 1 Jan 2026 ke baad. Manifest me future me yeh fields add ho sakte:
- `Memory`: enabled/disabled, storage location, retention, privacy controls
- `Persona`: “AlienCyborg” identity + behavior flags

(Abhi ke liye manifest ka focus: workflow execution governance.)
