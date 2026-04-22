---
WorkflowId: Report_Alien
Title: "Alien (Human Team Member) Consolidated Reports"
Category: System
Type: Workflow_Plural
Domain: Report
Scope: "Generate BI reports for a human team member (Alien) — merged manual work + controlled Cyborgs."

Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-19

Intent:
  Summary: >
    Yeh workflow kisi Alien (human team member) ki consolidated reports banata hai.
    Alien ki apni manual activity PLUS us Alien ke under control Cyborgs ka merged activity
    ek Dashboard me aata hai. Har Cyborg-sourced entry par 'SourceCyborgID' flag mandatory.
  WhenToUse:
    - "Kisi Alien (e.g. Himanshu) ki team-level consolidated BI chahiye"
    - "Alien ke under ke Cyborgs ka combined output dekhna ho"
  SiblingWorkflows:
    - "Report          → Single-Cyborg activity reports"
    - "Report_Project  → ACC Planner projects reports"
    - "Report_Repo     → Repos BI + in-repo Dashboard"

Inputs:
  Targets:
    Accepted:
      - "Email form          → 'alias@aliens.company' or 'alias@aliens.id'  (direct)"
      - "CyborgID form       → 'AC0001' / 'C1072'  (reverse-lookup to the controlling Alien)"
      - "Alias form          → 'himanshu'  (shorthand; manifest lookup)"
    Examples:
      - "himanshu@aliens.company"
      - "himanshu@aliens.id"
      - "himanshu"
      - "AC0001"
    Validation:
      - "Exactly 1 target allowed"
      - "Resolve to a single Alien record in manifest.Team.Aliens[]"
      - "Unresolvable => ACC_ERR_REPORT_ALIEN_UNKNOWN"
  Description:
    Format: "Any human language"

Defaults:
  ReportFormat: "md"
  ExcludeFromScan:
    - ".git"
    - "node_modules"
    - "__pycache__"
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
    - "/Aliens/manifest.json  (Team.Aliens[], Team.Cyborgs[])"
    - "/Aliens/Report/Cyborg/{CyborgID}/* (prerequisite)"
    - "/Aliens/.Alien/{alias}/**"
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
    Root: "/Aliens/Report/Alien/{alias}/"
    Files:
      - "Daily_Report_YYYY-MM-DD.md"
      - "Weekly_Report_YYYY-WNN.md"
      - "Monthly_Report_YYYY-MM.md"
      - "Yearly_Report_YYYY.md"
      - "Dashboard.md"

ErrorCodes:
  ACC_ERR_REPORT_ALIEN_UNKNOWN: "Target could not be resolved to an Alien in manifest.Team.Aliens[]"
  ACC_ERR_REPORT_ALIEN_NO_CYBORGS: "Alien has no ControlsCyborgs[] entries AND no manual activity"
  ACC_ERR_REPORT_CYBORG_REPORTS_MISSING: "Required per-Cyborg reports do not exist yet (run 'Report' for each Cyborg first)"
  ACC_ERR_REPORT_NO_DATA: "No activity found (manual or Cyborg-merged)"
  ACC_ERR_REPORT_SCAN_FAILED: "Scan error"
  ACC_ERR_DESCRIPTION_REQUIRED: "Description empty / missing"
---

# Report_Alien Workflow

## [0] Purpose

Aliens Company ke **human team members** (Aliens) ki consolidated BI reports.

Report me combine hota hai:
1. Alien ki **manual** activity (reviews, approvals, manual commits, decisions)
2. Alien ke under control ke **Cyborgs** ki activity — har entry par `SourceCyborgID` flag (audit / attribution integrity)

Reference: `Cyborg/AnilCyborg/Ecosystem/Members.Aliens_vs_Cyborgs.md`

### Call Signatures

```
Workflow( 'Report_Alien', 'himanshu@aliens.company', 'Generate all missing reports' )
Workflow( 'Report_Alien', 'himanshu@aliens.id',      'Generate all missing reports' )
Workflow( 'Report_Alien', 'himanshu',                'Generate all missing reports' )
Workflow( 'Report_Alien', 'AC0001',                  'Generate all missing reports' )
# (AC0001 → reverse-lookup to the Alien who controls this Cyborg)
```

---

## [1] Universal Includes

1) `Workflows/ACC/_Common/Workflow_Universal.md`
2) `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [2] Child Chain

1) `Report-Planning` — Alien + Cyborgs merged data discovery
2) `Report-Code` — Report files generate
3) `Report-Documentation` — (Optional) index update

---

## [3] Target → Alien Resolution (Dispatch)

Order (first match wins):

```
1. Target matches /^[^@\s]+@aliens\.(company|id)$/
   → Email form
   → Find manifest.Team.Aliens[*] where Aliens[i].email[*] == Target
   → Extract alias + ControlsCyborgs[]

2. Target matches CyborgID pattern (no slash, no @)
   AND Target exists in manifest.Team.Cyborgs[] OR matches Developer.CyborgID
   → CyborgID form (reverse lookup)
   → Find the Alien whose ControlsCyborgs[] contains this CyborgID
     (if Cyborg record has 'controlledBy', use that directly)
   → Extract alias + ControlsCyborgs[]

3. Target is a bare token (lowercase alphanumeric, no @, no slash)
   → Alias form
   → Find manifest.Team.Aliens[*] where Aliens[i].alias == Target
   → Extract alias + ControlsCyborgs[]

4. Otherwise
   → STOP with ACC_ERR_REPORT_ALIEN_UNKNOWN
```

**Output `alias`** is always the manifest-declared `alias` (local-part of primary email).

---

## [4] Manifest Shape (Reference)

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
    ],
    "Cyborgs": [
      { "id": "C1001", "type": "AlienCyborg", "controlledBy": "himanshu@aliens.company" }
    ]
  }
}
```

---

## [5] Output Location

```
/Aliens/Report/Alien/{alias}/
├── Daily_Report_YYYY-MM-DD.md
├── Weekly_Report_YYYY-WNN.md
├── Monthly_Report_YYYY-MM.md
├── Yearly_Report_YYYY.md
└── Dashboard.md
```

---

## [6] Data Sources (Merged)

### 6.1 Manual (Alien's direct work)

```
/Aliens/.Alien/{alias}/**
```
- Reviews, approvals, manual commits, decisions
- Labelled as `Source: Manual`

### 6.2 Cyborg-sourced (merged)

For each CyborgID in `ControlsCyborgs[]`:
```
/Aliens/Report/Cyborg/{CyborgID}/*     (must exist as prerequisite)
```
- Labelled as `Source: Cyborg {CyborgID}`
- If per-Cyborg reports do not exist: warn OR STOP with `ACC_ERR_REPORT_CYBORG_REPORTS_MISSING`
  depending on Description directive (default: WARN + proceed if at least 1 Cyborg has reports)

---

## [7] Attribution Ethics (STRICT — NON-NEGOTIABLE)

Every merged entry MUST carry source:

| Source | Label |
|:---|:---|
| Manual by the Alien | `Source: Manual` |
| From Cyborg X | `Source: Cyborg C1001` |

**FORBIDDEN:** Cyborg-done kaam ko "Manual by Alien" present karna.

This rule applies to every table row, every chart entry, every quoted artifact.

---

## [8] Report Content Requirements

### Common

```
1. Header: Alien name, alias, email, role, ControlsCyborgs list
2. Executive KPI card (Alien-level rollup)
3. Manual vs Cyborg split (pie) — absolute counts + %
4. Per-Cyborg contribution breakdown (table)
5. Workstream distribution (pie)
6. Peak days + velocity trends (xychart-beta)
7. Attribution integrity check — 100% of Cyborg entries flagged
8. Top achievements (both manual + Cyborg, each labelled)
9. Risks / backlog / gaps
10. Next actions (P0 / P1 / P2)
11. Glossary + end marker
```

### Daily / Weekly / Monthly / Yearly specifics

- Scoped to the respective period.
- Same attribution labels in every table.
- Week-over-week / month-over-month comparisons for the Alien overall AND broken down by source (Manual / per-Cyborg).

### Dashboard

- Always regenerated.
- Lifetime view.
- Alien-scoreboard + per-Cyborg-sub-scoreboards.

---

## [9] Prerequisites & Ordering

Recommended run order:
1. `Workflow('Report', '{CyborgID}', …)` for **each** CyborgID in `ControlsCyborgs[]`
2. Then `Workflow('Report_Alien', '{alias-or-email}', …)`

If per-Cyborg reports are stale/missing, Alien report quality degrades proportionally.

---

## [10] Terminal Policy

Allowed ONLY for: merging existing report data, filesystem listing of `.Alien/{alias}/**`, JSON parsing of manifest. Scripts location: `/Aliens/Cyborg/AnilCyborg/Code/`.

---

## [11] Errors

| Code | Condition | Action |
|:---|:---|:---|
| `ACC_ERR_REPORT_ALIEN_UNKNOWN` | Target cannot be resolved | STOP |
| `ACC_ERR_REPORT_ALIEN_NO_CYBORGS` | No ControlsCyborgs AND no manual activity | STOP |
| `ACC_ERR_REPORT_CYBORG_REPORTS_MISSING` | Per-Cyborg reports missing (strict mode) | STOP |
| `ACC_ERR_REPORT_NO_DATA` | No activity | STOP |
| `ACC_ERR_REPORT_SCAN_FAILED` | Scan error | STOP |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description missing | STOP |

---

## [12] Execution Summary

```
Workflow( 'Report_Alien', '{Target}', '{Description}' )

Step 1: Resolve Target → Alien (Section [3])
Step 2: Validate ControlsCyborgs[] + per-Cyborg reports existence
Step 3: Report-Planning (manual + Cyborgs merged discovery)
Step 4: Report-Code (generate missing reports with attribution)
Step 5: Report-Documentation (optional)
Step 6: Save RunRecord
```

---

## [13] Sibling Workflows

| Scope | Workflow |
|:---|:---|
| Single Cyborg activity | `Report` |
| ACC Planner projects | `Report_Project` |
| Repos BI + in-repo Dashboard | `Report_Repo` |

---

## [14] Changelog

- **26.00.00** (2026-04-19): Initial version. Split from monolithic `Report` v26.01.00. 3 target forms (email / CyborgID reverse-lookup / alias). Strict attribution ethics.
