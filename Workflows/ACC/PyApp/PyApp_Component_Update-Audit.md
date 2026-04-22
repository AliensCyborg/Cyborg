---
WorkflowId: PyApp_Component_Update-Audit
WorkflowName: PyApp Component Update (Audit)
Type: Workflow_Singular-Audit
Domain: PyApp
Scope: Audit existing PyApp Component + CSS before update (read-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Component_Update-Audit"
    - Targets: "App/Component"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/"
    FileName: "{ComponentNameNormalized}.Update.audit.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "Update-only: Component + CSS must already exist"
  - "Read-only audit"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Audit.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Products.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Products.css"
...

# PyApp_Component_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: update se pehle existing component + CSS ko **audit** karna.

Outputs:
- Run-scoped: `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`
- Deterministic artifact (hard):
  - `/Aliens/.Alien/{Developer}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code/Docs must not rely on run-scoped audit output.

---

## [01] Resolve paths (deterministic)
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ComponentNameNormalized: App prefix rule (SSOT)

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{ComponentNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{ComponentName}`

- ComponentFileCandidates (pick first that exists):
  - `{AppRoot}/User/component/{ComponentNameNormalized}.py`
  - `{AppRoot}/User/component/{ComponentNameLegacyUnderscore}.py`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{ComponentNameNormalized}.css`
  - `{AppRoot}/User/css/{ComponentNameLegacyUnderscore}.css`

- ComponentFile: first existing from ComponentFileCandidates
- CssFile: first existing from CssFileCandidates

Update-only hard rules:
- If ComponentFile missing => fail `WF_COMPONENT_NOT_FOUND`
- If CssFile missing => fail `WF_COMPONENT_CSS_NOT_FOUND`

---

## [02] Audit scope (Hard)
Audit MUST cover (minimum):

### A) Safety + scope
- Update-only boundaries respected (no create/delete intent)
- StrictScopeOnly enforced (no unrelated work)

### B) AliensStyle + enterprise quality (User Reported Issues - 8)
Audit MUST explicitly report Pass/Fail + fix for:
1) File Code-Header Author must be exactly: `AlienCyborg`
2) File Code-Footer exists
3) Internal code-header exists for non-trivial Python logic blocks (BackendGuard region) (Purpose/Input/Output)
4) Sections are proper and readable (`[SECTION]` blocks around major regions)
5) `$echo` and `d()` usage is correct when logging exists (do not use `x()`; do not leak `$echo` into UI output)
6) Comments are meaningful + structured (no noise)
7) AliensStyle applied correctly (header/footer/sections)
8) Output is enterprise-grade (not a tiny skeleton; contains required UI skeleton + guard discipline + premium checklist)

### C) PyApp UI contracts
- BackendGuard rule present when heavy work exists
- CSS scoped to UTag selectors; selector depth rule not violated
- ACC.UI.Premium baseline met (as applicable)

### D) Component hygiene hard rules (must be Pass/Fail)
- No Python `function` declarations / closures / classes inside component files
- No processing (conditions/loops/merges/escaping/Component() calls) outside BackendGuard
- Markup/template uses ONLY `$Data[...]` (no `$meta/$state/$config` etc.)
- If plural: per-item markup is delegated to singular child; if `$Data`-only template is used, confirm buffered child HTML into `$Data['ItemsHtml']`

---

## [03] Output template (Mandatory)
Audit.md MUST include:
1) Header (Target, RunId/RunItemId)
2) Resolved paths
3) Summary score (Pass/Fail + risk)
4) Findings grouped by severity
5) Required fix checklist (include `User Reported Issues (8)` section)
6) Non-destructive notes
7) Approval note (if ApprovalMode=manual)

Hard rule:
- Also write the same content to deterministic artifact under Planning path.
