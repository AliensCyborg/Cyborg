---
WorkflowId: PyOS_Engine_Python-Code
WorkflowName: PyOS Engine Python Generator (Code)
Type: Workflow_Singular-Code
ParentWorkflowId: PyOS_Engine_Python
Domain: PyOS
Scope: "Engine -> Python"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-27
Inputs:
  - Targets: "Single Engine (recommended). Example: PyOS.Cache"
  - Description: "Human requirement in any language"
Outputs:
  - Code: "/Aliens/PyOS/Python.{Latest}/Engine/{EngineBaseName}.py"
  - RunRecord: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
  - "/Aliens/manifest.json"
Quality:
  MustFollow:
    - AliensStyle
    - EngineRouter v7.0 compliance
Safety:
  NoDelete: true
  MissingTargetPolicy: "create-or-update"
  ProdWrite: true
  DbTouch: "not-required"
Approvals:
  Mode: "From /Aliens/manifest.json"
  ManualIf:
    - "ProdWrite && manifest.ApprovalMode == manual"
    - "Existing engine is LegacyNonCompliant"
ExampleFiles:
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.Account.py"
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.Agent.py"
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.API.py"
  - "/Aliens/PyOS/Py.26.00.00/Engine/PyOS.Cache.py"
...

# PyOS_Engine_Python-Code

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Purpose
Is workflow ka kaam: **PyOS Python Engine file generate/update** karna in a router-compatible way.

Target example:
- `Workflow("PyOS_Engine_Python", "PyOS.Cache", "Add Get/Set/Delete actions..." )`
- `Workflow("PyOS_Engine_Python", "PyOS.Troubleshoot", "Describe required behavior, logs, edge-cases (required)" )`

Output example:
- `/Aliens/PyOS/Py.26.00.00/Engine/PyOS.Cache.py`

---

## [01] Non-Negotiables
- NoDelete: kisi bhi file ko delete nahi karna.
- Strict scope: only the single resolved engine file should be created/updated.
- Boot/PyOS.EngineRouter.py ko modify karna **forbidden** in this workflow.
- Default engine type: **EngineRouterStatic**.
- AliensStyle MUST be applied fully (see [05]).

---

## [02] Target Normalization
Accepted Targets:
- `PyOS.Cache`
- `Cache` (normalized to `PyOS.Cache`)

Validation:
- EngineShortName must match: `^[A-Za-z][A-Za-z0-9]*$`
- Target must not contain `.py`.

Resolved names:
- EngineBaseName = `PyOS.{EngineShortName}`
- FileName = `{EngineBaseName}.py` → example: `PyOS.Cache.py`
- ClassName = `PyOS_{EngineShortName}` → example: `PyOS_Cache`

---

## [03] Path Resolution (ACC.Paths compliant)
Resolve Version: latest (fallback `Py.26.00.00`).

Write target file:
- `/Aliens/PyOS/{Version}/Engine/{EngineBaseName}.py`

---

## [04] EngineRouter Compatibility (Hard)
Because PyOS facade uses EngineRouter, generated engines MUST follow:
- File: `PyOS.{EngineShortName}.py`
- Class: `PyOS_{EngineShortName}`
- Public entry:
  - `public static function Main(array $Payload, array $Context)`

Action methods (optional) MUST:
- be `public static`
- accept `(array $Payload, array $Context)`

Return value MUST be an array (recommended) and SHOULD contain:
- `success` (bool)
- `engine` (string, e.g., `PyOS.Cache`)
- `error` (string, when failed)

Hard rule:
- Array envelope is recommended, but backward compatibility may require bool/string returns for specific actions.
- Planning must define the return-type matrix; code must follow it.

---

## [05] AliensStyle (HARD RULE — Must)
This workflow MUST apply **every mandatory rule** from:
- `/Aliens/Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Skills/AliensStyle.md`

If any mandatory AliensStyle element is missing, the run MUST be marked failed (style-gate failure).

### [05.1] Minimum AliensStyle checklist for PyOS Engines
The generated/updated engine file MUST include:
1) First line:
  - `# Copyright (c) A|iens. All rights reserved.`
2) Enterprise CodeHeader block (ASCII bars) including:
  - `Version`, `Author`, `Location`
  - `AliensStyle : <doc-version>`
  - `LastModified: <UTC ISO8601>`
  - `PyOS : Py.26.00.00` (or resolved version)
  - `Dependencies` (explicit list of real PyOS engines used)

Hard gate (must):
- `Author` must be exactly `AlienCyborg`
3) PascalCase compliance (AliensStyle core rule):
  - class, method names, helper names, and variables in PascalCase (except payload keys / DB keys).
4) Logger guard + fallback (PyOS engines):
  - never assume `d()` / `x()` exist; guard with `function_exists()` and fallback to `error_log()`.
5) Section divider standard:
  - every public method must have a SECTION divider above it.
6) Function internal code-header:
  - every public method body must start with a code-header describing Purpose/Input/Output.
7) CodeFooter block:
  - usage notes + changelog (when update) and compatibility notes.

### [05.2] Engine Header/Footer Template (Use This — Hard)
Use a clear, explicit header/footer so tools + reviewers can validate it deterministically.

Header (near top, after `# Python:` and copyright):
```
/* ============================================================================
 [ENGINE] {EngineBaseName}
 ------------------------------------------------------------------------------
 Version     : {ResolvedVersion}
 Author      : AlienCyborg
 Location    : /Aliens/PyOS/{ResolvedVersion}/Engine/{EngineBaseName}.py
 AliensStyle : v6.0
 LastModified: {UTC_ISO8601}
 Purpose     :
   - (2–6 lines)
 Dependencies:
   - PyOS::Log()
   - PyOS::Security() (if used)
   - (real deps only)
 ============================================================================ */
```

Footer (must exist at end of file):
```
// ============================================================================
// [FOOTER] HOW TO USE
// ---------------------------------------------------------------------------
// Usage:
//   PyOS::{EngineShortName}(...)
//   PyOS::{EngineShortName}{Action}(...)
//
// Changelog:
//   - {UTC_DATE} : {note}
// ---------------------------------------------------------------------------
// END OF FILE
// ============================================================================
```

---

## [06] Logging Contract (Default)
Follow PyOS engine convention (examples: Account/Agent):

Recommended guarded pattern (do NOT assume `d()` / `x()` exist):
```
$Echo = 'PyOS.{EngineShortName}: {Action}()';
if(function_exists('d')){ d($Echo); }
if(function_exists('x')){ x($Echo, 'progress', '{EngineShortName}'); }
```

Rules:
- `$Echo` / `$echo` allowed, but must be used consistently.
- `d()` / `x()` calls must be guarded with `function_exists()`.
- Log types: `progress`, `success`, `failed`, `error`.

---

## [07] Security Baseline (Deny-by-default)
If engine processes external input, it SHOULD call:
- `PyOS::SecurityValidate($Payload)`

If auth is required, engine SHOULD call:
- `PyOS::AuthValidate($Context)` (only if this exists in your ecosystem)

If permissions are required:
- Use existing PyOS permission engine calls (do not invent new auth systems).

---

## [08] Create vs Update Rules
### Create
If file missing:
- Create a full enterprise engine implementation as per Planning (not a minimal skeleton).

Forbidden (hard fail):
- Main-only engine
- Placeholder-only file that does not implement the planned topic feature-set

Minimum baseline for new engines (must exist in the same file):
- CodeHeader + CodeFooter
- Guarded logger helper
- Payload arg extraction helper (numeric payload)
- Ok/Fail/error model helpers
- Topic-based core actions (from planning + discovery)

### Update
If file exists:
- Minimal diffs only.
- **No Feature Loss**: do not remove existing public behavior unless explicitly requested.

Special case:
- If existing engine is non-router compliant (instance-only / missing static Main):
  - Do not attempt automatic conversion.
  - Require manual approval and a clear plan first.

---

## [09] Quality Gates (Must pass)
- ClassName matches file naming rule.
- `Main()` exists and is `public static`.
- No forbidden includes / direct requires of other engines.
- Only one production file is modified/created (the target engine file).

---

## [09.1] Enterprise / Ultra Output Rules (User Expectation)
User expectation: output **enterprise grade + ultra level features** ho, and engine file **large** ho sakti hai.

### (A) No Missing Coverage (Critical)
- Code workflow MUST do best-effort discovery (read-only) before writing:
  - Search in `/Aliens/PyOS/{Version}/Engine/` for calls like `PyOS::{EngineShortName}{Action}(`.
- For every discovered `{Action}`:
  - ensure `public static function {Action}(array $Payload, array $Context)` exists.
  - If exact behavior unknown:
    - do NOT ship a "jugaad" stub.
    - implement the best-effort enterprise behavior derived from Planning + existing PyOS conventions.
    - only if still ambiguous: implement a safe, well-documented fallback with:
      - strict validation
      - deterministic error model
      - explicit TODO + documented contract in Docs
      - and it must NOT be the only content of the engine.

### (B) Enterprise Baseline Inside Single File
Within the same engine file (no extra files):
- Add internal helpers:
  - payload arg getter (EngineRouter numeric payload)
  - type validators (string/int/array/bool) as needed
  - sanitizers/redactors for logging
- Add structured error model:
  - consistent error codes (e.g., `SEC_INVALID_PAYLOAD`, `CACHE_KEY_REQUIRED`)
  - consistent response envelope for Main + major actions

### (H) AliensGrade Audit Gate (10/10 Target)
Before marking the run successful, code output MUST satisfy:
- AliensStyle checklist (including Dependencies + Footer)
- CleanCode: clear sectioning, no dead code, no magic behavior, deterministic outputs
- Integration: uses existing PyOS engines where appropriate (Security/Policy/Log/Cache)
- Stability: no duplicate methods, no recursion traps, no fatal assumptions
- Observability: consistent logging with redaction

### (C) Defensive Coding + Stability
- Never remove existing public method signatures.
- Never break existing return types if engine already used.
- Handle edge cases:
  - missing args
  - wrong types
  - empty strings
  - nulls
- Avoid side effects in validators by default.

### (D) Logging Standard
- Use existing PyOS engine logging pattern (`$echo` + `x(...)`) for major steps.
- Logs MUST not include secrets; use redaction helper.

### (E) Verification
- After writing, run `python -m py_compile` on the generated engine file.
- If lint fails: fix immediately; do not ship broken Python.

### (F) Stability Guards (No Duplicate + No Accidental Recursion)
- Before final write, ensure **no duplicate method names** exist in the class.
  - Duplicate method => Python fatal `Cannot redeclare ...` (ship-stopper).
- Safe stubs MUST NOT call themselves (directly/indirectly).
  - Stub implementation should return deterministic safe failure, not recursion.

### (G) Return-Type Compatibility Gate
- If discovery shows mixed usage patterns for an action (e.g., used as `bool` in one place and `array` in another):
  - implement a backward-compatible behavior (best-effort),
  - document it in docs workflow,
  - add a validation checklist item in planning.

---

## [10] Minimal Success Criteria
- Engine file exists at:
  - `/Aliens/PyOS/{Version}/Engine/{EngineBaseName}.py`
- Engine is callable via facade:
  - `PyOS::{EngineShortName}()` routes to `Main()`
  - `PyOS::{EngineShortName}{Action}()` routes to `{Action}()` (if action exists)

- Enterprise criteria:
  - Discovered actions implemented or safe-stubbed (No Missing Coverage)
  - Lint passes (`python -m py_compile`)
