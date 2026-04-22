---
WorkflowId: Report
Title: "Cyborg Activity Report Generator (Daily · Weekly · Monthly · Yearly · Dashboard)"
Category: System
Type: Workflow_Plural
Domain: Report
Scope: "Generate comprehensive BI reports for a SINGLE Cyborg — only this CPU's activity."

Status: Stable
Version: 26.02.00
LastUpdated: 2026-04-19

Intent:
  Summary: >
    Yeh workflow ek specific Cyborg ke liye COMPLETE activity reports generate karta hai.
    Reports me sirf usi Cyborg ka kaam include hota hai — na Projects, na Repos, na Aliens.
    Missing Daily/Weekly/Monthly/Yearly/Dashboard reports automatically detect + fill hote hain.
    Output ek hi jagah: /Aliens/Report/Cyborg/{CyborgID}/
  WhenToUse:
    - "Kisi Cyborg ki activity reports generate karni hon"
    - "Missing daily/weekly/monthly/yearly reports detect + fill karni hon"
    - "Dashboard refresh karna ho (full history)"
  SiblingWorkflows:
    - "Report_Project  → ACC Planner (project-level) reports"
    - "Report_Repo     → Workspace repos BI + in-repo Dashboard.md"
    - "Report_Alien    → Human team-member (Alien) consolidated reports"

Inputs:
  Targets:
    Format: "single Cyborg ID (must match manifest Developer.Username or registered CyborgID)"
    Examples:
      - "AC0000"
      - "AC0001"
      - "C1072"
    Validation:
      - "Exactly 1 Cyborg ID allowed"
      - "Cyborg ID MUST match /Aliens/manifest.json → Developer.Username (or .CyborgID / .CPUID)"
      - "Mismatch => STOP with ACC_ERR_REPORT_CPU_MISMATCH"
      - "Project / Repo / Alien targets yahan allowed NAHI — use sibling workflows"
  Description:
    Format: "Any human language"

Defaults:
  ReportFormat: "md"
  ScanMethod: "CreationTime"
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
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
  Data:
    - "/Aliens/manifest.json"
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
    Root: "/Aliens/Report/Cyborg/{CyborgID}/"
    Files:
      - "Daily_Report_YYYY-MM-DD.md"
      - "Weekly_Report_YYYY-WNN.md"
      - "Monthly_Report_YYYY-MM.md"
      - "Yearly_Report_YYYY.md"
      - "Dashboard.md"

ErrorCodes:
  ACC_ERR_REPORT_CPU_MISMATCH: "Target CyborgID does not match manifest Developer.Username"
  ACC_ERR_REPORT_NO_DATA: "No working days found for this CyborgID"
  ACC_ERR_REPORT_SCAN_FAILED: "Filesystem scan failed"
  ACC_ERR_DESCRIPTION_REQUIRED: "Description empty / missing"
---

# Report Workflow (Cyborg-only)

## [0] Purpose

`Report` workflow sirf **ek Cyborg** ki activity reports banata hai — **only that CPU's work**.
Projects, repos, aur Aliens ke liye **alag sibling workflows** hain.

### Call Signature

```
Workflow( 'Report', '{CyborgID}', '{Description}' )
```

### Examples

```
Workflow( 'Report', 'AC0000',  'Generate all missing reports' )
Workflow( 'Report', 'AC0001',  'Generate all missing reports' )
Workflow( 'Report', 'C1072',   'February monthly report generate karo' )
```

### Dual Purpose — BI + Cyborg Handoff

1. **Business Intelligence:** Speed, quality, volume, velocity.
2. **Handoff:** `VSCode_Chat` workflow reports ka use karke naya Cyborg par context load karta hai — zero context loss.

---

## [1] Universal Includes

1) `Workflows/ACC/_Common/Workflow_Universal.md`
2) `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [2] Child Chain

1) `Report-Planning` — Data discovery + filesystem scan
2) `Report-Code` — Report files generate (Daily / Weekly / Monthly / Yearly / Dashboard)
3) `Report-Documentation` — (Optional) index update

---

## [3] Cyborg ID Validation (STRICT — Gate #1)

```
1. Read /Aliens/manifest.json
2. Extract Developer.Username (primary), Developer.CyborgID, Developer.CPUID
3. Case-sensitive compare with Target
4. Mismatch => STOP with ACC_ERR_REPORT_CPU_MISMATCH
```

Rules:
- Exactly 1 CyborgID in Target.
- No `Project` / `Repo` / email-style targets — use sibling workflows.

---

## [3.1] Terminology Rule

Reports me **"Cyborg ID"** use hoga; "CPU ID" / "Developer ID" forbidden.
Context: `Ecosystem/Members.Aliens_vs_Cyborgs.md`.

---

## [4] Output Location

```
/Aliens/Report/Cyborg/{CyborgID}/
├── Daily_Report_YYYY-MM-DD.md     (per working day)
├── Weekly_Report_YYYY-WNN.md      (per ISO week)
├── Monthly_Report_YYYY-MM.md      (per calendar month)
├── Yearly_Report_YYYY.md          (per calendar year)
└── Dashboard.md                   (always-latest lifetime BI dashboard)
```

Content: Pura kaam jo is Cyborg ne kiya — across ALL folders/repos. **No** Project / Repo / Alien sub-reports.

---

## [5] Report Types (Content Requirements)

### 5.1 Daily

```
1. Header: Date, Day, CyborgID, Workspace
2. Day summary (files, lines, size, workstreams, outcomes)
3. Work performed (per-workstream breakdown + deliverables + metrics)
4. Folder activity table
5. Quality notes
6. Mermaid charts (file distribution pie; activity timeline)
7. Next-day outlook (optional)
```

### 5.2 Weekly

```
1. Header: Week#, date range, CyborgID
2. Week summary (working days, files, size, projects active)
3. Daily breakdown table
4. Highlights
5. Mermaid: daily bar, workstream pie
6. Velocity metrics
7. Week-over-week comparison
```

### 5.3 Monthly

```
1. Header: Month + year + CyborgID
2. Summary (days, files, size, projects, milestones)
3. Weekly breakdown table
4. Activity calendar (visual grid)
5. Project progress
6. Mermaid: weekly bar, project pie, cumulative line
7. Month-over-month comparison
8. Activity heatmap
```

### 5.4 Yearly

```
1. Header + status
2. Year summary
3. Monthly breakdown table
4. Quarterly analysis (Q1..Q4)
5. Project portfolio (Gantt)
6. Engineering metrics (languages, code:docs ratio, velocity)
7. Mermaid: monthly, quarterly, YoY
8. Highlights + strategic insights
```

### 5.5 Dashboard (ALWAYS regenerate)

```
1. Executive scoreboard
2. KPIs
3. Full timeline Gantt
4. All-months calendar
5. Daily production analytics
6. Phase / project breakdowns
7. Engineering artifacts inventory
8. Quality scorecard
9. Weekly + monthly summaries
10. Velocity + peak records
11. Report index
12. Grand totals
13. Strategic insights
```

---

## [6] Data Discovery

### 6.1 Scan

```powershell
Get-ChildItem -Path 'C:\Aliens' -Recurse -File |
  Where-Object { $_.FullName -notmatch '\\(\.git|node_modules|__pycache__|\.Alien|Report)\\' } |
  Group-Object {$_.CreationTime.ToString('yyyy-MM-dd')}, {($_.FullName -replace '^C:\\Aliens\\','') -replace '\\.*$',''} |
  Select-Object Count, Name | Sort-Object Name
```

### 6.2 Scan script location

`/Aliens/Cyborg/AnilCyborg/Code/` — each script has header comment (purpose / workflow / date).

### 6.3 Output handling

- Group by date → working days.
- Filter bundled assets (old-timestamp framework files).
- Day-of-week via `(Get-Date).DayOfWeek`.

---

## [7] Missing Report Detection

```
1. Scan → working days list
2. List existing reports
3. Queue missing Daily / Weekly / Monthly / Yearly
4. Dashboard always regenerated
```

Generation order: Daily → Weekly → Monthly → Yearly → Dashboard (chronological).

---

## [8] Quality Standards

- Clean markdown, tables, Mermaid charts.
- Date format `YYYY-MM-DD` in filenames.
- Cross-links between reports.
- Enterprise BI depth — every metric backed by data.
- **No** data from other Cyborgs.

---

## [9] Terminal Policy

Allowed only for scanning / counts / sizes / date-verification / existing-report listing.
Not allowed: generating report content via scripts, bulk file creation, destructive ops.

---

## [10] Errors

| Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_REPORT_CPU_MISMATCH` | Target ≠ manifest | STOP |
| `ACC_ERR_REPORT_NO_DATA` | No working days found | STOP |
| `ACC_ERR_REPORT_SCAN_FAILED` | Scan error | STOP |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description missing | STOP |

---

## [11] Execution Summary

```
Workflow( 'Report', '{CyborgID}', '{Description}' )

Step 1: Gate #1 — CyborgID validation
Step 2: Report-Planning
Step 3: Report-Code
Step 4: Report-Documentation (optional)
Step 5: Save RunRecord
```

---

## [12] Sibling Workflows

| Scope                                 | Workflow         |
|:--------------------------------------|:-----------------|
| ACC Planner projects                  | `Report_Project` |
| Workspace repos + in-repo Dashboard   | `Report_Repo`    |
| Alien (human team member) reports     | `Report_Alien`   |

---

## [13] Changelog

- **26.02.00** (2026-04-19): **SPLIT** — Project / Repo / Alien scopes moved out into `Report_Project`, `Report_Repo`, `Report_Alien` sibling workflows. `Report` is now Cyborg-only.
- 26.01.00 (2026-04-19): Interim unified 5-modes version (superseded).
- 26.00.02 (2026-03-12): Project concept redefined = ACC Planner CSV.
- 26.00.01 (2026-03-12): `/Aliens/Project/` clarified as planner storage.
- 26.00.00 (2026-03-12): Initial Report workflow.
---
WorkflowId: Report
Title: "Aliens Activity Report Generator (Cyborg · Project · Repo · Alien — Daily · Weekly · Monthly · Yearly · Dashboard)"
Category: System
Type: Workflow_Plural
Domain: Report
Scope: "Generate comprehensive BI reports. Target determines scope: Cyborg, Project (all / per-Cyborg), Repo, or Alien (human team member)."

Status: Stable
Version: 26.01.00
LastUpdated: 2026-04-19

Intent:
  Summary: >
    Yeh workflow Aliens ecosystem ki COMPLETE BI reports generate karta hai. Target string ke
    format se decide hota hai ke reports ka SCOPE kya hoga — Cyborg (current CPU), Project (ACC
    Planners), Repo (workspace repositories), ya Alien (human team member jiske under multiple
    Cyborgs kaam karte hain). Har mode me missing Daily/Weekly/Monthly/Yearly/Dashboard reports
    detect aur fill hote hain.
  WhenToUse:
    - "Cyborg ki activity reports (only this CPU) generate karni hon"
    - "Saare ya specific-Cyborg-ke ACC Planner projects ki reports banani hon"
    - "Har repo ki repo-level reports + in-repo Dashboard.md update karna ho"
    - "Kisi human team member (Alien) ki consolidated reports (manual + Cyborg merged) banani hon"
    - "Periodic reporting cycle ke end me (din/week/month/year close)"

Inputs:
  Targets:
    Format: "One of 5 modes — see Section [3.4] Target Mode Resolution"
    Modes:
      - "{CyborgID}                  → Only this Cyborg's activity (e.g., 'AC0000', 'C1072')"
      - "Project                      → ALL ACC Planner projects, aggregated across all Cyborgs"
      - "Project/{CyborgID}           → Only this Cyborg's assigned projects (e.g., 'Project/AC0001')"
      - "Repo                         → ALL workspace repos + update each repo's Dashboard.md"
      - "{alien-email}                → Alien (human) reports (e.g., 'himanshu@aliens.company')"
    Examples:
      - "AC0000"
      - "C1072"
      - "Project"
      - "Project/AC0001"
      - "Repo"
      - "himanshu@aliens.company"
      - "himanshu@aliens.id"
    Validation:
      - "Exactly 1 target allowed (comma-separated multiple targets NOT allowed)"
      - "Cyborg-mode Target MUST match /Aliens/manifest.json → Developer.Username"
      - "Project/{CyborgID} mode → CyborgID part must match manifest"
      - "Alien email mode → must end with @aliens.company or @aliens.id"
      - "Mode detection per Section [3.4]; unresolvable target => ACC_ERR_REPORT_TARGET_INVALID"
  Description:
    Format: "Any human language"
    Examples:
      - "Generate all missing reports for this CPU"
      - "Generate all missing reports"
      - "Dashboard refresh karo with latest data"

Defaults:
  Version: "latest"
  VersionFallback: "PHP.26.00.00"
  ReportFormat: "md"
  ScanMethod: "CreationTime"
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
  Notes: >
    Reports are read-only documentation artifacts — no prod code or DB touched.
    Terminal usage allowed for filesystem scanning (data gathering only).

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Plural.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
  Data:
    - "/Aliens/manifest.json"

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
    CyborgMode:
      Trigger: "Target = {CyborgID}"
      Root: "/Aliens/Report/Cyborg/{CyborgID}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
      Note: "ONLY this Cyborg's activity. NO auto-generation of Project reports (breaking change vs v26.00.x)."
    ProjectAllMode:
      Trigger: "Target = 'Project' (literal)"
      Root: "/Aliens/Report/Project/{AppName}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
      Note: "All AppNames from /Aliens/Project/ALL/ planners. Data aggregated across all Cyborgs."
    ProjectPerCyborgMode:
      Trigger: "Target = 'Project/{CyborgID}'"
      Root: "/Aliens/Report/Project/{CyborgID}/{AppName}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
      Note: "Only planner rows where assign = {CyborgID}. Uses /Aliens/Project/{CyborgID}/*.csv as SSOT if present, else filters ALL/."
    RepoMode:
      Trigger: "Target = 'Repo' (literal)"
      Root: "/Aliens/Report/Repo/{RepoName}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
      ExtraAction:
        - "ALSO create/update /Aliens/{RepoName}/Dashboard.md (in-repo BI dashboard)"
      Note: "RepoName source = /Aliens/Cyborg/AnilCyborg/Ecosystem/Repos.Index.md (SSOT)."
    AlienMode:
      Trigger: "Target = '{alias}@aliens.company' or '{alias}@aliens.id'"
      Root: "/Aliens/Report/Alien/{alias}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
      Note: "Combines manual Alien activity + merged activity of all Cyborgs under this Alien's control. Each merged entry flagged with source CyborgID — never presented as manual work."

ErrorCodes:
  ACC_ERR_REPORT_TARGET_INVALID: "Target does not match any of the 5 supported modes (Cyborg / Project / Project/{CyborgID} / Repo / Alien)"
  ACC_ERR_REPORT_CPU_MISMATCH: "Cyborg-mode Target does not match manifest Developer.Username"
  ACC_ERR_REPORT_ALIEN_UNKNOWN: "Alien email does not resolve to a known team member in /Aliens/manifest.json Team.Aliens[]"
  ACC_ERR_REPORT_NO_DATA: "No working days / planner rows / repo activity found for the specified target"
  ACC_ERR_REPORT_SCAN_FAILED: "Filesystem scan failed or returned no results"
---

# Report Workflow (Plural)

## [0] Purpose (Hinglish)

`Report` workflow ka goal: Aliens ecosystem ki **comprehensive BI reports** generate karna. Target string
ke format se decide hota hai ke reports ka SCOPE kya hoga — Cyborg (current CPU), Project (ACC Planners),
Repo (workspace repositories), ya Alien (human team member).

**v26.01.00 Breaking Change:** Cyborg-mode me ab **Project reports automatically generate NAHI hote**.
Project reports ke liye alag se `Workflow('Report', 'Project', ...)` ya `Workflow('Report', 'Project/{CyborgID}', ...)`
run karna hoga. Yeh concern-separation improve karta hai aur accidental scope-creep prevent karta hai.

### Dual Purpose — BI + Cyborg Handoff

Reports ke **2 core purposes** hain:

**1) Business Intelligence (BI):**
- Kaam ki speed, quality, volume track karna
- Project progress monitor karna
- Velocity trends aur patterns dekhna
- Strategic insights aur decision support

**2) Cyborg Handoff / Migration:**
- Agar ek Cyborg (e.g. C1001) band karna ho aur kaam dusre Cyborg (e.g. C1002) ko transfer karna ho
- C1001 ki reports use karke C1002 me `VSCode_Chat` workflow run kiya jaata hai
- `Workflow('VSCode_Chat', '/Aliens/Report/Cyborg/C1001', 'Context load karo')`
- Is se C1002 me agent ko C1001 ka poora context mil jaata hai — kaam wahin se resume hota hai
- **Zero context loss** — reports hi context transfer ka medium hain

> Reports ki quality directly Cyborg handoff quality ko impact karti hai.
> Better reports = Better handoff = Zero re-work on new Cyborg.

See: `Workflows/ACC/VSCode/VSCode_Chat.md` for the handoff consumer workflow.

### Call Signatures (5 Target Modes)

```
# Mode 1 — Cyborg (only this CPU's activity; NO auto-Project)
Workflow( 'Report', 'AC0000',                 'Generate all missing reports for this CPU' )

# Mode 2 — All Projects (aggregated across all Cyborgs)
Workflow( 'Report', 'Project',                'Generate all missing reports' )

# Mode 3 — Projects for a specific Cyborg only
Workflow( 'Report', 'Project/AC0001',         'Generate all missing reports' )

# Mode 4 — All Repos (repo-level reports + update each repo's Dashboard.md)
Workflow( 'Report', 'Repo',                   'Generate all missing reports' )

# Mode 5 — Alien (human team member consolidated reports; Cyborgs merged with source flag)
Workflow( 'Report', 'himanshu@aliens.company','Generate all missing reports' )
```

See Section **[3.4] Target Mode Resolution** for dispatch logic.

---

## [1] Universal Includes (Mandatory Order)

1) `require_once Workflows/ACC/_Common/Workflow_Universal.md`
2) `require_once Workflows/ACC/_Common/Workflow_Plural.md`

---

## [2] Child Chain (Execution Order)

1) `Report-Planning` — Data discovery aur scan
2) `Report-Code` — Report files generate karna (Daily/Weekly/Monthly/Yearly/Dashboard)
3) `Report-Documentation` — (Optional) Summary/index update

---

## [3] Cyborg ID Validation (STRICT — Gate #1, Cyborg-scope modes only)

### 3.1 Applicability

This gate applies to modes that carry a CyborgID:
- **Mode 1** — `{CyborgID}`
- **Mode 3** — `Project/{CyborgID}` (validates the CyborgID segment)

For **Mode 2 (Project)**, **Mode 4 (Repo)**, and **Mode 5 (Alien)** — this gate does NOT apply; see Section [3.4].

### 3.2 Manifest Load

```
1. Read /Aliens/manifest.json
2. Extract Developer.Username (and Developer.CyborgID if present)
3. Compare with Target CyborgID segment (case-sensitive)
4. Mismatch => STOP with ACC_ERR_REPORT_CPU_MISMATCH
```

### 3.3 Cyborg ID Resolution Order

```
1. manifest.Developer.Username (primary)
2. manifest.Developer.CyborgID (if exists)
3. manifest.Developer.CPUID (if exists)
4. None match => STOP
```

---

## [3.4] Target Mode Resolution (Dispatch Logic) — NEW in v26.01.00

Target string ko resolve karne ka order (first match wins):

```
1. Target == 'Project' (exact, case-sensitive)
     → Mode 2: ProjectAll
     → Skip CyborgID validation
     → Execute Section [9.X] All-Projects flow

2. Target == 'Repo' (exact, case-sensitive)
     → Mode 4: Repo
     → Skip CyborgID validation
     → Execute Section [14] Repo flow

3. Target matches /^Project\/(.+)$/ (e.g., 'Project/AC0001')
     → Mode 3: ProjectPerCyborg
     → Extract CyborgID = capture group
     → Run Gate #1 (Section [3]) on extracted CyborgID
     → Execute Section [9.Y] Per-Cyborg-Projects flow

4. Target matches /^[^@\s]+@aliens\.(company|id)$/
     → Mode 5: Alien
     → Skip CyborgID validation; run Alien validation (Section [15.2])
     → Execute Section [15] Alien flow

5. Target matches CyborgID pattern (no slashes, no @)
     → Mode 1: Cyborg
     → Run Gate #1 (Section [3])
     → Execute Sections [4.1] + [5]–[8] (Cyborg-only flow)
     → DO NOT auto-generate any Project reports (v26.01.00 behavior)

6. Otherwise
     → STOP with ACC_ERR_REPORT_TARGET_INVALID
```

**Rule:** Exactly ONE mode executes per workflow call. Modes do NOT cascade or chain automatically.

---

## [3.1] Terminology Rule (STRICT — NON-NEGOTIABLE)

Reports me hamesha **"Cyborg ID"** term use hoga.

- "CPU ID" → **forbidden** (reports me use mat karo)
- "Developer ID" → **forbidden** (reports me use mat karo)
- "CPU / Developer ID" → **forbidden**
- **Sirf "Cyborg ID"** allowed hai

Yeh rule apply hota hai:
- Report headers/footers
- Tables (label columns)
- Body text
- Mermaid chart titles
- Dashboard scoreboard
- Footer attribution lines

Background: CPU ID, Developer ID, aur Cyborg ID actual me ek hi cheez hai — lekin branding/consistency ke liye reports me hamesha "Cyborg ID" use hoga.

---

## [4] Report Output Locations (SSOT — All 5 Modes)

### 4.1 Mode 1 — Cyborg Reports (CPU-only; NO Project auto-gen)

```
/Aliens/Report/Cyborg/{CyborgID}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

**Content:** Pura kaam jo is Cyborg ne kiya — across ALL folders/repos. **No** project/planner/repo sub-reports.

> **Breaking change vs v26.00.x:** Pehle Cyborg mode automatically `/Report/Project/{AppName}/` bhi generate karta tha. Ab nahi karta. Alag workflow call chahiye.

### 4.2 Mode 2 — All Projects (ACC Planner-wide)

```
/Aliens/Report/Project/{AppName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

**AppName** extraction rules: see Section [4.6].

**Content:** Us app/engine ki planner status aggregated across ALL Cyborgs — total rows, pending/completed/failed,
stage breakdown, per-Cyborg contribution, delivery verification. Daily/Weekly/Monthly/Yearly reports capture
day-by-day throughput of that planner.

### 4.3 Mode 3 — Projects for a Specific Cyborg

```
/Aliens/Report/Project/{CyborgID}/{AppName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

**Data source priority:**

```
1. /Aliens/Project/{CyborgID}/*.csv  (if folder exists — this Cyborg's assigned subset)
2. Else: filter rows from /Aliens/Project/ALL/*.csv WHERE assign = {CyborgID}
```

**Content:** Only rows assigned to this Cyborg. Same sub-structure as Mode 2 but scoped.

### 4.4 Mode 4 — Repos (all workspace repos)

**Part A — Repo-scoped reports folder:**

```
/Aliens/Report/Repo/{RepoName}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

**Part B — In-repo Dashboard (MANDATORY extra action):**

```
/Aliens/{RepoName}/Dashboard.md     ← create if missing, update if present
```

**RepoName SSOT:** `/Aliens/Cyborg/AnilCyborg/Ecosystem/Repos.Index.md`

**Exclusions:** `Attendance/Dashboard.md` is owned by the `Attendance_Dashboard` workflow and MUST NOT be overwritten.
Workflow-owned in-repo dashboards are listed in Section [14.4].

### 4.5 Mode 5 — Alien Reports (human team member)

```
/Aliens/Report/Alien/{alias}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

**`{alias}`** = local-part of email. `himanshu@aliens.company` → alias = `himanshu`.

**Content:** Consolidated BI of:
- **Manual activity** by the Alien (human team member) — e.g., reviews, approvals, manual commits, decisions recorded in `/Aliens/.Alien/{alias}/`.
- **Merged Cyborg activity** — reports of all Cyborgs under this Alien's control (AlienCyborg instances: typically up to 4 per Alien). Each merged entry **MUST be flagged** with `SourceCyborgID` — never presented as manual work.

**Alien → Cyborgs resolution:** `/Aliens/manifest.json` → `Team.Aliens[]` → find entry with matching email → read `ControlsCyborgs[]` list.

### 4.6 AppName (ProjectID) Discovery Rules

For Mode 2 and Mode 3:

```
1. Scan /Aliens/Project/ALL/ for *.csv files
2. Extract AppName from each filename:
   - {AppName}_Planner.csv                 → AppName
   - {AppName}_Planner_STAGE_{N}.csv       → AppName (dedupe across stages)
   - {EngineType}_Update-{N}.csv           → EngineType as AppName
3. Deduplicate AppNames
4. Each unique AppName → its own folder under the mode's root
5. FORBIDDEN: code domain folders (WebOS/, WebSDK/ etc.) se ProjectID derive karna
```

> **FORBIDDEN:** Workspace top-level folders ko "projects" samajhna. Code domains are repos (Mode 4), not projects.

---

## [5] Report Types (Complete Definition)

### 5.1 Daily Report

**File:** `Daily_Report_YYYY-MM-DD.md`
**Scope:** Single working day
**Content Requirements:**

```
1. Header: Date, Day Name, Cyborg ID, Workspace
2. Day Summary Table:
   - Total files created/modified
   - Total lines written (if measurable)
   - Total size (MB/GB)
   - Primary workstreams
   - Key outcomes
3. Work Performed (detailed):
   - Per-workstream breakdown
   - Files created/modified lists
   - Key deliverables with metrics
4. Folder Activity Breakdown Table:
   - Top-level folder → file count → key files
5. Quality Notes:
   - Any failures/issues
   - Quality gates passed
6. Mermaid Charts (where applicable):
   - File distribution pie chart
   - Activity timeline
7. Next Day Outlook (optional)
```

### 5.2 Weekly Report

**File:** `Weekly_Report_YYYY-WNN.md` (ISO week number)
**Scope:** Monday–Sunday of that ISO week
**Content Requirements:**

```
1. Header: Week number, Date range, Cyborg ID
2. Week Summary:
   - Working days in this week
   - Total files created
   - Total size
   - Projects active
3. Daily Breakdown Table:
   - Day → Files → Size → Primary Work → Key Outcome
4. Week Highlights:
   - Top achievements
   - Records broken
   - Milestones reached
5. Mermaid Charts:
   - Daily files bar chart
   - Workstream distribution pie
6. Velocity Metrics:
   - Avg files/day
   - Peak day
   - Consistency score
7. Week-over-Week Comparison (if previous week exists)
```

### 5.3 Monthly Report

**File:** `Monthly_Report_YYYY-MM.md`
**Scope:** Full calendar month
**Content Requirements:**

```
1. Header: Month name, Year, Cyborg ID
2. Month Summary:
   - Working days
   - Total files
   - Total size
   - Projects active
   - Key milestones
3. Weekly Breakdown Table:
   - Week → Days → Files → Size → Primary Work
4. Daily Activity Calendar (visual grid)
5. Project Progress:
   - Per-project status
   - Completion percentages
   - ETA updates
6. Mermaid Charts:
   - Weekly files bar chart
   - Project distribution pie
   - Cumulative progress line
7. Month Highlights:
   - Top achievements
   - Records
   - Challenges
8. Month-over-Month Comparison (if previous month exists)
9. Activity Heatmap (calendar grid showing intensity per day)
```

### 5.4 Yearly Report

**File:** `Yearly_Report_YYYY.md`
**Scope:** Full calendar year
**Content Requirements:**

```
1. Header: Year, Cyborg ID, Overall Status
2. Year Summary:
   - Total working days/months
   - Total files
   - Total size
   - Projects completed/active
3. Monthly Breakdown Table:
   - Month → Days → Files → Size → Projects → Key Milestone
4. Quarterly Analysis:
   - Q1/Q2/Q3/Q4 summaries
5. Project Portfolio:
   - All projects worked on
   - Status/completion per project
   - Timeline (Gantt)
6. Engineering Metrics:
   - Languages/frameworks used
   - Code vs docs ratio
   - Velocity trends
7. Mermaid Charts:
   - Monthly files bar chart
   - Quarterly distribution
   - YoY comparison (if exists)
8. Year Highlights & Records
9. Strategic Insights
```

### 5.5 Dashboard

**File:** `Dashboard.md`
**Scope:** ALL-TIME aggregated BI dashboard
**Content Requirements:**

```
1.  Executive Summary (scoreboard)
2.  Key Performance Indicators (KPIs)
3.  Full Activity Timeline (Gantt)
4.  Activity Calendar (all months)
5.  Daily Production Analytics (master table + charts)
6.  Phase/Project Breakdowns (detailed sections)
7.  Engineering Artifacts Inventory
8.  Quality Scorecard
9.  Weekly & Monthly Summaries
10. Velocity & Peak Records
11. Report Index (links to all reports)
12. Grand Totals (lifetime statistics)
13. Strategic Insights
```

**Dashboard is ALWAYS regenerated** (not incremental update) — it reflects the latest complete picture.

---

## [6] Data Discovery (Filesystem Scan)

### 6.1 Scan Method

Report workflow uses **PowerShell filesystem scanning** to discover activity:

```powershell
# Single-pass scan: group all files by CreationTime date and top-level folder
Get-ChildItem -Path 'C:\Aliens' -Recurse -File `
    -Exclude '.git','node_modules','__pycache__' |
    Where-Object { $_.FullName -notmatch '\\(\.git|node_modules|__pycache__|\.Alien|Report)\\' } |
    Group-Object {$_.CreationTime.ToString('yyyy-MM-dd')}, {($_.FullName -replace '^C:\\Aliens\\','') -replace '\\.*$',''} |
    Select-Object Count, Name |
    Sort-Object Name
```

### 6.2 Scan Script Location

**MANDATORY:** Any scan/utility scripts MUST be created in:

```
/Aliens/Cyborg/AnilCyborg/Code/
```

**NEVER** in workspace root (`/Aliens/`) or any other location.

Each script file MUST include a header comment explaining:
- What the file does
- Which workflow uses it
- When it was created
- Why it exists

### 6.3 Scan Output Interpretation

```
1. Group by date → identify working days
2. Filter out bundled assets (DartApp fonts with old timestamps like 2011/2019)
3. For each working day:
   a. Get file counts per top-level folder
   b. Get subfolder breakdown (2 levels deep)
   c. Identify sample files for context
4. Map dates to day-of-week names using PowerShell:
   (Get-Date "YYYY-MM-DD").DayOfWeek
```

### 6.4 Bundled Asset Detection

Files with CreationTime in years significantly before workspace creation (e.g., 2011, 2019) that appear inside framework/runtime folders (DartApp, node_modules) should be **excluded** from daily report counts — they are bundled assets with preserved timestamps, not actual developer work.

Detection heuristic:
- If date < workspace first working day AND folder = DartApp/node_modules → exclude
- Log excluded counts in Dashboard for transparency

---

## [7] Missing Report Detection

### 7.1 Algorithm

```
1. Scan filesystem → get all working days
2. List existing reports in /Aliens/Report/Cyborg/{CyborgID}/
3. For each working day:
   - Check if Daily_Report_YYYY-MM-DD.md exists
   - If missing → add to generation queue
4. For each ISO week with at least 1 working day:
   - Check if Weekly_Report_YYYY-WNN.md exists
   - If missing → add to generation queue
5. For each month with at least 1 working day:
   - Check if Monthly_Report_YYYY-MM.md exists
   - If missing → add to generation queue
6. For each year with at least 1 working day:
   - Check if Yearly_Report_YYYY.md exists
   - If missing → add to generation queue
7. Dashboard.md → ALWAYS regenerate (overwrite)
```

### 7.2 Generation Order

```
1. Daily Reports (chronological order, oldest first)
2. Weekly Reports (chronological)
3. Monthly Reports (chronological)
4. Yearly Reports (chronological)
5. Dashboard (last, aggregates everything)
```

---

## [8] Report Quality Standards

### 8.1 Formatting Rules

- Clean markdown, easy to read
- Tables for structured data
- Mermaid charts for visual analytics (bar, pie, line, gantt)
- Emoji section headers for visual navigation
- ASCII art scoreboard for executive summary
- Consistent date formats: `YYYY-MM-DD` in filenames, human-readable in content
- Links between reports (cross-referencing)

### 8.2 Data Accuracy

- All file counts from actual filesystem scan (no guessing)
- Day names verified via PowerShell `(Get-Date).DayOfWeek`
- Size calculations from actual file sizes
- No rounding unless explicitly stated
- No data from other Cyborgs

### 8.3 Content Depth

- Enterprise-grade BI quality
- Every metric backed by data
- Comparative analysis (day-over-day, week-over-week, month-over-month)
- Trend lines and forecasting where data permits
- Strategic insights (not just numbers)

---

## [9] Project-Level Report Rules (ACC Planner-Based)

**FUNDAMENTAL RULE:** "Project" = ACC Planner CSV file. "Project" ≠ code domain folders.
WebOS, WebSDK, WebApp, Course_ACLE etc. code domains hain — yeh "projects" NAHI hain.
A "project" us APP/ENGINE ki PLANNING hai jo `/Aliens/Project/ALL/` me CSV ke tor par tracked hai.

### 9.1 Project = ACC Planner (SSOT)

"Project" ka ek hi matlab hai Aliens ecosystem me:

```
Project = ACC Planner CSV file in /Aliens/Project/ALL/

Examples:
  ToDo_Planner.csv              → Project "ToDo"
  Teams_Planner_STAGE_1.csv     → Project "Teams"
  WebOS_Engine_PHP_Update-5.csv → Project "WebOS_Engine_PHP"
  Admin_Planner_STAGE_1.csv     → Project "Admin"
  CRM_Planner_STAGE_1.csv       → Project "CRM"
  Git_Planner.csv               → Project "Git"
```

> **FORBIDDEN:** Workspace top-level folders (WebOS/, WebSDK/, WebApp/, Course_ACLE/, Wiki/, Docs/, Certificate/, etc.) ko "projects" map karna **allowed nahi** hai. Yeh sirf code/content domain folders hain.

### 9.2 Project Report Structure

```
/Aliens/Report/Project/{AppName}/
└── Dashboard.md    ← Planner progress dashboard (aggregated from all Cyborgs)
```

| Element | Value |
|:---|:---|
| **AppName** | Planner CSV se extracted (e.g., ToDo, Teams, Admin, CRM, Git, WebOS_Engine_PHP) |
| **Dashboard.md** | Planner rows summary: pending/completed/failed, stage breakdown, Cyborg assignments, delivery verification |
| **Daily/Weekly/Monthly/Yearly** | Is level par Daily/Weekly/Monthly/Yearly reports generate NAHI hote — sirf Dashboard |
| **Multi-Cyborg** | Dashboard automatically multiple Cyborgs ka data aggregate karta hai |

### 9.3 Project Discovery (Planner-Based)

```
1. Scan /Aliens/Project/ALL/ for *.csv files
2. Extract AppName from each CSV filename:
   - {AppName}_Planner.csv → AppName
   - {AppName}_Planner_STAGE_{N}.csv → AppName (deduplicate stages)
   - {EngineType}_Update-{N}.csv → EngineType as AppName
3. Existing /Aliens/Report/Project/{AppName}/ folders bhi scan karo
4. Union = all known projects
5. For each AppName: read planner CSV data → generate Dashboard.md
```

> **ANTI-PATTERN (FORBIDDEN):** Pehle workflow code domain folders ko projects samajhta tha aur per-domain dashboards banata tha
> (e.g., `/Report/Project/AC0000/WebOS/Dashboard.md`). Yeh **GALAT** tha. "Project" sirf ACC Planner se aata hai.

---

### 9.4 ACC Planner System (`/Aliens/Project/` — SSOT)

**CRITICAL:** `/Aliens/Project/` folder ek code project NAHI hai. Yeh folder **ACC Planner CSV files** ka ghar hai — yeh planning/task management files hain jo define karti hain ke KYA kaam karna hai, KIS WebApp/Engine ke liye, aur kya STATUS hai.

#### 9.4.1 Project Folder Structure

```
/Aliens/Project/
├── ALL/                          ← Master planners (SSOT — all apps ka combined set)
│   ├── {AppName}_Planner.csv           ← Main planner for a WebApp (e.g., ToDo_Planner.csv, Teams_Planner.csv)
│   ├── {AppName}_Planner_STAGE_{N}.csv ← Stage-level planner (phased delivery)
│   ├── {EngineType}_Update-{N}.csv     ← Engine update planners (e.g., WebOS_Engine_PHP_Update-5.csv)
│   ├── WebOS_SQL_Update.csv            ← SQL-level update planner
│   └── manifest.json                  ← Planner registry/metadata
├── {CyborgID}/                   ← Developer-specific task copies (e.g., C1001/, C1008/, AnilNayak/)
│   ├── {Planner_Name}.csv             ← Assigned planner copy for this Cyborg
│   └── ALL/                           ← (optional) further breakdowns like .tasks.csv
├── README.md
└── LICENSE
```

#### 9.4.2 Planner CSV Naming Convention

| Pattern | Example | Meaning |
|:---|:---|:---|
| `{AppName}_Planner.csv` | `ToDo_Planner.csv` | Main WebApp planner (all rows for that app) |
| `{AppName}_Planner_STAGE_{N}.csv` | `Teams_Planner_STAGE_1.csv` | Stage-specific planner (phased delivery, N = stage number) |
| `{EngineType}_{Update/Create}-{N}.csv` | `WebOS_Engine_PHP_Update-5.csv` | Engine code update/create planner |
| `WebOS_SQL_Update.csv` | `WebOS_SQL_Update.csv` | WebOS SQL schema update planner |

**Known AppNames (from planners on disk):**
ToDo, Teams, Git, Admin, CRM, Designer, Docx, Drive, Sheet, Slide, PowerPoint, Visio, Project

#### 9.4.3 Planner CSV Row Structure (Key Fields)

Planner CSVs me har row ek **unit** represent karti hai — ek buildable artifact (Screen, Component, API, Engine, etc.).

Key columns:
- `ID` — row identifier
- `name` — unit name
- `worklflow` — target workflow (e.g., `WebApp_Screen`, `WebSDK_Module_API`, `WebOS_Engine_PHP`, `Meta`)
- `description` — unit ka prompt/instructions (CSV-driven execution ka payload)
- `status` — `0` = pending, `1` = complete, `2` = failed
- `assign` — Cyborg ID assigned for this row (e.g., `C1001`, `C1008`)
- `planning_path`, `code_path`, `doc_path` — output file paths
- `planning`, `code`, `doc` — flags (done/not done)
- `parent`, `parent_type` — hierarchy (Screen→WebApp, Section→Screen, etc.)
- `depends_on` — dependency chain

Row 0 is usually `__PlannerMeta__` (the meta/config row, not an actual task).

#### 9.4.4 Developer Assignment Folders (`{CyborgID}/`)

- `/Aliens/Project/ALL/` = master set (all rows, all assignments)
- `/Aliens/Project/C1001/` = Cyborg C1001 ke assigned tasks ka copy
- `/Aliens/Project/C1008/` = Cyborg C1008 ke assigned tasks ka copy
- `/Aliens/Project/AnilNayak/` = developer-direct assignments

Yeh folders me subset planners hote hain — sirf woh rows jo us Cyborg ko assigned hain.

#### 9.4.5 Report me Planner Data Kaise Use Karein

Reports me `Project/` folder ka data **planning analytics** ke tor par include hona chahiye:

1. **Planner Inventory:** Kitne planners exist karte hain, kon kon se apps ke liye
2. **Per-Planner Status Summary:**
   - Total rows
   - `status=0` (pending) count
   - `status=1` (completed) count
   - `status=2` (failed) count
   - Completion percentage
3. **Stage Breakdown:** Agar multi-stage planners hain (STAGE_1, STAGE_2, ...) to per-stage progress
4. **Cyborg Assignment Summary:** Kon se Cyborg ko kitne tasks assigned hain
5. **Cross-Reference:** Planner tasks ko actual code output se cross-reference karo
   - Planner me row ka `code_path` agar disk par exist karta hai = verified delivery
   - Planner me row ka `code_path` agar disk par exist nahi karta = pending/gap

#### 9.4.6 Missing Planner Detection & Creation Rule

**Rule (IMPORTANT):**
Agar kisi WebApp/Engine ke liye kaam hua hai (filesystem scan me files detected hain) lekin uska **ACC Planner CSV nahi milta** `/Aliens/Project/ALL/` me, to:

1. Report me **"Missing Planner"** flag karo aur clearly mention karo:
   - "WebApp {AppName} ke liye kaam hua hai lekin koi planner nahi mila"
2. Report workflow **planner create kar sakta hai** `/Aliens/Project/ALL/` me:
   - File name: `{AppName}_Planner.csv` (naming convention follow karo)
   - Content: Discovered work ko rows me document karo (which files were created, under which folders)
   - `status=1` mark karo un tasks ke liye jo already complete hain (kaam ho chuka hai)
   - Purpose: Retrospective documentation — yeh record banata hai ke kya kaam kiya gaya tha
3. Yeh sirf **documentation/tracking** purpose ke liye hai — new work assign nahi karna
4. Created planner me `__PlannerMeta__` row mandatory hai (Row 0)

**Planner Creation Permission:**
- Report workflow ko `/Aliens/Project/` me files CREATE ki permission hai (manifest `AllowCreate: true` + `AllowedRoots` me `Project` included)
- Existing planners MODIFY karne ki permission **nahi** hai — sirf new files create allowed
- Agar planner already exists to skip — overwrite forbidden

---

## [10] Terminal Usage Policy (Report-Specific)

### 10.1 Allowed Terminal Actions

Reports workflow ko terminal usage ki special permission hai — **sirf data gathering ke liye**:

```
ALLOWED:
- PowerShell Get-ChildItem for filesystem scanning
- File count/size aggregation
- Date/day-name verification
- Existing report listing

NOT ALLOWED:
- Terminal se report content generate karna
- Bulk file creation via scripts
- Any destructive operations
```

### 10.2 Scan Script Rules

- Scan scripts MUST go to `/Aliens/Cyborg/AnilCyborg/Code/`
- Each script MUST have a descriptive header
- Scripts are reusable across report runs
- After scan, data is used by AI agent to write reports

---

## [11] Error Handling

| Error Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_REPORT_TARGET_INVALID` | Target matches none of the 5 modes | STOP, show supported modes |
| `ACC_ERR_REPORT_CPU_MISMATCH` | Cyborg Target ≠ manifest | STOP, report mismatch |
| `ACC_ERR_REPORT_ALIEN_UNKNOWN` | Alien email not found in manifest Team.Aliens[] | STOP, report unknown Alien |
| `ACC_ERR_REPORT_NO_DATA` | No working days / planner rows / repo activity | STOP, report scan results |
| `ACC_ERR_REPORT_SCAN_FAILED` | PowerShell scan error | STOP, report error details |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | STOP, ask for description |

---

## [12] Execution Summary

```
Workflow( 'Report', '{Target}', '{Description}' )

Step 1: Target Mode Resolution (Section [3.4]) — dispatch to one of 5 modes
Step 2: Gate #1 (Cyborg ID validation) — only if mode carries a CyborgID
Step 3: Run Report-Planning (scan + discover, mode-aware)
Step 4: Run Report-Code (generate missing reports per mode's output locations)
Step 5: (Repo mode only) Create/update in-repo /Aliens/{RepoName}/Dashboard.md
Step 6: Run Report-Documentation (optional: index update)
Step 7: Save RunRecord
```

---

## [13] Changelog

- **26.01.00** (2026-04-19): **MAJOR — 5 Target Modes.** Added Mode 2 (`Project`), Mode 3 (`Project/{CyborgID}`), Mode 4 (`Repo`), Mode 5 (Alien email). **Breaking:** Mode 1 (`{CyborgID}`) no longer auto-generates Project reports — must call `Project` / `Project/{CyborgID}` mode separately. Added Sections [3.4] Target Mode Resolution, [14] Repo Mode, [15] Alien Mode. Expanded Outputs block with 5 mode-specific roots. Added `ACC_ERR_REPORT_TARGET_INVALID` and `ACC_ERR_REPORT_ALIEN_UNKNOWN` error codes.
- 26.00.02 (2026-03-12): **MAJOR FIX** — Project-Level Reports concept redefined. "Project" = ACC Planner CSV, NOT code domain folders. Project reports produced per-AppName Dashboards only.
- 26.00.01 (2026-03-12): Fixed Project folder understanding — `/Aliens/Project/` is ACC Planner CSV storage. Added Section 9.4 (ACC Planner System) and missing-planner detection rule.
- 26.00.00 (2026-03-12): Initial version — full Report workflow with Daily/Weekly/Monthly/Yearly/Dashboard support, dual output (Cyborg + Project level), CPU validation, filesystem scanning, enterprise BI quality.

---

## [14] Repo Mode (`Target = 'Repo'`) — NEW in v26.01.00

### 14.1 Purpose

Workspace ke har repository ke liye **repo-level BI reports** generate karo, **aur** har repo ke andar wali
`Dashboard.md` create/update karo.

### 14.2 Repo Discovery (SSOT)

```
1. Read /Aliens/Cyborg/AnilCyborg/Ecosystem/Repos.Index.md
2. Extract list of repo names (e.g., Cyborg, WebOS, WebSDK, WebApp, Blog, Course, ...)
3. For each repo that exists on disk as a folder under /Aliens/ :
   → include in run
4. Repos listed in Repos.Index.md but folder missing on disk:
   → skip + record in run log as "deferred: folder missing"
```

### 14.3 Per-Repo Execution

For each repo `{RepoName}`:

```
Part A — External reports folder:
  /Aliens/Report/Repo/{RepoName}/
    ├── Daily_Report_YYYY-MM-DD.md       (per working day, filesystem-scoped to this repo)
    ├── Weekly_Report_YYYY-WNN.md
    ├── Monthly_Report_YYYY-MM.md
    ├── Yearly_Report_YYYY.md
    └── Dashboard.md

Part B — In-repo dashboard:
  /Aliens/{RepoName}/Dashboard.md
    Create if missing; update if present.
    Structure: enterprise BI dashboard (KPIs, trend chart, composition pie,
    drill-down tables, risks, growth levers, next actions, glossary).
    Domain-specific metrics per repo (do NOT template copy-paste).
```

### 14.4 Workflow-Owned In-Repo Dashboards (Exclusions)

Yeh files Repo mode **NEVER overwrite** karega — kisi dedicated workflow ki ownership hai:

| In-Repo Path | Owner Workflow |
|:---|:---|
| `/Aliens/Attendance/Dashboard.md` | `Attendance_Dashboard` |

Agar future me aur dashboards workflow-owned bane, yahan add karo.

### 14.5 Per-Repo Data Scope

- **Filesystem scope** = `/Aliens/{RepoName}/**`
- **Exclusions** = `.git`, `node_modules`, `__pycache__`
- **Daily/Weekly/Monthly/Yearly** = activity scoped strictly to this repo folder
- **Dashboard.md** (both external and in-repo) = full lifetime picture of this repo

### 14.6 Skip Semantics

Agar kisi repo ke liye filesystem scope me 0 files mile (empty repo), to:
- External report folder skip (no Daily/Weekly/Monthly/Yearly generated)
- In-repo `Dashboard.md` still create (minimal "no activity yet" placeholder)

---

## [15] Alien Mode (`Target = '{alias}@aliens.(company|id)'`) — NEW in v26.01.00

### 15.1 Purpose

Kisi **Alien** (human team member) ki consolidated reports banana. Report me:
- Alien ki manual activity
- Alien ke under control me jitne Cyborgs hain unki merged activity (each flagged with source CyborgID)

### 15.2 Alien Validation

```
1. Parse target → alias = local-part of email
2. Read /Aliens/manifest.json → Team.Aliens[]
3. Find entry where email matches (@aliens.company or @aliens.id)
4. If no match → STOP with ACC_ERR_REPORT_ALIEN_UNKNOWN
5. Extract ControlsCyborgs[] (list of CyborgIDs this Alien controls)
```

**manifest.json shape (reference — see Ecosystem/Members.Aliens_vs_Cyborgs.md):**

```json
{
  "Team": {
    "Aliens": [
      {
        "alias": "himanshu",
        "email": ["himanshu@aliens.company", "himanshu@aliens.id"],
        "name": "Himanshu",
        "role": "Developer",
        "ControlsCyborgs": ["C1001", "C1002", "C1003", "C1004"]
      }
    ]
  }
}
```

### 15.3 Output Folder

```
/Aliens/Report/Alien/{alias}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

### 15.4 Data Sources (merged)

```
1. MANUAL: /Aliens/.Alien/{alias}/**
   → reviews, approvals, manual commits, recorded decisions
   → attributed as "Manual"

2. CYBORG-MERGED: /Aliens/Report/Cyborg/{CyborgID}/** for every CyborgID in ControlsCyborgs[]
   → each data point retains SourceCyborgID tag
   → in tables, always show: Date | CyborgID | Activity | Source=Cyborg
   → Mode 1 Cyborg reports MUST exist first (prerequisite; if missing, log warning)
```

### 15.5 Attribution Rule (STRICT)

Har merged entry me source **mandatory flag** hai:

| Source | Label |
|:---|:---|
| Manual by the Alien | `Source: Manual` |
| From Cyborg X | `Source: Cyborg C1001` |

**FORBIDDEN:** Cyborg-done kaam ko "manual Alien work" ke tor par present karna.
Yeh ethics aur audit integrity ka matter hai.

### 15.6 Dashboard Composition

Alien Dashboard me alag-alag sections:
1. Executive KPI card (Alien-level rollup)
2. Manual vs Cyborg split (pie)
3. Per-Cyborg contribution breakdown
4. Workstream distribution
5. Peak days & velocity trends
6. Ethics/attribution integrity check (100% of Cyborg entries flagged)

---
