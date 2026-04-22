---
WorkflowId: Report_Project
Title: "ACC Planner Project Reports (All · Per-Cyborg · Per-Project)"
Category: System
Type: Workflow_Plural
Domain: Report
Scope: "Generate comprehensive BI reports for ACC Planner projects."

Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-19

Intent:
  Summary: >
    Yeh workflow ACC Planner (projects) ke liye BI reports generate karta hai.
    3 target modes: All projects (aggregated), per-Cyborg (only that Cyborg's assigned rows),
    per-Project (single app/engine). Missing Daily/Weekly/Monthly/Yearly/Dashboard fill hote hain.
  WhenToUse:
    - "Saare ACC Planner projects ki reports chahiye"
    - "Kisi Cyborg ke assigned projects ki reports chahiye"
    - "Ek specific project (e.g. ToDo, Teams, WebOS_Engine_PHP) ki report chahiye"
  SiblingWorkflows:
    - "Report        → Single-Cyborg activity reports"
    - "Report_Repo   → Repos BI + in-repo Dashboard"
    - "Report_Alien  → Human team-member consolidated reports"

Inputs:
  Targets:
    Modes:
      - "All                  → every project (aggregated across all Cyborgs)"
      - "{CyborgID}           → only this Cyborg's assigned planner rows (e.g. 'AC0001')"
      - "{ProjectName}        → single project (e.g. 'ToDo', 'Teams', 'WebOS_Engine_PHP')"
    Examples:
      - "All"
      - "AC0001"
      - "ToDo"
      - "WebOS_Engine_PHP"
    Validation:
      - "Exactly 1 target allowed"
      - "Target == 'All'       → Mode A"
      - "Target matches CyborgID pattern (no slashes, no @) AND exists in manifest → Mode B"
      - "Target matches a discovered AppName from /Aliens/Project/ALL/*.csv → Mode C"
      - "Unresolvable => ACC_ERR_REPORT_PROJECT_TARGET_INVALID"
  Description:
    Format: "Any human language"

Defaults:
  ReportFormat: "md"
  PlannerRoot: "/Aliens/Project/ALL/"
  ExcludeFromScan:
    - ".git"
    - "node_modules"
    - "__pycache__"

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
    - "/Aliens/Project/ALL/*.csv"
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
    ModeA_All:
      Root: "/Aliens/Report/Project/{AppName}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
    ModeB_PerCyborg:
      Root: "/Aliens/Report/Project/{CyborgID}/{AppName}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
    ModeC_PerProject:
      Root: "/Aliens/Report/Project/{AppName}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"

ErrorCodes:
  ACC_ERR_REPORT_PROJECT_TARGET_INVALID: "Target does not resolve to All / known CyborgID / known ProjectName"
  ACC_ERR_REPORT_PROJECT_CYBORG_UNKNOWN: "CyborgID Target not found in manifest"
  ACC_ERR_REPORT_PROJECT_UNKNOWN: "ProjectName Target has no matching planner in /Aliens/Project/ALL/"
  ACC_ERR_REPORT_NO_DATA: "No planner rows / activity found for the resolved scope"
  ACC_ERR_REPORT_SCAN_FAILED: "Filesystem / planner scan failed"
  ACC_ERR_DESCRIPTION_REQUIRED: "Description empty / missing"
---

# Report_Project Workflow

## [0] Purpose

`Report_Project` ACC Planner (= **Projects**) ke liye BI reports banata hai.

> **Project = ACC Planner CSV** (e.g. `ToDo_Planner.csv`). Workspace top-level code folders (WebOS/, WebSDK/ …) **projects NAHI hain** — yeh repos hain (`Report_Repo` use karo).

### Call Signatures

```
Workflow( 'Report_Project', 'All',              'Generate all missing reports' )
Workflow( 'Report_Project', 'AC0001',           'Generate all missing reports' )
Workflow( 'Report_Project', 'ToDo',             'Generate all missing reports' )
Workflow( 'Report_Project', 'WebOS_Engine_PHP', 'Generate all missing reports' )
```

---

## [1] Universal Includes

1) `Workflows/ACC/_Common/Workflow_Universal.md`
2) `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [2] Child Chain

1) `Report-Planning` — Planner + filesystem scan (scope-aware)
2) `Report-Code` — Report files generate per mode's output root
3) `Report-Documentation` — (Optional) index update

---

## [3] Target Mode Resolution (Dispatch)

First-match-wins order:

```
1. Target == 'All' (exact, case-sensitive)
   → Mode A: AllProjects
   → Skip Cyborg validation
   → AppNames discovered from /Aliens/Project/ALL/*.csv

2. Target matches CyborgID (no slash, no @) AND exists in manifest's Cyborg roster
   → Mode B: PerCyborg
   → Per-Cyborg validation (Section [4])
   → AppNames scoped to rows where assign = {CyborgID}

3. Target matches a known AppName from planners
   → Mode C: PerProject
   → Single AppName run

4. Otherwise
   → STOP with ACC_ERR_REPORT_PROJECT_TARGET_INVALID
```

---

## [4] Cyborg Validation (Mode B only)

```
1. Read /Aliens/manifest.json
2. Match Target against:
     - manifest.Developer.Username / .CyborgID / .CPUID  (current CPU)
     - manifest.Team.Cyborgs[].id                         (registered roster)
3. No match => STOP with ACC_ERR_REPORT_PROJECT_CYBORG_UNKNOWN
```

---

## [5] Project (AppName) Discovery

```
1. Scan /Aliens/Project/ALL/ for *.csv
2. Extract AppName:
     - {AppName}_Planner.csv
     - {AppName}_Planner_STAGE_{N}.csv
     - {EngineType}_Update-{N}.csv  → EngineType as AppName
3. Deduplicate
4. Mode C: fuzzy-compare Target against this list; exact match required
```

**Known AppNames (typical):** ToDo, Teams, Git, Admin, CRM, Designer, Docx, Drive, Sheet, Slide, PowerPoint, Visio, Project, WebOS_Engine_PHP, WebOS_SQL.

---

## [6] Output Locations (per mode)

### Mode A — All Projects

```
/Aliens/Report/Project/{AppName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```
Aggregated across **all Cyborgs**.

### Mode B — Per-Cyborg

```
/Aliens/Report/Project/{CyborgID}/{AppName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

Data source priority:
1. `/Aliens/Project/{CyborgID}/*.csv` (if folder exists)
2. Else filter `/Aliens/Project/ALL/*.csv` rows WHERE `assign = {CyborgID}`

### Mode C — Per-Project

```
/Aliens/Report/Project/{AppName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```
Single AppName, aggregated across all Cyborgs (same shape as Mode A, single subtree).

---

## [7] Report Content Requirements

### Common sections (all 5 report types)

```
1. Header: scope label (All / CyborgID+AppName / AppName) + period + run date
2. Planner status summary (total rows, pending / completed / failed, %)
3. Stage breakdown (if STAGE_* planners exist)
4. Per-Cyborg contribution (Mode A & C); plus single-Cyborg scoped (Mode B)
5. Throughput trend (Mermaid xychart-beta)
6. Status composition (Mermaid pie)
7. Top-N failing rows (if any)
8. Delivery verification (planner code_path vs filesystem cross-check)
9. Risks / gaps
10. Next actions (P0 / P1 / P2)
11. Glossary + End marker
```

**Dashboard** = always regenerated; lifetime picture.
**Daily / Weekly / Monthly / Yearly** = planner throughput + delivery cadence within their periods.

---

## [8] Planner Data Model (Key Fields)

| Column | Meaning |
|:---|:---|
| `ID` | Row identifier |
| `name` | Unit name |
| `worklflow` | Target workflow (e.g. `WebApp_Screen`, `WebSDK_Module_API`) |
| `description` | Unit prompt / instructions |
| `status` | 0 = pending, 1 = complete, 2 = failed |
| `assign` | CyborgID assigned |
| `planning_path`, `code_path`, `doc_path` | Output paths |
| `planning`, `code`, `doc` | Done flags |
| `parent`, `parent_type` | Hierarchy |
| `depends_on` | Dependency chain |

Row 0 is typically `__PlannerMeta__` — skip from counts.

---

## [9] Missing-Planner Detection (Advisory)

Agar filesystem me kisi AppName ke files hain lekin planner missing hai:
- Report me **"Missing Planner" flag** dikhao.
- Optionally `{AppName}_Planner.csv` retro-create karo `/Aliens/Project/ALL/` me (status=1 for done rows + `__PlannerMeta__` row) — **sirf agar permissions allow** karein (aur existing planner overwrite forbidden).

---

## [10] Terminal Policy

Allowed ONLY for:
- Planner CSV scanning / counting / row-state aggregation
- Filesystem cross-check of `code_path` existence

Not allowed: AI-generated content via scripts, destructive ops.
Scripts location: `/Aliens/Cyborg/AnilCyborg/Code/` with header comment.

---

## [11] Errors

| Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_REPORT_PROJECT_TARGET_INVALID` | Target matches no mode | STOP |
| `ACC_ERR_REPORT_PROJECT_CYBORG_UNKNOWN` | Mode B CyborgID not in manifest | STOP |
| `ACC_ERR_REPORT_PROJECT_UNKNOWN` | Mode C AppName not found | STOP |
| `ACC_ERR_REPORT_NO_DATA` | No rows / activity in scope | STOP |
| `ACC_ERR_REPORT_SCAN_FAILED` | Scan error | STOP |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description missing | STOP |

---

## [12] Execution Summary

```
Workflow( 'Report_Project', '{Target}', '{Description}' )

Step 1: Dispatch → Mode A / B / C (Section [3])
Step 2: Validate (Cyborg check for Mode B; AppName check for Mode C)
Step 3: Report-Planning (planner + filesystem scan)
Step 4: Report-Code (generate missing reports per mode root)
Step 5: Report-Documentation (optional)
Step 6: Save RunRecord
```

---

## [13] Sibling Workflows

| Scope | Workflow |
|:---|:---|
| Single Cyborg activity | `Report` |
| Repos BI + in-repo Dashboard | `Report_Repo` |
| Alien (human) consolidated | `Report_Alien` |

---

## [14] Changelog

- **26.00.00** (2026-04-19): Initial version. Split from monolithic `Report` v26.01.00. 3 target modes (All / CyborgID / ProjectName). Full Daily/Weekly/Monthly/Yearly/Dashboard stack per mode's root.
