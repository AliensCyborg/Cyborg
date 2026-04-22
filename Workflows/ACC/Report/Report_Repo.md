---
WorkflowId: Report_Repo
Title: "Workspace Repos BI Reports + In-Repo Dashboard Refresh"
Category: System
Type: Workflow_Plural
Domain: Report
Scope: "Generate BI reports for workspace repos (top-level code folders) AND refresh each repo's in-place Dashboard.md."

Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-19

Intent:
  Summary: >
    Yeh workflow workspace ke top-level **repos** (jaise WebOS/, WebSDK/, WebApp/, AI/, Copilot/, Docs/, ACLE/, ATLE/, Skill_*, Wiki_*, Blog/, Course_*)
    ke liye BI reports banata hai aur parallel me har repo ki in-repo /Aliens/{RepoName}/Dashboard.md bhi refresh karta hai.
    2 target modes: All repos, ya single Repo.
  WhenToUse:
    - "Saare repos ka BI snapshot chahiye"
    - "Kisi ek repo ka deep BI chahiye"
    - "In-repo Dashboard.md files ko latest data se refresh karna ho"
  SiblingWorkflows:
    - "Report           → Single-Cyborg activity reports"
    - "Report_Project   → ACC Planner projects reports"
    - "Report_Alien     → Human team-member consolidated reports"

Inputs:
  Targets:
    Modes:
      - "All               → every repo listed in Repos.Index.md"
      - "{RepoName}        → single repo (e.g. 'WebOS', 'WebSDK', 'ACLE', 'Docs')"
    Examples:
      - "All"
      - "WebOS"
      - "WebSDK"
      - "ACLE"
      - "Docs"
    Validation:
      - "Exactly 1 target allowed"
      - "Target == 'All'    → Mode A"
      - "Target matches a name in Ecosystem/Repos.Index.md → Mode B"
      - "Unresolvable => ACC_ERR_REPORT_REPO_UNKNOWN"
  Description:
    Format: "Any human language"

Defaults:
  ReportFormat: "md"
  InRepoDashboard: true
  ExcludeRepos:
    - "Attendance   # owned by Attendance_Dashboard workflow"
  ExcludeFromScan:
    - ".git"
    - "node_modules"
    - "__pycache__"
    - ".Alien"
    - "Report"

Permissions:
  NoDelete: true
  CanCreateFiles: true
  CanEditFiles: true
  CanModifyProd: false
  CanTouchDB: false
  CanRunTerminal: true

Approval:
  Mode: "auto"

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Plural.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
  Data:
    - "/Aliens/manifest.json"
    - "Cyborg/AnilCyborg/Ecosystem/Repos.Index.md"
  Knowledge:
    - "Cyborg/AnilCyborg/Ecosystem/Members.Aliens_vs_Cyborgs.md"

Calls:
  ChildWorkflows:
    - "Report-Planning"
    - "Report-Code"
    - "Report-Documentation"

Outputs:
  RunRecord: true
  Planning: true
  Code: true
  Documentation: false
  ReportFiles:
    Root: "/Aliens/Report/Repo/{RepoName}/"
    Files:
      - "Daily_Report_YYYY-MM-DD.md"
      - "Weekly_Report_YYYY-WNN.md"
      - "Monthly_Report_YYYY-MM.md"
      - "Yearly_Report_YYYY.md"
      - "Dashboard.md"
  InRepoDashboard:
    Path: "/Aliens/{RepoName}/Dashboard.md"
    CreateIfMissing: true
    OverwriteIfExists: true
    ExcludedByOwner:
      - "Attendance"

ErrorCodes:
  ACC_ERR_REPORT_REPO_UNKNOWN: "Target Repo not found in Repos.Index.md"
  ACC_ERR_REPORT_REPO_EXCLUDED: "Target Repo is owner-excluded (workflow-owned Dashboard)"
  ACC_ERR_REPORT_NO_DATA: "No activity found inside the repo"
  ACC_ERR_REPORT_SCAN_FAILED: "Scan error"
  ACC_ERR_DESCRIPTION_REQUIRED: "Description empty / missing"
---

# Report_Repo Workflow

## [0] Purpose

Workspace ke top-level **repos** ke liye BI reports + in-repo Dashboard refresh.

> **Repo = top-level code folder** under `/Aliens/` (WebOS, WebSDK, WebApp, AI, Copilot, Docs, ACLE, ATLE, Skill_ACLE, Skill_ATLE, Course_ACLE, Course_ATLE, Wiki_ACLE, Wiki_ATLE, Blog, Aliens.Inc, Cyborg, Anil.IM, AnilCyborg, WebBrand, WebDB, Workflow, Workflow_Zero, etc.)
> **Repos ≠ Projects.** Projects ke liye `Report_Project` use karo.

SSOT: `Cyborg/AnilCyborg/Ecosystem/Repos.Index.md`

### Call Signatures

```
Workflow( 'Report_Repo', 'All',    'Generate all missing reports' )
Workflow( 'Report_Repo', 'WebOS',  'Generate all missing reports' )
Workflow( 'Report_Repo', 'WebSDK', 'Generate all missing reports' )
Workflow( 'Report_Repo', 'ACLE',   'Dashboard refresh karo' )
```

---

## [1] Universal Includes

1) `Workflows/ACC/_Common/Workflow_Universal.md`
2) `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [2] Child Chain

1) `Report-Planning` — Repo filesystem + commit/activity scan
2) `Report-Code` — Report files + in-repo Dashboard.md generate/refresh
3) `Report-Documentation` — (Optional) index update

---

## [3] Target Mode Resolution (Dispatch)

```
1. Target == 'All' (exact)
   → Mode A: AllRepos
   → Iterate every repo from Ecosystem/Repos.Index.md
   → Skip Defaults.ExcludeRepos (Attendance)

2. Target matches a RepoName from Repos.Index.md
   → Mode B: SingleRepo
   → If in ExcludeRepos => STOP with ACC_ERR_REPORT_REPO_EXCLUDED

3. Otherwise
   → STOP with ACC_ERR_REPORT_REPO_UNKNOWN
```

---

## [4] Repos SSOT

`Cyborg/AnilCyborg/Ecosystem/Repos.Index.md` provides the canonical list (≈24 repos) with tier / purpose / owner.

**Owner-excluded from this workflow:**
- `Attendance` — its `Dashboard.md` is owned by `Attendance_Dashboard` workflow (do not overwrite).

---

## [5] Output Locations

### Central BI root (both modes)

```
/Aliens/Report/Repo/{RepoName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

### In-repo Dashboard (MANDATORY — both modes)

```
/Aliens/{RepoName}/Dashboard.md
```
- Create if missing; overwrite if exists (**owner-excluded repos skipped**).
- Same BI quality as the central Dashboard — tailored to the repo's domain.

---

## [6] Data Discovery

### 6.1 Filesystem scan

```powershell
Get-ChildItem -Path "C:\Aliens\{RepoName}" -Recurse -File |
  Where-Object { $_.FullName -notmatch '\\(\.git|node_modules|__pycache__|\.Alien|Report)\\' } |
  Select-Object FullName, Length, CreationTime, LastWriteTime, Extension
```

### 6.2 Metrics

- File count, total size, language mix (extension histogram)
- Daily / weekly / monthly creation + modification cadence
- Top contributors (by CyborgID if trackable via `.Alien/` logs; else by commit metadata if `.git` available — read-only)
- Largest files / longest files / recent hot files
- Test / doc / code ratio
- Growth curve (cumulative file count over time)

### 6.3 Scripts

Location: `/Aliens/Cyborg/AnilCyborg/Code/` (with header comment).
Read-only; no destructive ops on target repo.

---

## [7] Report Content Requirements

### Common sections

```
1. Header: RepoName, tier, purpose, last-updated
2. Executive scoreboard (files / size / languages / contributors / age)
3. Growth trend (xychart-beta — cumulative files over time)
4. Language mix (pie)
5. Activity heat calendar (last 12 months)
6. Top folders / hot zones
7. Largest files
8. Contributor split (with Cyborg / Manual attribution where available)
9. Risks / stagnation zones
10. Next actions (P0 / P1 / P2)
11. Glossary + end marker
```

### Period-specific reports

- Daily / Weekly / Monthly / Yearly mirror the period-scoped slice.
- Period-over-period comparisons mandatory.

### Dashboard (both central + in-repo)

- Always regenerated.
- Lifetime snapshot.
- **Central Dashboard** (`/Aliens/Report/Repo/{RepoName}/Dashboard.md`) → deep BI.
- **In-repo Dashboard** (`/Aliens/{RepoName}/Dashboard.md`) → repo-resident, same BI quality; acts as the landing page for anyone opening the repo.

---

## [8] Mode A (All) Execution Flow

```
For each RepoName in Repos.Index.md:
    If RepoName in ExcludeRepos: skip (noted in RunRecord)
    Else:
        Run Mode B logic for RepoName
Aggregate: produce /Aliens/Report/Repo/_All/Dashboard.md (cross-repo rollup)
```

Cross-repo rollup Dashboard compares all repos side-by-side (size, velocity, health).

---

## [9] Terminal Policy

Allowed ONLY for: filesystem metadata scans, size calculations, extension histograms, read-only `git log` if a repo has `.git`. **No** content generation via scripts. **No** destructive ops.

---

## [10] Errors

| Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_REPORT_REPO_UNKNOWN` | Target Repo not in Repos.Index.md | STOP |
| `ACC_ERR_REPORT_REPO_EXCLUDED` | Target Repo is owner-excluded | STOP |
| `ACC_ERR_REPORT_NO_DATA` | Repo empty / no scan data | STOP |
| `ACC_ERR_REPORT_SCAN_FAILED` | Scan error | STOP |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description missing | STOP |

---

## [11] Execution Summary

```
Workflow( 'Report_Repo', '{Target}', '{Description}' )

Step 1: Dispatch → Mode A / B (Section [3])
Step 2: Resolve repo list from Repos.Index.md
Step 3: Report-Planning (filesystem + metadata scan per repo)
Step 4: Report-Code:
        (a) Central reports under /Aliens/Report/Repo/{RepoName}/
        (b) In-repo /Aliens/{RepoName}/Dashboard.md  (skip excluded)
        (c) Mode A only: /Aliens/Report/Repo/_All/Dashboard.md rollup
Step 5: Report-Documentation (optional)
Step 6: Save RunRecord
```

---

## [12] Sibling Workflows

| Scope | Workflow |
|:---|:---|
| Single Cyborg activity | `Report` |
| ACC Planner projects | `Report_Project` |
| Alien (human) consolidated | `Report_Alien` |

---

## [13] Changelog

- **26.00.00** (2026-04-19): Initial version. Split from monolithic `Report` v26.01.00. 2 target modes (All / RepoName). Dual output: central BI + mandatory in-repo `Dashboard.md` refresh. `Attendance` owner-excluded.
