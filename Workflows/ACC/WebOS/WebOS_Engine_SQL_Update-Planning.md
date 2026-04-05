---
WorkflowId: WebOS_Engine_SQL_Update-Planning
WorkflowName: WebOS Engine SQL Update Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: WebOS_Engine_SQL_Update
Domain: WebOS
Category: Engines
Language: SQL
Scope: "One Engine -> Planning (Update)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  Required:
    - Target: "Single engine target. Example: WebOS.Cache"
  Optional:
    - Description
Outputs:
  PlanFile:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/"
    FileName: "{EngineBaseName}.update.plan.md"
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only planning requires existing SQL file
  ProdWrite: "planning-only"
  DbTouch: "planning-only"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "/Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Log.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.Analytics.Cohort.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Vector.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Prompt.sql"
...

# WebOS_Engine_SQL_Update-Planning

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## [00] Purpose
Is workflow ka kaam: **sirf update planning generate** karna for an existing WebOS SQL engine.

Output:
- `/Aliens/.Alien/{Developer}/Planning/WebOS/Engine/SQL/{EngineBaseName}.update.plan.md`

Hard rule:
- SQL file must already exist; if missing => fail.

---

## [01] Evidence Pack (Update-specific)
Planning MUST read (best-effort):
1) Existing SQL file:
   - `/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql`
2) Deterministic Audit Artifact (required):
  - `/Aliens/.Alien/{Developer}/Planning/WebOS/Engine/SQL/{EngineBaseName}.update.audit.md`
3) Existing docs (if present):
   - `/Aliens/Docs/WebOS/Engine/SQL/{EngineBaseName}.md`

If any evidence missing:
- Mark explicitly "Not found"; do not guess.

Hard rule:
- If deterministic audit artifact is missing => fail fast (update process integrity).

---

## [02] Planning Output Template (Mandatory Sections)
Plan MUST include (order fixed):
1) Overview (what & why)
2) Inputs (target + description)
3) Resolved Context (Developer, ApprovalMode, Version)
4) Current-State Summary (from SQL + audit)
5) Audit Findings (copy key gaps + severity)
6) Update Goals (measurable)
7) Step-by-Step Plan (10–15 steps)
8) Safety & Idempotency Strategy (non-destructive defaults)
9) Schema/Index Change List (evidence-based)
10) Compatibility + DB Dialect Assumptions (or UNKNOWN)
11) Files & Outputs (exact paths)
12) Validation Checklist (static review, staging checks)
13) Rollback Plan (non-destructive)

---

## [03] Enterprise Update Planning (Hard)
- Plan MUST list which audit items will be fixed in code step.
- Plan MUST state: no unrelated files modified.
- Plan MUST call out any destructive change as "Explicitly Requested" + "Requires Approval".

Hard rule (User issues):
- Plan MUST include `User Reported Issues (SQL-adapted)` mapped to concrete SQL changes (header/sections/changelog/comments/completeness).

---

## [04] Minimal Success Criteria
- Update plan file exists at expected path.
- Includes Safety/Idempotency strategy + Change List.
