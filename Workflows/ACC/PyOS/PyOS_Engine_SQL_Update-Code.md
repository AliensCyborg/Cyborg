---
WorkflowId: PyOS_Engine_SQL_Update-Code
WorkflowName: PyOS Engine SQL Update (Code)
Type: Workflow_Singular-Code
ParentWorkflowId: PyOS_Engine_SQL_Update
Domain: PyOS
Category: Engines
Language: SQL
Scope: "One Engine -> SQL file (Update)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Target: "Single engine target. Example: PyOS.Cache"
  - Description: "Human update requirement"
Outputs:
  - SqlFile: "/Aliens/PyOS/{Version}/unit/sql/{EngineBaseName}.sql"
  - RunRecord: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
Consumes:
  - PlanFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/{EngineBaseName}.update.plan.md"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
  - "Workflows/ACC/_Refs/AliensStyle.md"
  - "Workflows/ACC/_Refs/AliensGrade.md"
  - "/Aliens/manifest.json"
Quality:
  MustFollow:
    - AliensStyle
    - AliensGrade
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only
  ProdWrite: true
  DbTouch: "not-required"  # SQL file only; not executing DB
Approvals:
  Mode: "From /Aliens/manifest.json"
  ManualIf:
    - "ProdWrite && manifest.ApprovalMode == manual"
    - "Plan includes destructive changes"
ExampleFiles:
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.AI.Log.sql"
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.Analytics.Cohort.sql"
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.AI.Vector.sql"
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.AI.Prompt.sql"
...

# PyOS_Engine_SQL_Update-Code

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Goal
Existing SQL engine file ko plan ke hisaab se **enterprise-grade update** karna.

Hard constraints:
- Target SQL file MUST already exist.
- Only the single target SQL file may be modified.
- No destructive changes by default.

---

## [01] Pre-Checks
1) Manifest permissions + approvals.
2) Target normalization (same as planning).
3) Resolve Version + output path:
   - `/Aliens/PyOS/{Version}/unit/sql/{EngineBaseName}.sql`
4) If file missing => fail with `WF_TARGET_MISSING`.

---

## [02] Load Plan (Mandatory)
Read:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/{EngineBaseName}.update.plan.md`

If plan missing:
- Fail (no guessing). Suggest running Update-Planning first.

Also read deterministic audit artifact (required):
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/{EngineBaseName}.update.audit.md`

If audit artifact missing:
- Fail fast (update process integrity). Suggest running Update-Audit first.

---

## [03] Update Strategy (Hard)
The update MUST:
1) Fix audit-detected style/safety/idempotency gaps
2) Keep SQL non-destructive unless explicitly requested + approved
3) Preserve existing sections and structure; apply changes minimally
4) Add/adjust indexes safely with clear why-comments
5) Maintain re-run safety (idempotent)

Allowed (default):
- CREATE TABLE IF NOT EXISTS
- Add indexes
- Idempotent seed/upserts when plan asks

Forbidden (default):
- DROP/TRUNCATE
- Destructive mass updates

---

## [04] Quality Gate
Before saving:
- Matches plan scope
- No forbidden statements unless explicitly approved
- Sections + header comment intact
- Changelog updated

Additional hard gates (must):
- Header `Author` must be exactly `AlienCyborg`
- Header must be a single top-of-file block comment in this exact shape:

/*
Engine: {EngineBaseName}
Author: AlienCyborg
PyOS: {Version}
Purpose: <1-2 lines from Description/Plan>
Safety: <non-destructive by default; call out risks>
Compatibility: <DB dialect or UNKNOWN>
*/

- Section markers must use `[SECTION] ...` and must be consistent across file.
- Minimum sections for update (unless explicitly N/A in plan):
  - [SECTION] TABLES
  - [SECTION] INDEXES
  - [SECTION] CHANGELOG (must be last; acts as footer)
- CHANGELOG section must exist and include a clear update note (what changed + why).
- Comments must be meaningful (why for indexes/constraints; no noise).
- `$echo` and `d()` tokens must not appear anywhere in SQL (cross-language contamination).

---

## [05] Done Criteria
- SQL file updated at output path
- No unrelated files modified
- Plan and audit items addressed (or explicitly deferred)
