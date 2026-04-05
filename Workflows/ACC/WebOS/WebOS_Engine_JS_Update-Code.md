---
WorkflowId: WebOS_Engine_JS_Update-Code
WorkflowName: WebOS Engine JS Update (Code)
Type: Workflow_Singular-Code
ParentWorkflowId: WebOS_Engine_JS_Update
Domain: WebOS
Scope: "Engine -> JS (Update)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Targets: "Single Engine (update). Example: WebOS.Cache"
  - Description: "Human update requirement"
Outputs:
  - Code: "/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js"
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
  DbTouch: "not-required"
Approvals:
  Mode: "From /Aliens/manifest.json"
  ManualIf:
    - "ProdWrite && manifest.ApprovalMode == manual"
    - "Audit reported Critical gaps"
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Loader.js"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Cache.js"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Table.js"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Auth.js"
...

# WebOS_Engine_JS_Update-Code

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Purpose
Is workflow ka kaam: existing WebOS JS engine file ko **enterprise-grade update** karna.

Hard constraints:
- Target engine file MUST already exist.
- Only the single target engine file may be modified.
- No feature loss; backward compatibility preserved.

---

## [01] Inputs & Evidence
Before writing, workflow MUST read (best-effort):
- Existing engine file
- Update plan: `{EngineName}.update.plan.md`
- Deterministic Audit Artifact: `{EngineName}.update.audit.md`

If plan/audit missing:
- Fail fast (update process integrity).

---

## [02] Update Strategy (Hard)
The update MUST:
1) Fix audit-detected AliensStyle/AliensGrade gaps
2) Close security gaps (prototype pollution, unsafe merges, validation)
3) Strengthen multi-tenant isolation (context keying)
4) Preserve existing public API (method names/signatures/return shapes)
5) Implement requested changes (bug/perf/security)

Allowed:
- Large diffs inside the single file, if needed for enterprise completeness.

Forbidden:
- Removing existing public methods/options (unless explicitly requested)
- Editing other engines/files
- Introducing new global behaviors outside this engine

---

## [03] Verification Gates (Must)
- Engine file exists at `/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js`
- Header/Footer present
- No secrets in logs/examples
- Explicit error model present and stable
- Deterministic outputs (no random/time-based changes unless documented)

Additional hard gates (must):
- Header `Author` must be exactly `AlienCyborg`
- Do NOT preserve/copy legacy author from existing file; UPDATE must normalize author to `AlienCyborg`.
- Footer must include: `Usage Guide`, `Troubleshooting`, `Update Notes`, `Change Log`.
- Every exported/public method must start with JSDoc code-header (Purpose/Input/Output)
- Sections must be consistent and readable (rulers + grouped responsibilities; no blob file)
- Logging must be guarded and debug-flag driven (no unsafe console spam)
- Comments must be meaningful (why/contract) and AliensStyle-compliant
- Engine must be enterprise-grade when plan/description requires it (helpers + stable error model + no missing planned features)

JS-specific logging rule (hard):
- `$echo` is PHP-only. Do NOT use `$echo` in JS engines.
- `d()` is PHP/SDK helper. Do NOT call `d()` in JS engines.
- Prefer an internal `Debug()` helper with a deterministic flag, OFF by default.

Required templates (hard):
1) Function JSDoc header:
```js
/**
 * Purpose : <one line>
 * Input   : <Payload/Context shape>
 * Output  : <return shape + error envelope>
 */
```

2) Footer minimum headings (inside a footer block):
- Usage Guide
- Troubleshooting
- Update Notes
- Change Log

---

## [04] Minimal Success Criteria
- Target engine file updated.
- Audit items addressed (or explicitly deferred with justification in docs/plan).
- No unrelated files modified.
