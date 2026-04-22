---
WorkflowId: PyApp_Section_Update-Audit
WorkflowName: PyApp Section Update (Audit)
Type: Workflow_Singular-Audit
Domain: PyApp
Scope: Audit existing PyApp Section + CSS before update (read-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Section_Update-Audit"
    - Targets: "App/Screen_Section"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Section/"
    FileName: "{SectionNameNormalized}.Update.audit.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "Update-only: Section + CSS must already exist"
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
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Breadcrumbs.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Breadcrumbs-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Hero-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_About.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_About-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Specs.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Specs-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Offers.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Offers-Section.css"
   
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_QA.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_QA-Section.css"
   
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Related.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Related-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Reviews.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Reviews-Section.css"

  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Footer.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Footer-Section.css"
...

# PyApp_Section_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: update se pehle existing section + CSS ko **audit** karna.

Outputs:
- Run-scoped: `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`
- Deterministic artifact (hard):
  - `/Aliens/.Alien/{Developer}/Planning/PyApp/{AppName}/Section/{SectionNameNormalized}.Update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code/Docs must not rely on run-scoped audit output.

---

## [01] Resolve paths (deterministic)
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- SectionNameNormalized: App prefix rule
- SectionNameLegacyUnderscore: legacy basename variant (dot/underscore compatibility)

SectionFileCandidates (pick first that exists):
- `{AppRoot}/User/section/{SectionNameNormalized}.py`
- `{AppRoot}/User/section/{SectionNameLegacyUnderscore}.py`

CssFileCandidates (pick first that exists; update-only compatibility; do not rename/move):
- `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`         (SSOT / canonical)
- `{AppRoot}/User/css/{SectionNameNormalized}.css`                (legacy transitional)
- `{AppRoot}/User/css/{SectionNameLegacyUnderscore}-Section.css`  (legacy)
- `{AppRoot}/User/css/{SectionNameLegacyUnderscore}.css`          (rare transitional)

- SectionFile: first existing from SectionFileCandidates
- CssFile: first existing from CssFileCandidates

Update-only hard rules:
- If SectionFile missing => fail `WF_SECTION_NOT_FOUND`
- If CssFile missing => fail `WF_SECTION_CSS_NOT_FOUND`

---

## [02] Audit scope (Hard)
Audit MUST cover:

### A) Composition-only policy
- Section must not contain generated Component implementations
- Section must only call `Component('Name', ...)` and keep logic minimal

### B) AliensStyle + enterprise quality (User Reported Issues - 8)
Audit MUST explicitly report Pass/Fail + fix for:
1) File Code-Header Author must be exactly: `AlienCyborg`
2) File Code-Footer exists
3) Internal code-header exists for any non-trivial Python logic blocks (Purpose/Input/Output)
4) Sections are proper and readable (`[SECTION]` blocks around major regions)
5) `$echo` and `d()` usage is correct when logging exists (no `x()`; no `$echo` leakage)
6) Comments are meaningful + structured
7) AliensStyle applied correctly (header/footer/sections)
8) Output is enterprise-grade (not a tiny skeleton; preserves composition, safety, premium rules)

### C) UI + CSS
- BackendGuard discipline enforced for heavy work
- CSS scoped to UTag selectors; selector depth rule not violated

---

## [03] Output template (Mandatory)
Audit.md MUST include:
- Header, paths, summary score
- Findings + required fix checklist (include `User Reported Issues (8)`)
- Non-destructive notes + approval note

Hard rule:
- Also write the same content to deterministic artifact under Planning path.
