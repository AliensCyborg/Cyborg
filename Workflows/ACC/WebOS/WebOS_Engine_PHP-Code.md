---
WorkflowId: WebOS_Engine_PHP-Code
WorkflowName: WebOS Engine PHP Generator (Code)
Type: Workflow_Singular-Code
ParentWorkflowId: WebOS_Engine_PHP
Domain: WebOS
Scope: "Engine -> PHP"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-27
Inputs:
  - Targets: "Single Engine (recommended). Example: WebOS.Cache"
  - Description: "Human requirement in any language"
Outputs:
  - Code: "/Aliens/WebOS/PHP.{Latest}/Engine/{EngineBaseName}.php"
  - RunRecord: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
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
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Account.php"
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Agent.php"
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.API.php"
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Cache.php"
...

# WebOS_Engine_PHP-Code

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Purpose
Is workflow ka kaam: **WebOS PHP Engine file generate/update** karna in a router-compatible way.

Target example:
- `Workflow("WebOS_Engine_PHP", "WebOS.Cache", "Add Get/Set/Delete actions..." )`
- `Workflow("WebOS_Engine_PHP", "WebOS.Troubleshoot", "Describe required behavior, logs, edge-cases (required)" )`

Output example:
- `/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Cache.php`

---

## [01] Non-Negotiables
- NoDelete: kisi bhi file ko delete nahi karna.
- Strict scope: only the single resolved engine file should be created/updated.
- Boot/WebOS.EngineRouter.php ko modify karna **forbidden** in this workflow.
- Default engine type: **EngineRouterStatic**.
- AliensStyle MUST be applied fully (see [05]).

---

## [02] Target Normalization
Accepted Targets:
- `WebOS.Cache`
- `Cache` (normalized to `WebOS.Cache`)

Validation:
- EngineShortName must match: `^[A-Za-z][A-Za-z0-9]*$`
- Target must not contain `.php`.

Resolved names:
- EngineBaseName = `WebOS.{EngineShortName}`
- FileName = `{EngineBaseName}.php` → example: `WebOS.Cache.php`
- ClassName = `WebOS_{EngineShortName}` → example: `WebOS_Cache`

---

## [03] Path Resolution (ACC.Paths compliant)
Resolve Version: latest (fallback `PHP.26.00.00`).

Write target file:
- `/Aliens/WebOS/{Version}/Engine/{EngineBaseName}.php`

---

## [04] EngineRouter Compatibility (Hard)
Because WebOS facade uses EngineRouter, generated engines MUST follow:
- File: `WebOS.{EngineShortName}.php`
- Class: `WebOS_{EngineShortName}`
- Public entry:
  - `public static function Main(array $Payload, array $Context)`

Action methods (optional) MUST:
- be `public static`
- accept `(array $Payload, array $Context)`

Return value MUST be an array (recommended) and SHOULD contain:
- `success` (bool)
- `engine` (string, e.g., `WebOS.Cache`)
- `error` (string, when failed)

Hard rule:
- Array envelope is recommended, but backward compatibility may require bool/string returns for specific actions.
- Planning must define the return-type matrix; code must follow it.

---

## [05] AliensStyle (HARD RULE — Must)
This workflow MUST apply **every mandatory rule** from:
- `/Aliens/Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Skills/AliensStyle.md`

If any mandatory AliensStyle element is missing, the run MUST be marked failed (style-gate failure).

### [05.1] Minimum AliensStyle checklist for WebOS Engines
The generated/updated engine file MUST include:
1) First line:
  - `# Copyright (c) A|iens. All rights reserved.`
2) Enterprise CodeHeader block (ASCII bars) including:
  - `Version`, `Author`, `Location`
  - `AliensStyle : <doc-version>`
  - `LastModified: <UTC ISO8601>`
  - `WebOS : PHP.26.00.00` (or resolved version)
  - `Dependencies` (explicit list of real WebOS engines used)

Hard gate (must):
- `Author` must be exactly `AlienCyborg`
3) PascalCase compliance (AliensStyle core rule):
  - class, method names, helper names, and variables in PascalCase (except payload keys / DB keys).
4) Logger guard + fallback (WebOS engines):
  - never assume `d()` / `x()` exist; guard with `function_exists()` and fallback to `error_log()`.
5) Section divider standard:
  - every public method must have a SECTION divider above it.
6) Function internal code-header:
  - every public method body must start with a code-header describing Purpose/Input/Output.
7) CodeFooter block:
  - usage notes + changelog (when update) and compatibility notes.

### [05.2] Engine Header/Footer Template (Use This — Hard)
Use a clear, explicit header/footer so tools + reviewers can validate it deterministically.

Header (near top, after `<?php` and copyright):
```
/* ============================================================================
 [ENGINE] {EngineBaseName}
 ------------------------------------------------------------------------------
 Version     : {ResolvedVersion}
 Author      : AlienCyborg
 Location    : /Aliens/WebOS/{ResolvedVersion}/Engine/{EngineBaseName}.php
 AliensStyle : v6.0
 LastModified: {UTC_ISO8601}
 Purpose     :
   - (2–6 lines)
 Dependencies:
   - WebOS::Log()
   - WebOS::Security() (if used)
   - (real deps only)
 ============================================================================ */
```

Footer (must exist at end of file):
```
// ============================================================================
// [FOOTER] HOW TO USE
// ---------------------------------------------------------------------------
// Usage:
//   WebOS::{EngineShortName}(...)
//   WebOS::{EngineShortName}{Action}(...)
//
// Changelog:
//   - {UTC_DATE} : {note}
// ---------------------------------------------------------------------------
// END OF FILE
// ============================================================================
```

---

## [06] Logging Contract (Default)
Follow WebOS engine convention (examples: Account/Agent):

Recommended guarded pattern (do NOT assume `d()` / `x()` exist):
```
$Echo = 'WebOS.{EngineShortName}: {Action}()';
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
- `WebOS::SecurityValidate($Payload)`

If auth is required, engine SHOULD call:
- `WebOS::AuthValidate($Context)` (only if this exists in your ecosystem)

If permissions are required:
- Use existing WebOS permission engine calls (do not invent new auth systems).

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
  - Search in `/Aliens/WebOS/{Version}/Engine/` for calls like `WebOS::{EngineShortName}{Action}(`.
- For every discovered `{Action}`:
  - ensure `public static function {Action}(array $Payload, array $Context)` exists.
  - If exact behavior unknown:
    - do NOT ship a "jugaad" stub.
    - implement the best-effort enterprise behavior derived from Planning + existing WebOS conventions.
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
- Integration: uses existing WebOS engines where appropriate (Security/Policy/Log/Cache)
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
- Use existing WebOS engine logging pattern (`$echo` + `x(...)`) for major steps.
- Logs MUST not include secrets; use redaction helper.

### (E) Verification
- After writing, run `php -l` on the generated engine file.
- If lint fails: fix immediately; do not ship broken PHP.

### (F) Stability Guards (No Duplicate + No Accidental Recursion)
- Before final write, ensure **no duplicate method names** exist in the class.
  - Duplicate method => PHP fatal `Cannot redeclare ...` (ship-stopper).
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
  - `/Aliens/WebOS/{Version}/Engine/{EngineBaseName}.php`
- Engine is callable via facade:
  - `WebOS::{EngineShortName}()` routes to `Main()`
  - `WebOS::{EngineShortName}{Action}()` routes to `{Action}()` (if action exists)

- Enterprise criteria:
  - Discovered actions implemented or safe-stubbed (No Missing Coverage)
  - Lint passes (`php -l`)
