---
WorkflowId: VSCode_Chat
Title: "CPU Handoff — Load Previous CPU Context into Current Chat Session"
Category: System
Type: Workflow_Singular
Domain: VSCode
Scope: "Read a source CPU's reports and generate a comprehensive context briefing for seamless work continuation on the current CPU"

Status: Stable
Version: 26.00.00
LastUpdated: 2026-03-12

Intent:
  Summary: >
    VSCode_Chat workflow ka goal: ek purane CPU ki reports padhkar current CPU/agent session me
    poora context load karna — taaki kaam wahin se shuru ho jahan purane CPU ne chhora tha.
    Yeh workflow CPU migration/handoff ka core enabler hai. Reports sirf BI ke liye nahi, 
    context-transfer ke liye bhi banai jaati hain. Is workflow ke through C1001 ka kaam
    C1002 me seamlessly continue ho sakta hai.
  WhenToUse:
    - "Jab ek CPU band karna ho aur uska pending kaam dusre CPU ko transfer karna ho"
    - "Jab naye CPU par purane CPU ka poora context chahiye ho"
    - "Jab specific project ka kaam ek CPU se dusre me shift karna ho"
    - "Jab historical context reload karna ho bina manually reports padhne ke"

Inputs:
  Targets:
    Format: "Path to source CPU's report folder"
    Examples:
      - "/Aliens/Report/Cyborg/C1001"
      - "/Aliens/Report/Cyborg/AC0000"
      - "/Aliens/Report/Project/ACLE"
    Validation:
      - "Path MUST exist on disk"
      - "Path me reports (.md files) honi chahiye"
      - "Empty folder => STOP with ACC_ERR_CHAT_NO_REPORTS"
  Description:
    Format: "Any human language"
    Examples:
      - "C1001 ka poora context load karo — pending tasks continue karne hain"
      - "AC0000 ke ACLE project ka context load karo"
      - "Last 30 days ka context load karo for project handoff"
      - "Dashboard + latest monthly report se context generate karo"

Defaults:
  Version: "latest"
  VersionFallback: "PHP.26.00.00"
  ContextDepth: "full"

Permissions:
  NoDelete: true
  CanCreateFiles: false
  CanEditFiles: false
  CanModifyProd: false
  CanTouchDB: false
  CanRunTerminal: false

Approval:
  Mode: "auto"
  Notes: >
    VSCode_Chat is read-only — it only reads existing reports and generates 
    in-memory context. No files are created, modified, or deleted.
    No approval required.

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
  Data:
    - "/Aliens/manifest.json"

Calls:
  ChildWorkflows: []

Outputs:
  RunRecord: false
  Planning: false
  Code: false
  Documentation: false
  ChatContext:
    Type: "In-memory context loaded into VS Code chat session"
    Format: "Structured briefing (not saved to disk)"
    Sections:
      - "Source CPU Identity"
      - "Work History Timeline"
      - "Project Portfolio Status"
      - "Pending / In-Progress Tasks"
      - "Key Deliverables Completed"
      - "Velocity & Patterns"
      - "Recommended Next Actions"

ErrorCodes:
  ACC_ERR_CHAT_NO_REPORTS: "No report files found at the specified path"
  ACC_ERR_CHAT_PATH_INVALID: "Targets path does not exist or is not accessible"
  ACC_ERR_CHAT_DASHBOARD_MISSING: "Dashboard.md not found — partial context only"
---

# VSCode_Chat Workflow (Singular)

## [0] Purpose (Hinglish)

`VSCode_Chat` workflow ka goal: ek **source CPU ki reports** ko read karke current **VS Code chat session** me poora context load karna.

### Why This Exists

Reports sirf **business intelligence** ke liye nahi banai jaati — reports ka ek critical purpose **CPU Handoff** hai:

```
📋 SCENARIO:
  - C1001 par 3 months kaam hua
  - Ab C1001 band hoga, C1002 active hoga
  - C1001 ke pending tasks C1002 ko dene hain
  - C1002 ko wahin se shuru karna hai jahan C1001 ne chhora

🔗 SOLUTION:
  1. Report workflow se C1001 ki puri history reports me capture hai
  2. C1002 par VSCode_Chat run karo pointing to C1001's reports
  3. Agent automatically C1001 ka poora context samajh lega
  4. Kaam seamlessly continue — zero context loss
```

### Call Signature

```
Workflow( 'VSCode_Chat', '{SourceReportPath}', '{Description}' )
```

**Examples:**

```
Workflow( 'VSCode_Chat', '/Aliens/Report/Cyborg/C1001', 'C1001 ka poora context load karo' )
Workflow( 'VSCode_Chat', '/Aliens/Report/Cyborg/AC0000', 'ACLE project ka context chahiye' )
Workflow( 'VSCode_Chat', '/Aliens/Report/Project/ACLE', 'Project-level context load karo' )
```

---

## [1] Universal Includes (Mandatory Order)

1) `require_once Workflows/ACC/_Common/Workflow_Universal.md`
2) `require_once Workflows/ACC/_Common/Workflow_Singular.md`

---

## [2] Execution Steps

### Step 1: Path Validation

```
1. Targets path ko resolve karo (absolute path)
2. Check: path exists on disk?
   - No => STOP with ACC_ERR_CHAT_PATH_INVALID
3. Check: path me .md files hain?
   - No => STOP with ACC_ERR_CHAT_NO_REPORTS
4. Check: Dashboard.md exists?
   - No => WARNING (ACC_ERR_CHAT_DASHBOARD_MISSING) — continue with partial context
   - Yes => Dashboard = primary context source
```

### Step 2: Report Discovery

```
1. Scan Targets path for all .md files
2. Categorize:
   - Dashboard.md        → (primary — highest value)
   - Yearly_Report_*.md  → (strategic overview)
   - Monthly_Report_*.md → (monthly detail)
   - Weekly_Report_*.md  → (weekly detail)
   - Daily_Report_*.md   → (granular detail)
   - INDEX.md            → (navigation reference)
3. Sort each category chronologically (newest first)
4. Count total reports per category
```

### Step 3: Context Loading Strategy

```
LOADING PRIORITY (most valuable first):

Level 1 — ALWAYS load (mandatory):
  ├── Dashboard.md (full read — complete history overview)
  └── Latest Monthly_Report (most recent month's details)

Level 2 — LOAD if Description asks for full context:
  ├── Latest Yearly_Report (if exists)
  ├── Last 4 Weekly_Reports (last month's weekly breakdowns)
  └── Last 7 Daily_Reports (last week's daily details)

Level 3 — LOAD if Description specifies project or deep history:
  ├── All Monthly_Reports (full monthly history)
  ├── All Weekly_Reports (full weekly history)
  └── Selected Daily_Reports (based on Description filters)

DEFAULT = Level 1 + Level 2 (balanced context without overload)
```

### Step 4: Context Extraction

From each loaded report, extract:

```
Per Report:
  ├── Date/Period covered
  ├── Files created (count + size)
  ├── Active folders/projects
  ├── Key deliverables
  ├── Primary workstreams
  └── Key outcomes

From Dashboard specifically:
  ├── Grand totals (lifetime file count, size, working days)
  ├── Project portfolio (all projects + status)
  ├── Peak performance records
  ├── Strategic insights
  ├── Velocity trends
  └── Working patterns
```

### Step 5: Pending Work Detection

```
CRITICAL: Identify what was IN-PROGRESS or PENDING when source CPU stopped.

Detection methods:
1. Dashboard "Recent Activity" section → last active projects
2. Latest Daily Report → what was being worked on last
3. Latest Weekly Report → what was the ongoing workstream
4. Project reports (if available) → per-project completion %
5. Description hints (user may specify which projects to focus on)

Output:
  ├── Last Active Date: {date}
  ├── Last Active Projects: [{project1}, {project2}]
  ├── Ongoing Workstreams: [{work1}, {work2}]
  ├── Likely Pending Tasks: [{task1}, {task2}]
  └── Recommended Resume Points: [{point1}, {point2}]
```

### Step 6: Context Briefing Generation

Generate a structured in-memory briefing (this is what the agent "learns"):

```
═══════════════════════════════════════════════════════════════
  🔗 CPU HANDOFF BRIEFING
═══════════════════════════════════════════════════════════════

  SOURCE CPU:    {SourceCPU_ID}
  CURRENT CPU:   {CurrentCPU_ID} (from manifest)
  HANDOFF DATE:  {Today}
  REPORTS READ:  {TotalReportsRead}

  ───────────────────────────────────────────────────────────
  📊 SOURCE CPU LIFETIME SUMMARY
  ───────────────────────────────────────────────────────────
  Working Days:       {TotalWorkingDays}
  Active Period:      {FirstDate} — {LastDate}
  Total Files:        {TotalFiles}
  Total Size:         {TotalSize}
  Projects Touched:   {ProjectCount}

  ───────────────────────────────────────────────────────────
  🏗️ PROJECT PORTFOLIO
  ───────────────────────────────────────────────────────────
  {For each project:}
  • {ProjectName}: {Status} | {FileCount} files | Last active: {Date}

  ───────────────────────────────────────────────────────────
  ⏳ PENDING / IN-PROGRESS WORK
  ───────────────────────────────────────────────────────────
  {For each pending item:}
  • {PendingTask}: Last worked on {Date} | Context: {Brief}

  ───────────────────────────────────────────────────────────
  📈 VELOCITY & PATTERNS
  ───────────────────────────────────────────────────────────
  Avg Files/Day:      {AvgDaily}
  Peak Day:           {PeakDate} ({PeakFiles} files)
  Primary Pattern:    {WorkPattern}

  ───────────────────────────────────────────────────────────
  🎯 RECOMMENDED NEXT ACTIONS
  ───────────────────────────────────────────────────────────
  1. {Action1}
  2. {Action2}
  3. {Action3}

═══════════════════════════════════════════════════════════════
```

---

## [3] Context Scope Rules

### 3.1 Cyborg-Level Reports (Default)

```
Path: /Aliens/Report/Cyborg/{SourceCPU}/
Scope: ALL work done by that CPU — across all projects
Use case: Full CPU handoff (transfer everything)
```

### 3.2 Project-Level Reports (Focused)

```
Path: /Aliens/Report/Project/{ProjectID}/
Scope: ONLY that project's work — may include multiple CPUs
Use case: Project-specific handoff (transfer one project only)
```

### 3.3 Mixed (Description-Driven)

```
User can specify in Description:
- "Sirf ACLE project ka context load karo" 
  → Even if Cyborg path given, filter to ACLE-related data only
- "Sab kuch load karo except WebOS work"
  → Exclude WebOS-related entries from context
```

---

## [4] Report Reading Rules (STRICT)

### 4.1 Read-Only Guarantee

```
HARD RULE:
- VSCode_Chat workflow KISI BHI FILE ko create, modify, ya delete NAHI karega.
- Yeh workflow SIRF reports read karta hai aur in-memory context generate karta hai.
- No disk artifacts produced. No RunRecord (since nothing changes on disk).
- Outputs exist only in the VS Code chat session memory.
```

### 4.2 Large Report Handling

```
Agar source CPU me 100+ reports hain:
- Dashboard.md + Latest Monthly = ALWAYS full read
- Older reports = extract ONLY summary tables + key metrics
- Daily reports older than 30 days = extract ONLY header summary row
- This prevents context window overflow
```

### 4.3 Missing Report Types

```
Agar koi report type missing ho:
- Dashboard missing → WARNING, proceed with monthly/weekly/daily aggregation
- Yearly missing → OK, use monthly data
- Monthly missing → OK, use weekly data
- Weekly missing → OK, use daily data
- Daily missing → Only available dates used

NO report type is strictly mandatory — workflow adapts to whatever exists.
```

---

## [5] Integration with Report Workflow

### 5.1 Report → VSCode_Chat Pipeline

```
WORKFLOW PIPELINE:

CPU-A (outgoing CPU):
  1. Workflow('Report', 'C1001', 'Generate all missing reports')
     → All reports generated for C1001
     → Saved to /Aliens/Report/Cyborg/C1001/

CPU-B (incoming CPU):
  2. Workflow('VSCode_Chat', '/Aliens/Report/Cyborg/C1001', 'Load full context for handoff')
     → Reads C1001's reports
     → Generates in-memory context briefing
     → Agent now "knows" everything C1001 did
  
  3. Agent continues C1001's pending work seamlessly on CPU-B
```

### 5.2 Report Quality Dependency

```
VSCode_Chat output quality DIRECTLY depends on Report quality.
- Better reports → Better context → Better handoff
- Missing reports → Gaps in context → Possible re-work

This is why Report workflow enforces enterprise-grade report quality.
```

---

## [6] Source CPU ID Extraction

### 6.1 From Cyborg Path

```
Path: /Aliens/Report/Cyborg/{CyborgID}/
Extract: CyborgID from path segment
Example: /Aliens/Report/Cyborg/C1001/ → SourceCPU = "C1001"
```

### 6.2 From Project Path

```
Path: /Aliens/Report/Project/{ProjectID}/
Extract: ProjectID from path segment
Note: Project path may contain reports from MULTIPLE CPUs
      (each report has CyborgID suffix in filename)
      Agent must aggregate across CPUs or filter based on Description
```

### 6.3 Current CPU ID

```
Current CPU ID = /Aliens/manifest.json → Developer.Username
This is the CPU that is RECEIVING the context (the active CPU)
```

---

## [7] Description Processing Rules

### 7.1 Filter Keywords

Description may contain filter instructions:

| Keyword/Phrase | Effect |
|:---|:---|
| "full context" / "poora context" | Load Level 1 + 2 + 3 (everything) |
| "recent only" / "last month" | Load Level 1 + last 30 days only |
| "project {name}" | Filter context to that project only |
| "pending tasks" / "resume" | Focus extraction on pending/in-progress items |
| "velocity" / "performance" | Focus on metrics/analytics data |

### 7.2 Default (No Filter)

If Description has no specific filter keywords:
- Load Level 1 + Level 2 (Dashboard + Latest Month + Last 4 Weeks + Last 7 Days)
- Extract all data categories (projects, pending, velocity)

---

## [8] Error Handling

| Error Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_CHAT_PATH_INVALID` | Targets path does not exist | STOP — report "Path not found: {path}" |
| `ACC_ERR_CHAT_NO_REPORTS` | Path exists but no .md files | STOP — report "No reports found. Run Report workflow first." |
| `ACC_ERR_CHAT_DASHBOARD_MISSING` | Dashboard.md missing | WARNING — proceed with available reports |

---

## [9] Output Contract

### 9.1 What Gets Produced

```
Output Type: IN-MEMORY ONLY
- No files created on disk
- No RunRecord
- No planning artifacts
- Context exists ONLY in the VS Code chat session

The agent's "output" is its own UNDERSTANDING of the source CPU's work,
which it then uses to continue operating seamlessly.
```

### 9.2 Console Confirmation

After context loading completes, display:

```
═══════════════════════════════════════════════════════
  🔗 CPU HANDOFF CONTEXT LOADED
═══════════════════════════════════════════════════════

  Source:       {SourceCPU_ID}
  Current CPU:  {CurrentCPU_ID}
  Reports Read: {Count}
  
  📊 Context Summary:
  ─────────────────────
  Period:     {FirstDate} — {LastDate}
  Days:       {WorkingDays}
  Projects:   {ProjectCount}
  Pending:    {PendingCount} items detected

  ✅ Context loaded. Ready to continue {SourceCPU_ID}'s work.
  
═══════════════════════════════════════════════════════
```

---

## [10] Security & Safety

- Read-only operation — zero write risk
- No credentials/secrets extracted from reports
- No cross-workspace access (only local `/Aliens/Report/` folder)
- No network calls
- No terminal usage needed

---

## [11] Changelog

- 26.00.00 (2026-03-12): Initial version — CPU handoff workflow via report context loading, 3-level loading strategy, pending work detection, read-only guarantee.