---
WorkflowId: PySDK_Module_Class_Update-Audit
WorkflowName: PySDK Module Class Update (Audit)
Type: Workflow_Singular-Audit
ParentWorkflowId: PySDK_Module_Class_Update
Domain: PySDK
TargetKind: "Module/Class"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Inputs:
  - Targets
  - Description
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{ModuleName}/"
    FileName: "Class.Update.audit.md" # module-scoped default; see file-scoped rule below
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only audit; class file must exist
  ProdWrite: "no"              # audit writes only run record + deterministic artifact
  DbTouch: "not-required"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Audit.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "Workflows/ACC/_Refs/PySDK.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
  - "/Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Student/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Doctor/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Book/py/index.py"
...

# PySDK_Module_Class_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: **enterprise-grade audit** of an existing PySDK module class file (`Python/index.py`) before update.

Output (run-scoped):
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Deterministic Audit Artifact (hard):
- Also write a stable copy for downstream steps:
  - `/Aliens/.Alien/{Developer}/Planning/PySDK/Module/{ModuleName}/Class.Update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code/Docs must not rely on a run-scoped audit path.

---

## [01] Targets
Accepted targets:
- `ModuleName` OR `ModuleName/FileBase` (single target per singular call)

Target meaning (SSOT; Update scope):
- `ModuleName` => audit ALL Python class files under module:
  - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/*.py`
  - (minimum required coverage: `index.py` + `_Trait.py` (if exists) + `{ModuleName}_*.py` files)
- `ModuleName/FileBase` => audit ONLY:
  - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/{FileBase}.py`

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Any table names discovered during audit should be validated as singular (report violation).
- If violated => `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Forbidden:
- More than one slash, extensions in FileBase, path traversal (`..`), dots, backslashes

Validation rules:
- `ModuleName` and `FileBase` MUST match: `^[A-Za-z0-9_]+$`
- `FileBase` MUST NOT include `.py`

---

## [02] Resolve paths (ACC.Paths compliant)
Resolve Version: latest (fallback `Py.26.00.00`).

Paths:
- `{ModuleRoot} = /Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/`
- If target is `ModuleName`:
  - `{PhpRoot} = {ModuleRoot}/py/`
  - `{ClassFiles} = {PhpRoot}/*.py` (enumerate files; deterministic ordering: `index.py` first, then `_Trait.py`, then remaining sorted)
- If target is `ModuleName/FileBase`:
  - `{PhpRoot} = {ModuleRoot}/py/`
  - `{ClassFile} = {PhpRoot}/{FileBase}.py`
- Optional: `{ModuleSql} = {ModuleRoot}/sql/index.sql`

Hard rule:
- If target is `ModuleName` and `{PhpRoot}` missing OR no `.py` files => fail with `WF_TARGET_MISSING`.
- If target is `ModuleName/FileBase` and `{ClassFile}` missing => fail with `WF_TARGET_MISSING`.

Deterministic artifact naming (NON-NEGOTIABLE):
- If target is `ModuleName` => write:
  - `/Aliens/.Alien/{Developer}/Planning/PySDK/Module/{ModuleName}/Class.Update.audit.md`
- If target is `ModuleName/FileBase` => write:
  - `/Aliens/.Alien/{Developer}/Planning/PySDK/Module/{ModuleName}/Class.Update.{FileBase}.audit.md`

---

## [03] Audit scope (Hard)
Audit MUST cover (minimum):

### A) Canonical skeleton + ordering
- Exactly one `# Python:` at file start; no duplicate open tags; no `` close tag
- `declare(strict_types=1);` then `Access();` (when safely possible)
- Bootstrap logs exist
- `import('...')` usage consistent with ecosystem
- `$Python['{Module}']` metadata + `$Python['{Module}']['function'][]` manifest present/consistent (best-effort)

### B) AliensStyle Compliance
- CodeHeader block present and meaningful
- CodeFooter present (changelog/version footprint)
- SECTION divider above every public method
- Method internal `[CODEHEADER]` blocks present
- Start/Finish logs per method (Completed/Failed)

### C) Safety / quality
- No secrets in logs or comments
- English-only developer content
- DB prefix law: detect hardcoded prefixed table names

### D) User Reported Issues (8) (Non-Negotiable)
Audit MUST explicitly detect and report these (Pass/Fail + fix):
1) File CodeHeader `Author` must be exactly: `AlienCyborg`
2) File CodeFooter missing
3) Function-level CodeHeader missing (Purpose/Input/Output)
4) Sections missing / not consistent
5) `$echo` and `d()` usage not per convention OR unsafe leaks
6) Comments not meaningful / not structured
7) AliensStyle not applied
8) File too small / not enterprise-grade coverage

### E) Large File / Modularization Recommendation (SSOT)
Audit MUST detect if modularization is recommended to reduce AI + developer friction.

Minimum checks (best-effort; do not fail run only due to heuristic):

1) Module Type detection (SSOT evidence):
- If the class/method/table family indicates plural units (any of these is enough):
  - A public method matches: `^{ModuleName}_[A-Za-z0-9]+`
  - SQL (if present) contains any table matching: `^{ModuleName}_[A-Za-z0-9]+$`
  - Module Python folder contains any `{ModuleName}_*.py` files
  => Set `ModuleType=PluralModule`.
- Else => `ModuleType=SingularModule`.

2) Large file heuristic (SingularModule only):
- If `Python/index.py` is `> 1500 lines` OR has `> 80 public methods` OR file size `> 200 KB` => set `PartitionRecommendation=RECOMMENDED`.

3) Method-prefix grouping heuristic (for parts discovery):
- Detect methods matching: `^{ModuleName}_[A-Za-z0-9]+`
- Group by first token after `{ModuleName}_` (e.g., `User_AddressNew` => `Address`).

Audit output MUST include:
- `ModuleType`: `SingularModule | PluralModule`
- `PartitionRecommendation`: `NOT_NEEDED | OPTIONAL | RECOMMENDED`
- `DetectedParts[]`: list of suggested parts (e.g., `Address`, `Meta`)
- `DetectedMethodsByPart` (summary counts only; do not dump full code)

Rules:
- If `ModuleType=PluralModule` then `PartitionRecommendation` MUST be at least `RECOMMENDED` (plural units should live in separate part files; modular layout is structural).

Deterministic artifact requirement:
- The deterministic audit artifact MUST include the above fields so Update-Planning can decide whether to generate a `PartitionMigrationPlan`.
  - Module target: `Class.Update.audit.md`
  - File target: `Class.Update.{FileBase}.audit.md`

---

## [04] Output template (Mandatory)
Audit.md MUST include (order fixed):
1) Header (ModuleName, Version, RunId/RunItemId)
2) Resolved paths
3) Summary score (Pass/Fail + risk)
4) Findings (Critical/High/Medium/Low)
5) Required fix list (checklist)
6) Non-destructive notes (what must NOT change)
7) Approval note (if ApprovalMode=manual)

Hard rule:
- The Required Fix List MUST include a dedicated section: `User Reported Issues (8)`.

---

## [05] Minimal success criteria
- Audit.md exists at run record path.
- Deterministic audit artifact exists at Planning path (module or file-scoped, per target).
