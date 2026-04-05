---
WorkflowId: WebApp_Screen_Update-Audit
WorkflowName: WebApp Screen Update (Audit)
Type: Workflow_Singular-Audit
Domain: WebApp
Scope: Audit existing WebApp Screen + CSS before update (read-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Screen_Update-Audit"
    - Targets: "App/Screen"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Screen/"
    FileName: "{ScreenNameNormalized}.Update.audit.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "Update-only: Screen + CSS must already exist"
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
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposit.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposit-Screen.css"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposits.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposits-Screen.css"
...

# WebApp_Screen_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: update se pehle existing screen + CSS ko **audit** karna.

Outputs:
- Run-scoped: `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`
- Deterministic artifact (hard):
  - `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/Screen/{ScreenNameNormalized}.Update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code/Docs must not rely on run-scoped audit output.

---

## [01] Resolve paths (deterministic)
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ScreenNameNormalized: App prefix rule
- ScreenNameLegacyUnderscore: legacy basename variant (dot/underscore compatibility)

ScreenFileCandidates (pick first that exists):
- `{AppRoot}/User/screen/{ScreenNameNormalized}.php`
- `{AppRoot}/User/screen/{ScreenNameLegacyUnderscore}.php`

CssFileCandidates (pick first that exists; update-only compatibility; do not rename/move):
- `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`         (SSOT / canonical)
- `{AppRoot}/User/css/{ScreenNameNormalized}.css`                (legacy transitional)
- `{AppRoot}/User/css/{ScreenNameLegacyUnderscore}-Screen.css`   (legacy)
- `{AppRoot}/User/css/{ScreenNameLegacyUnderscore}.css`          (rare transitional)

- ScreenFile: first existing from ScreenFileCandidates
- CssFile: first existing from CssFileCandidates

Update-only hard rules:
- If ScreenFile missing => fail `WF_SCREEN_NOT_FOUND`
- If CssFile missing => fail `WF_SCREEN_CSS_NOT_FOUND`

---

## [02] Audit scope (Hard)
Audit MUST cover:

### A) Composition-only policy
- Screen must not contain generated Section implementations
- Screen must only call `Section('Name', ...)` and keep logic minimal

### B) AliensStyle + enterprise quality (User Reported Issues - 8)
Audit MUST explicitly report Pass/Fail + fix for:
1) File Code-Header Author must be exactly: `AlienCyborg`
2) File Code-Footer exists
3) Internal code-header exists for any non-trivial PHP logic blocks (Purpose/Input/Output)
4) Sections are proper and readable (`[SECTION]` blocks around major regions)
5) `$echo` and `d()` usage is correct when logging exists (no `x()`; no `$echo` leakage)
6) Comments are meaningful + structured
7) AliensStyle applied correctly (header/footer/sections)
8) Output is enterprise-grade (not a tiny skeleton; preserves composition, safety, and premium rules)

### C) UI + CSS
- CSS scoped safely (prefer wrapper/scope tag)
- ACC.UI.Premium baseline met (as applicable)

---

## [03] Output template (Mandatory)
Audit.md MUST include:
- Header, paths, summary score
- Findings + required fix checklist (include `User Reported Issues (8)`)
- Non-destructive notes + approval note

Hard rule:
- Also write the same content to deterministic artifact under Planning path.
