---
WorkflowId: Report
Title: "Developer Activity Report Generator (Daily · Weekly · Monthly · Yearly · Dashboard)"
Category: System
Type: Workflow_Plural
Domain: Report
Scope: "Generate comprehensive BI reports for a specific Cyborg — covering all work done on THAT Cyborg only"

Status: Stable
Version: 26.00.02
LastUpdated: 2026-03-12

Intent:
  Summary: >
    Yeh workflow ek specific Cyborg ke liye COMPLETE activity reports generate karta hai.
    Reports me sirf usi Cyborg par kiya gaya kaam include hota hai — kisi dusre Cyborg ka kaam nahi.
    Missing reports (Daily, Weekly, Monthly, Yearly, Dashboard) automatically detect aur generate hote hain.
    Output 2 jagah save hoti hai: Cyborg-level (CPU-centric) aur Project-level (ACC Planner-centric Dashboard).
  WhenToUse:
    - "Jab bhi kisi Cyborg ki activity reports generate karni hon"
    - "Jab missing daily/weekly/monthly/yearly reports detect aur fill karni hon"
    - "Jab Dashboard refresh karna ho (full history)"
    - "Periodic reporting cycle ke end me (din/week/month/year close)"

Inputs:
  Targets:
    Format: "single Cyborg ID (must match manifest Developer.Username or a registered Cyborg ID)"
    Examples:
      - "AC0000"
      - "C1072"
    Validation:
      - "Targets me sirf 1 Cyborg ID allowed (comma-separated multiple Cyborgs NOT allowed)"
      - "Cyborg ID MUST match /Aliens/manifest.json → Developer.Username"
      - "Mismatch => STOP with ACC_ERR_REPORT_CPU_MISMATCH"
  Description:
    Format: "Any human language"
    Examples:
      - "Generate all missing reports for this CPU"
      - "Dashboard refresh karo with latest data"
      - "February 2026 ke liye monthly report generate karo"
      - "Puri history cover karo — koi din miss nahi hona chahiye"

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
    CyborgLevel:
      Root: "/Aliens/Report/Cyborg/{CyborgID}/"
      Files:
        - "Daily_Report_YYYY-MM-DD.md"
        - "Weekly_Report_YYYY-WNN.md"
        - "Monthly_Report_YYYY-MM.md"
        - "Yearly_Report_YYYY.md"
        - "Dashboard.md"
    ProjectLevel:
      Root: "/Aliens/Report/Project/{AppName}/"
      Files:
        - "Dashboard.md"
      Note: "AppName = ACC Planner CSV se extract hota hai (e.g., ToDo, Teams, Admin). See Section [9]."

ErrorCodes:
  ACC_ERR_REPORT_CPU_MISMATCH: "Targets Cyborg ID does not match manifest Developer.Username"
  ACC_ERR_REPORT_NO_DATA: "No working days found for the specified CPU"
  ACC_ERR_REPORT_SCAN_FAILED: "Filesystem scan failed or returned no results"
---

# Report Workflow (Plural)

## [0] Purpose (Hinglish)

`Report` workflow ka goal: ek specific **Cyborg ID** ke liye **comprehensive activity reports** generate karna.

Reports me **sirf usi Cyborg par kiya gaya kaam** shamil hota hai — kisi dusre Cyborg ka data report me nahi aayega.

### Dual Purpose — BI + CPU Handoff

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

### Call Signature

```
Workflow( 'Report', '{CyborgID}', '{Description}' )
```

**Examples:**

```
Workflow( 'Report', 'AC0000', 'Generate all missing reports for this CPU' )
Workflow( 'Report', 'AC0000', 'Dashboard refresh with latest data' )
Workflow( 'Report', 'C1072', 'February monthly report generate karo' )
```

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

## [3] Cyborg ID Validation (STRICT — Gate #1)

### 3.1 Manifest Load

```
1. Read /Aliens/manifest.json
2. Extract Developer.Username
3. Compare with Targets (case-sensitive)
4. Mismatch => STOP with ACC_ERR_REPORT_CPU_MISMATCH
```

### 3.2 Validation Rules

- Targets me **sirf 1 Cyborg ID** allowed.
- Comma-separated multiple Cyborg IDs **forbidden** (yeh Singular-style single-target workflow hai Plural wrapper me).
- Cyborg ID ko manifest ke `Developer.Username` se **exact match** hona chahiye.
- Mismatch par bilkul bhi koi report generate nahi hogi — hard stop.

**Rationale:** Agar is PC ka `Developer.Username = AnilNayak` hai aur koi `AC0000` bhejta hai, to wo match nahi karega UNLESS manifest me `CyborgID` ya `DeveloperID` explicitly map kiya gaya ho. Agent ko manifest me available identification fields check karni hain.

### 3.3 Cyborg ID Resolution Order

```
1. manifest.Developer.Username (primary)
2. manifest.Developer.CyborgID (if exists)
3. manifest.Developer.CPUID (if exists)
4. None match => STOP
```

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

## [4] Report Output Locations (SSOT)

### 4.1 Cyborg-Level Reports (CPU-Centric)

```
/Aliens/Report/Cyborg/{CyborgID}/
├── Daily_Report_YYYY-MM-DD.md         (per working day)
├── Weekly_Report_YYYY-WNN.md          (per ISO week)
├── Monthly_Report_YYYY-MM.md          (per calendar month)
├── Yearly_Report_YYYY.md              (per calendar year)
└── Dashboard.md                       (always-latest aggregated BI dashboard)
```

**Content:** Pura kaam jo is CPU ne kiya — across ALL projects.

### 4.2 Project-Level Reports (ACC Planner-Centric)

**CRITICAL:** "Project" ka matlab code domain folders (WebOS, WebSDK, WebApp, Course_ACLE etc.) **NAHI** hai.
"Project" ka matlab hai: **ACC Planner** — `/Aliens/Project/` folder me stored planning CSV files.

```
/Aliens/Report/Project/{AppName}/
└── Dashboard.md                       (planner progress dashboard for this app/engine)
```

**AppName** = ACC Planner CSV se extract hota hai:
- `ToDo_Planner.csv` → AppName = `ToDo`
- `Teams_Planner_STAGE_1.csv` → AppName = `Teams`
- `WebOS_Engine_PHP_Update-5.csv` → AppName = `WebOS_Engine_PHP`

**Content:** Us app/engine ki planner status — total rows, pending/completed/failed counts,
stage-wise breakdown, Cyborg assignment summary, aur delivery verification.
Yeh code file counts / domain folder activity **nahi** hai — yeh **PLANNING status** hai.

**Dashboard.md aggregated hai:** Multiple Cyborgs ka planner data ek hi Dashboard me aggregate hota hai.

> **FORBIDDEN:** Workspace top-level folders (WebOS/, WebSDK/, WebApp/, Course_ACLE/, Wiki/, etc.) ko "projects" samajh ke unke liye `/Report/Project/{DomainName}/` folder banana **allowed nahi** hai.

### 4.3 Project ID (AppName) Discovery

AppNames (= ProjectIDs) discover karne ke liye:

```
1. Scan /Aliens/Project/ALL/ for planner CSV files
2. Extract AppName from each filename:
   - {AppName}_Planner.csv → AppName
   - {AppName}_Planner_STAGE_{N}.csv → AppName
   - {EngineType}_Update-{N}.csv → EngineType as AppName
3. Deduplicate AppNames (multiple stages = same AppName)
4. Each unique AppName gets its own report folder:
   /Aliens/Report/Project/{AppName}/Dashboard.md
5. FORBIDDEN: code domain folders (WebOS/, WebSDK/ etc.) se ProjectID derive karna
```

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
/Aliens/Cyborg/AlienCyborg/Code/
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

- Scan scripts MUST go to `/Aliens/Cyborg/AlienCyborg/Code/`
- Each script MUST have a descriptive header
- Scripts are reusable across report runs
- After scan, data is used by AI agent to write reports

---

## [11] Error Handling

| Error Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_REPORT_CPU_MISMATCH` | Targets Cyborg ID ≠ manifest | STOP, report mismatch |
| `ACC_ERR_REPORT_NO_DATA` | No working days found | STOP, report scan results |
| `ACC_ERR_REPORT_SCAN_FAILED` | PowerShell scan error | STOP, report error details |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | STOP, ask for description |

---

## [12] Execution Summary

```
Workflow( 'Report', '{CyborgID}', '{Description}' )

Step 1: Validate Cyborg ID against manifest ← Gate
Step 2: Run Report-Planning (scan + discover)
Step 3: Run Report-Code (generate all missing reports)
Step 4: Run Report-Documentation (optional: index update)
Step 5: Save RunRecord
```

---

## [13] Changelog

- 26.00.02 (2026-03-12): **MAJOR FIX** — Project-Level Reports concept completely redefined. "Project" = ACC Planner CSV, NOT code domain folders (WebOS/WebSDK/WebApp etc.). Sections 4.2, 4.3, 9.1, 9.2, 9.3 rewritten. Domain-to-project mapping table removed + replaced with planner-based project discovery. Project reports now produce per-AppName Dashboards only (`/Report/Project/{AppName}/Dashboard.md`). Daily/Weekly/Monthly/Yearly at project level removed.
- 26.00.01 (2026-03-12): Fixed Project folder understanding — `/Aliens/Project/` is ACC Planner CSV storage (not a code project). Added Section 9.4 (ACC Planner System), planner analytics in reports, missing planner detection and creation rule.
- 26.00.00 (2026-03-12): Initial version — full Report workflow with Daily/Weekly/Monthly/Yearly/Dashboard support, dual output (Cyborg + Project level), CPU validation, filesystem scanning, and enterprise BI quality standards.
