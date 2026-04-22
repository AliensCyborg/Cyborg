---
WorkflowId: PyOS_Engine_Python_Update-Audit
WorkflowName: PyOS Engine Python Update (Audit)
Type: Workflow_Singular-Audit
ParentWorkflowId: PyOS_Engine_Python_Update
Domain: PyOS
Category: Engines
Language: Python
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Targets
  - Description
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/py/"
    FileName: "{EngineBaseName}.update.audit.md"
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only audit; engine file must exist
  ProdWrite: "no"             # audit writes only run record
  DbTouch: "not-required"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
  - "/Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.Cache.py"
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.Router.py"
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.AppRegistry.py"
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.WebAPI.py"
...

# PyOS_Engine_Python_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: **enterprise-grade audit** of an existing PyOS Python engine file before update.

Audit must be so complete that **koi bhi kami** (style / structure / safety / router contract / docs mismatch) miss na ho.

Output:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Deterministic Audit Artifact (hard):
- Also write a stable copy for downstream steps:
  - `/Aliens/.Alien/{Developer}/Planning/PyOS/Engine/py/{EngineBaseName}.update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code must not rely on a run-scoped path.

---

## [01] Target Normalization
Accepted Targets:
- `PyOS.Cache`
- `Cache` (normalized to `PyOS.Cache`)

Forbidden:
- `.py` extension in Targets
- Path traversal (`..`)

---

## [02] Resolve Engine File (ACC.Paths compliant)
Resolve Version: latest (fallback `Py.26.00.00`).

Resolved engine file:
- `/Aliens/PyOS/{Version}/Engine/{EngineBaseName}.py`

Hard rule:
- If engine file missing => fail with `WF_TARGET_MISSING`.

---

## [03] Audit Scope (Hard)
Audit MUST cover (minimum):

### A) AliensStyle Compliance
- First line copyright marker (mandatory for PyOS engines)
- Enterprise CodeHeader block:
  - Version / Author / Location
  - AliensStyle version
  - LastModified UTC
  - PyOS version tag
  - Dependencies list (real engines used)
- CodeFooter block present
- PascalCase compliance for class/methods/helpers
- Section divider above every public method
- Method internal code-header (Purpose/Input/Output)
- Logger guard exists (never assume `d()`/`x()` exist)

### A.1) Must-Fix Items (User-Reported; Non-Negotiable)
Audit MUST explicitly detect and report these (with exact file+line references where possible):
1) File CodeHeader `Author` must be exactly: `AlienCyborg`
2) File CodeFooter missing
3) Function-level CodeHeader missing (Purpose/Input/Output)
4) Sections missing / not consistent
5) `$echo` and `d()/x()` usage not per PyOS convention OR not guarded
6) Comments not meaningful / not structured (no noise, no missing intent)
7) AliensStyle not applied (minimum checklist fail)
8) Engine is too small / not enterprise-grade (missing helpers, missing actions, missing coverage inventory)

### B) EngineRouter Contract
- File naming: `PyOS.{Short}.py`
- Class naming: `PyOS_{Short}`
- `public static function Main(array $Payload, array $Context)` exists
- Public actions (if any) are `public static` with same signature
- Numeric payload passing compatibility acknowledged

### C) Safety / Quality
- Defensive validation of payload
- Deterministic error model (codes/messages) for Main + major actions
- No fatal assumptions; safe returns
- No forbidden includes / side-effect requires
- No secret leakage in logs

### D) Coverage Gaps
- Best-effort discovery:
  - Search for call patterns: `PyOS::{EngineShortName}{Action}(` across repo (read-only)
- List missing actions vs existing methods
- Identify backward-compat return-type expectations (bool/string/array envelope)

Enterprise size/coverage expectation (hard):
- If coverage discovery finds expected actions but methods are missing OR implemented as stubs, mark as **Critical**.
- If engine lacks the enterprise helper baseline (logging guard, payload arg getter, deterministic error model helpers), mark as **High**.

### E) Docs Drift
- Existing docs file present? `/Aliens/Docs/PyOS/Engine/py/{EngineBaseName}.md`
- If present: detect mismatch in method list / behavior notes (best-effort)

---

## [04] Audit Output Template (Mandatory)
Audit.md MUST include (order fixed):
1) Header (EngineName, Version, RunId/RunItemId)
2) File Paths (engine path + docs path)
3) Summary Score (Pass/Fail + risk)
4) Missing / Violations List (grouped: AliensStyle, Router, Safety, Coverage, Docs)
5) Required Fix List (explicit checklist)
6) Non-Destructive Notes (what must NOT change)
7) Approval Note (if ApprovalMode=manual)

Hard rule:
- Audit cannot be a shallow checklist; it must list concrete missing items.

Hard rule:
- The Required Fix List MUST include a dedicated section: `User Reported Issues (8)` and must address each item (Pass/Fail + fix).

---

## [05] Minimal Success Criteria
- Audit.md exists at RunRecord path.
- If pass: explicitly states "Ready for update".
- If fail: explicitly states "Do not update until fixed" with a checklist.

Plus (hard):
- `{EngineBaseName}.update.audit.md` exists at deterministic Planning path.
