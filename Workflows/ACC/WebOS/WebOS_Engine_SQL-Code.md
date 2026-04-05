---
WorkflowId: WebOS_Engine_SQL-Code
WorkflowName: WebOS Engine SQL - Code
Type: Workflow_Singular-Code
Domain: WebOS
Category: Engines
Language: SQL
Scope: "One Engine -> SQL file"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
Inputs:
  - Target: "Single engine target. Example: WebOS.Cache"
  - Description: "Requirement (human language)"
Outputs:
  - SqlFile: "/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "Workflows/ACC/_Refs/AliensStyle.md"
  - "Workflows/ACC/_Refs/AliensGrade.md"
Consumes:
  - PlanFile: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/{EngineBaseName}.plan.md"
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Log.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.Analytics.Cohort.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Vector.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Prompt.sql"
...
## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Goal
Planning workflow ke output (plan) ke hisaab se:
- Target engine ki SQL file create/update karni hai
- WebOS folder rules follow karne hain
- Secure + idempotent SQL prefer karna hai
- Existing important parts ko remove/change nahi karna (unless Description/Plan me explicit)

## [01] Pre-Checks
1) Manifest permissions
- WebOS scope allowed hona chahiye
- Code modify allowed hona chahiye
- Approvals (Auto/Manual) ka rule follow hona chahiye

2) Target normalization
- same rules as Planning

3) Resolve Version + output path
- `/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql`

If any pre-check fails:
- Stop with clear error.

## [02] Load Plan
Read:
- `/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/{EngineBaseName}.plan.md`

If plan missing:
- Error (no guessing). Suggest running Planning workflow first.

## [03] Reference Examples (For Style Only)
Pick 2-4 existing SQL engine files from:
- `/Aliens/WebOS/{Version}/unit/sql/`

Purpose:
- comment/header style
- section naming
- schema naming conventions
Hard rule:
- Do not copy irrelevant SQL; only learn pattern.

## [04] SQL File Structure (AliensStyle for SQL)
Generated SQL file must be structured and auditable.

Recommended layout (must follow section markers exactly):

1) Header Block Comment (must)
Use a single top-of-file block comment in this shape:

/*
Engine: {EngineBaseName}
Author: AlienCyborg
WebOS: {Version}
Purpose: <1-2 lines from Description/Plan>
Safety: <non-destructive by default; call out risks>
Compatibility: <DB dialect or UNKNOWN>
*/

Hard gate (must):
- Header must include `Author: AlienCyborg` exactly.

2) Sections (with clear rulers)
- [SECTION] PREREQUISITES (optional)
- [SECTION] TABLES
- [SECTION] INDEXES
- [SECTION] VIEWS (optional)
- [SECTION] DATA_SEED (optional)
- [SECTION] CHANGELOG (must be last)

Hard gates (must):
- CHANGELOG section must exist and must be the final section (acts as footer)
- If plan exists OR Description is non-empty: avoid “tiny” SQL; include required sections and evidence-based change inventory

Rules:
- Use consistent section markers (as per AliensStyle)
- Keep “why” comments for each non-trivial index/constraint

Forbidden (hard):
- `$echo` and `d()` tokens must not appear anywhere in SQL (cross-language contamination).

## [05] Safe SQL Rules (Default)
Unless Plan explicitly allows destructive changes:

Allowed by default:
- CREATE TABLE IF NOT EXISTS
- ADD INDEX (with safe names)
- ADD COLUMN only if DB supports IF NOT EXISTS; otherwise write guarded SQL + note
- Data inserts that are idempotent (INSERT IGNORE / ON DUPLICATE KEY / upsert) only if plan asks

Forbidden by default:
- DROP TABLE / DROP COLUMN
- TRUNCATE
- mass UPDATE without WHERE
- long blocking ALTER on huge tables (must be planned)

## [06] Update Strategy (If file already exists)
If SQL file exists:
- Preserve existing structure
- Apply changes as additive sections
- If must change existing lines, do it minimally and keep a changelog note

If SQL file does not exist:
- Create new file at resolved path
- Still must follow AliensStyle structure

## [07] Write Output
Write/update:
- `/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql`

Rules:
- Never write outside this path.
- Never touch DB directly in this workflow (SQL execution is a different workflow, if ever).
- Never modify any other engine files.

## [08] Quality Gate (AliensGrade)
Before saving final:
- Validate it matches plan scope
- Validate no forbidden statements exist (unless explicitly approved)
- Validate naming conventions
- Validate idempotency notes exist
- Validate file is readable (sections + comments)

## [09] Done Criteria
- SQL file exists at declared path
- Content matches plan and user description
- No extra files generated
- No external side effects
