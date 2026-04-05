---
WorkflowId: Report-Planning
Title: "Report Data Discovery & Scan Planning"
Category: System
Type: Workflow_Singular-Planning
Domain: Report
Scope: "Discover all working days, scan filesystem, build data model for report generation"

Status: Stable
Version: 26.00.02
LastUpdated: 2026-03-12

Intent:
  Summary: >
    Report-Planning ka kaam: filesystem scan karke working days discover karna,
    per-day/per-folder file counts collect karna, existing reports check karna,
    aur missing reports ki list banana. Yeh data Report-Code ko pass hota hai.
  WhenToUse:
    - "Report plural workflow ke child ke tor par (automatically called)"

Inputs:
  Targets:
    Format: "Single Cyborg ID"
    Examples:
      - "AC0000"
  Description:
    Format: "Inherited from parent Report workflow"

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
  Data:
    - "/Aliens/manifest.json"

Outputs:
  Planning:
    - "ScanResult: per-date file counts grouped by top-level folder"
    - "WorkingDays: list of all dates with actual development activity"
    - "FolderBreakdown: 2-level subfolder breakdown per working day"
    - "ExistingReports: list of already-generated report files"
    - "MissingReports: list of reports that need to be generated"
    - "DayNames: verified day-of-week for each working day"
    - "ProjectMapping: ACC Planner-based project discovery (AppName → planner stats)"
---

# Report-Planning (Singular-Planning)

## [0] Purpose

Report-Planning ka kaam filesystem scan karke **data model** build karna hai jo Report-Code use karega reports generate karne ke liye.

Is step me koi report file nahi banti — sirf data collect aur organize hota hai.

---

## [1] Execution Steps (Mandatory Order)

### Step 1: Manifest Validation (Gate)

```
1. Read /Aliens/manifest.json
2. Extract Developer.Username (and CyborgID/CPUID if available)
3. Verify Targets matches one of these identifiers
4. Mismatch → STOP with ACC_ERR_REPORT_CPU_MISMATCH
```

### Step 2: Full Filesystem Scan

**Goal:** Discover ALL files created on this workspace, grouped by date and folder.

**Method:** PowerShell single-pass scan

```powershell
# Scan script: /Aliens/Cyborg/AlienCyborg/Code/report_scan.ps1
# Purpose: Single-pass full filesystem scan for Report workflow
# Groups all files by CreationTime date + top-level folder

Get-ChildItem -Path 'C:\Aliens' -Recurse -File |
    Where-Object {
        $_.FullName -notmatch '\\(\.git|node_modules|__pycache__|\.Alien|Report)\\' -and
        $_.FullName -notmatch '\\\.git$'
    } |
    ForEach-Object {
        $rel = $_.FullName -replace [regex]::Escape('C:\Aliens\'), ''
        $topFolder = ($rel -split '\\')[0]
        [PSCustomObject]@{
            Date = $_.CreationTime.ToString('yyyy-MM-dd')
            Folder = $topFolder
            Size = $_.Length
        }
    } |
    Group-Object Date, Folder |
    Sort-Object Name |
    ForEach-Object {
        $parts = $_.Name -split ', '
        [PSCustomObject]@{
            Date = $parts[0]
            Folder = $parts[1]
            FileCount = $_.Count
            TotalSizeKB = [math]::Round(($_.Group | Measure-Object Size -Sum).Sum / 1024, 1)
        }
    } |
    Format-Table -AutoSize
```

**Rules:**
- Script location: `/Aliens/Cyborg/AlienCyborg/Code/report_scan.ps1`
- Exclusions: `.git`, `node_modules`, `__pycache__`, `.Alien`, `Report` folders
- Output: Date × Folder × FileCount × TotalSizeKB

### Step 3: Bundled Asset Filtering

```
1. Identify dates that are significantly older than workspace creation
   (e.g., 2011, 2019 dates in DartApp folder = bundled font assets)
2. Mark these as "bundled assets" — exclude from working day counts
3. Log excluded dates + folders + file counts for Dashboard transparency
```

**Heuristic:**
- If date year < (workspace first working day year) AND folder = DartApp → bundled asset
- If date year < 2025 AND all files in single framework folder → likely bundled

### Step 4: Working Days Identification

```
1. From scan results, extract unique dates with actual development activity
2. Exclude bundled asset dates
3. Sort chronologically
4. For each date: verify day-of-week via PowerShell
   (Get-Date "YYYY-MM-DD").DayOfWeek
5. Output: list of { Date, DayName, TotalFiles, FolderBreakdown }
```

### Step 5: Subfolder Detail Scan (Targeted)

For each working day, run targeted 2-level subfolder scan:

```powershell
# For specific date + folder combinations, get deeper breakdown
Get-ChildItem -Path "C:\Aliens\{TopFolder}" -Recurse -File |
    Where-Object {
        $_.CreationTime.Date -eq [datetime]"YYYY-MM-DD" -and
        $_.FullName -notmatch '\\(\.git|node_modules|__pycache__)\\' 
    } |
    ForEach-Object {
        $rel = $_.FullName -replace [regex]::Escape("C:\Aliens\{TopFolder}\"), ''
        $parts = $rel -split '\\'
        $sub = if ($parts.Count -ge 2) { "$($parts[0])\$($parts[1])" } else { $parts[0] }
        $sub
    } |
    Group-Object |
    Sort-Object Count -Descending |
    Select-Object Count, Name -First 20
```

### Step 6: Existing Report Inventory

```
1. List all files in /Aliens/Report/Cyborg/{CyborgID}/
2. Parse filenames to extract:
   - Daily: Daily_Report_YYYY-MM-DD.md → date
   - Weekly: Weekly_Report_YYYY-WNN.md → week
   - Monthly: Monthly_Report_YYYY-MM.md → month
   - Yearly: Yearly_Report_YYYY.md → year
   - Dashboard: Dashboard.md → exists/not
3. Build inventory of existing reports
```

### Step 7: Missing Report Calculation

```
For each report type, calculate what's missing:

DAILY:
  - For each working day: check if Daily_Report exists → if not, add to missing list

WEEKLY:
  - For each ISO week containing working days: check if Weekly_Report exists
  - ISO week = (Get-Date "YYYY-MM-DD").ToString("yyyy-'W'dd") using ISO calculation

MONTHLY:
  - For each month containing working days: check if Monthly_Report exists

YEARLY:
  - For each year containing working days: check if Yearly_Report exists

DASHBOARD:
  - Always marked as "needs regeneration"
```

### Step 8: Folder Activity Summary (Non-Project)

```
For each working day, summarize folder-level file activity:

1. List all top-level folders with file creation on that date
2. Per folder: file count + total size
3. This data feeds into Cyborg-level reports (Daily/Weekly/Monthly/Yearly)

IMPORTANT:
- Yeh step code domain folders (WebOS/, WebSDK/, WebApp/, etc.) ko "projects" MAP NAHI karta
- "Project" sirf ACC Planner CSV se aata hai (Step 9 handles this)
- Code domain folders sirf Cyborg-level reports ka folder breakdown table me use hote hain
- /Aliens/Project/ folder ACC Planner CSVs contain karta hai — yeh code/content folder NAHI hai

Output: per-day folder activity (used by Cyborg-level report templates)
```

### Step 9: ACC Planner Scan (`/Aliens/Project/`)

**Goal:** `/Aliens/Project/` folder me existing ACC Planner CSV files discover karna aur unka status summarize karna.

**9.1 Planner Discovery:**
```
1. List all CSV files in /Aliens/Project/ALL/
   - Filter: *_Planner*.csv, *_Update*.csv patterns
   - Each CSV = 1 ACC Planner
2. For each planner CSV:
   a. Read header row (column names)
   b. Count total data rows (excluding header + Row 0 if Meta)
   c. Count by status: 0=pending, 1=completed, 2=failed
   d. Extract AppName from filename (e.g., ToDo_Planner.csv → ToDo)
   e. Note stages if present (STAGE_1, STAGE_2, etc.)
3. Output: PlannerInventory list with per-planner stats
```

**9.2 Developer Assignment Discovery:**
```
1. List all subdirectories in /Aliens/Project/ that match CyborgID pattern (C1001, C1008, etc.) or developer names
2. For each assignment folder:
   a. List CSV files (these are copies assigned to that Cyborg)
   b. Count rows by status
   c. Map CyborgID → assigned planners → row counts
3. Output: CyborgAssignments map
```

**9.3 Missing Planner Detection:**
```
1. From filesystem scan (Step 2), identify all unique top-level folders with activity
2. Map folder → expected AppName (e.g., WebApp/{AppName}/ → AppName)
3. For each detected AppName:
   a. Check if {AppName}_Planner.csv exists in /Aliens/Project/ALL/
   b. If missing → add to MissingPlanners list
4. Output: MissingPlanners list
```

**9.4 Planner-to-Code Cross-Reference:**
```
1. For each planner CSV with status=1 rows:
   a. Read code_path column from completed rows
   b. Check if code_path file actually exists on disk
   c. Matched = verified delivery
   d. Unmatched = gap (planner says done but file missing)
2. Output: VerifiedDeliveries count, GapCount
```

---

## [2] Planning Output Artifact

Planning output is saved as a structured data model (mental/context model) that Report-Code consumes.

Key data structures:

```
PlanningData:
  CyborgID: "{CyborgID}"
  ManifestDeveloper: "{Developer.Username}"
  ScanTimestamp: "YYYY-MM-DD HH:MM:SS"
  
  WorkingDays: [
    { Date, DayName, TotalFiles, TotalSizeKB, FolderBreakdown: { Folder: Count }, SubfolderDetails }
  ]
  
  BundledAssets: [
    { Date, Folder, FileCount, Reason }
  ]
  
  ExistingReports: {
    Daily: [ "YYYY-MM-DD", ... ],
    Weekly: [ "YYYY-WNN", ... ],
    Monthly: [ "YYYY-MM", ... ],
    Yearly: [ "YYYY", ... ],
    Dashboard: true/false
  }
  
  MissingReports: {
    Daily: [ "YYYY-MM-DD", ... ],
    Weekly: [ "YYYY-WNN", ... ],
    Monthly: [ "YYYY-MM", ... ],
    Yearly: [ "YYYY", ... ],
    Dashboard: true  (always regenerated)
  }
  
  ProjectMapping: {
    "AppName": {
      PlannerFile: "filename.csv",
      TotalRows: N,
      Pending: N,
      Completed: N,
      Failed: N,
      CompletionPct: N%,
      Stages: [ stage numbers if multi-stage ],
      AssignedCyborgs: [ "C1001", "C1008", ... ]
    }
  }
  
  Note: "ProjectMapping is planner-based (from Step 9 ACC Planner Scan). It is NOT folder-to-domain mapping."
  
  PlannerData: {
    PlannerInventory: [
      {
        FileName: "ToDo_Planner.csv",
        AppName: "ToDo",
        Stage: null | 1 | 2 | ...,
        TotalRows: N,
        Pending: N,    (status=0)
        Completed: N,  (status=1)
        Failed: N,     (status=2)
        CompletionPct: N%
      }
    ],
    CyborgAssignments: {
      "C1001": {
        Planners: [ "ToDo_Planner_STAGE_1.csv", ... ],
        TotalRows: N,
        Pending: N,
        Completed: N
      }
    },
    MissingPlanners: [
      { AppName: "...", Reason: "Files found in WebApp/{AppName}/ but no planner exists" }
    ],
    VerifiedDeliveries: N,
    DeliveryGaps: N
  }
  
  GenerationQueue: [
    { Type: "Daily|Weekly|Monthly|Yearly|Dashboard", Identifier: "...", Priority: N }
  ]
```

---

## [3] Quality Gates (Planning)

- All file counts from actual scan (zero guessing)
- Day names verified via PowerShell
- Bundled assets properly filtered
- No data from other Cyborgs
- Missing report calculation is exhaustive (no gaps)

---

## [4] Changelog

- 26.00.02 (2026-03-12): **MAJOR FIX** — Step 8 rewritten: removed domain-to-project mapping (WebOS→WebOS etc.). Step 8 now only summarizes folder activity for Cyborg-level reports. ProjectMapping in PlanningData changed from folder-based to ACC Planner-based (AppName → planner stats).
- 26.00.01 (2026-03-12): Fixed Project folder handling — removed `Project/ → Project` from project mapping. Added Step 9 (ACC Planner Scan) with planner discovery, developer assignment discovery, missing planner detection, and planner-to-code cross-reference. Updated PlanningData output artifact with PlannerData structure.
- 26.00.00 (2026-03-12): Initial version — filesystem scan, working day discovery, missing report detection, project mapping.
