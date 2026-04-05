---
WorkflowId: WebOS_Engine_SQL-Documentation
WorkflowName: WebOS Engine SQL - Documentation
Type: Workflow_Singular-Documentation
Domain: WebOS
Category: Engines
Language: SQL
Scope: "One Engine -> Docs"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
Inputs:
  - Target: "Single engine target. Example: WebOS.Cache"
  - Description: "Requirement (human language)"
Outputs:
  - DocFile: "/Aliens/Docs/WebOS/Engine/SQL/{EngineBaseName}.md"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
Consumes:
  - PlanFile: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/{EngineBaseName}.plan.md"
  - SqlFile: "/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql"
...

## [00] Goal
Target engine ke liye documentation generate karni hai, taaki:
- future me agent/employee/human ko clear ho: SQL file ka purpose kya hai
- kaise use/run karna hai
- kya schema changes hain
- safety constraints kya hain
- upgrade path kya hai

## [00A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if SQL/plan info missing
- Security/Performance/Observability/Testing sections required

## [01] Pre-Checks
- Manifest permissions (Docs write allowed)
- Target normalization
- Resolve Version and paths

## [02] Inputs to read
Read (if exists):
1) Plan
- `/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/{EngineBaseName}.plan.md`

2) SQL file
- `/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql`

If any required input is missing:
- Error (do not invent).

## [03] Documentation Template
Doc MUST follow the **19 mandatory sections** defined in `Workflow_Singular-Documentation`.

Title Block (Section-1) MUST explicitly include:
- Author: AlienCyborg
- Author URI / Website: [AlienCyborg.com](https://AlienCyborg.com)

Recommended (enterprise traceability):
- Owner/Team (who maintains this engine)
- Status (Draft/Stable)
- Output Doc Path (same as workflow output)
- Source paths used (SQL + Plan)

Additionally (SQL engine specific), include:

1) Overview
- Engine name
- What problem it solves (from Description / Plan)
- Target paths (SQL file path + where it lives in WebOS)

2) What it creates/changes
- Tables list (names + short purpose)
- Indexes list (names + why)
- Views/procs (if any)

3) How to run
- Intended execution environment (migration runner / manual DBA / installer)
- Order-of-operations notes (if depends on other SQL)
- Safe re-run notes (idempotency)

4) Safety & Rollback
- What is safe by default
- What is dangerous / destructive (if any)
- Rollback guidance (if possible) or “manual rollback required”

5) Performance Notes
- Indexing strategy
- Expected heavy queries
- Large-table considerations

6) Compatibility
- WebOS version compatibility
- DB dialect assumptions

7) Testing Checklist
- Minimal smoke checks
- Staging validation items

## [04] Save Output
Write:
- `/Aliens/Docs/WebOS/Engine/SQL/{EngineBaseName}.md`

Rules:
- Create directories under `/Aliens/Docs/` if missing.
- Only write this one doc file for this target.
- Never edit unrelated docs.

## [05] Done Criteria
- Doc file exists
- Content matches SQL + Plan
- No extra content beyond scope

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---
