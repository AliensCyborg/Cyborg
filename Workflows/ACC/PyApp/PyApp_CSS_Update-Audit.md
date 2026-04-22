---
WorkflowId: PyApp_CSS_Update-Audit
WorkflowName: PyApp CSS Update (Audit)
Type: Workflow_Singular-Audit
Domain: PyApp
Scope: Audit existing app-level CSS before update (read-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Inputs:
  Workflow():
    - WorkflowId: "PyApp_CSS_Update-Audit"
    - Targets: "AppName"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/CSS/"
    FileName: "App.Update.audit.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "Update-only: CSS must already exist"
  - "Read-only audit"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Audit.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/PyApp/Trip/25.00.00/theme/Trip_Basic/User/css/0.css"
...

# PyApp_CSS_Update-Audit

## [00] Purpose
Update se pehle `User/css/0.css` ko audit karna (read-only):
- token overrides are sane
- selectors are generic
- no destructive resets

## [01] Resolve paths (deterministic)
- AppRoot: `/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
- CssFile: `{AppRoot}/User/css/0.css`

Hard rules:
- If CssFile missing => fail `WF_APP_CSS_NOT_FOUND`

## [02] Audit checks (Minimum)
- `:root` overrides do not delete base keys
- overlay patterns (`popups`, `Flyouts`) remain safe
- utility classes are generic (no component coupling)
- no `@import` of remote assets by default
