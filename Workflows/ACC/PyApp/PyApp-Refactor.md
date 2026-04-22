---
WorkflowId: PyApp-Refactor
WorkflowName: PyApp Refactor
Type: Workflow_Plural
Domain: PyApp
Scope: Apply refactors based on audit report to reach enterprise-grade quality
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp-Refactor"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Refactor/PyApp/{AppName}/"
    FileName: "PyApp-Refactor.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("PyApp-Refactor", "{AppName}", "{Description}")`

Apply refactors and fixes based on the audit report to reach enterprise-grade quality.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

# [2] Dependencies (Hard Gate)
Requires audit artifact:
- `/Aliens/.Alien/{Developer}/Audit/PyApp/{AppName}/PyApp-Audit.md`

If missing:
- ErrorCode: `WF_AUDIT_REQUIRED`

---

# [3] Refactor Rules
- Refactor scope MUST be limited to audit findings.
- Do not add new features not requested by audit.
- Preserve existing behavior unless the audit explicitly flags a bug.
- Update docs if refactor changes public behavior.

---

# [4] Save Outputs
RefactorRunOut:
- `/Aliens/.Alien/{Developer}/Runs/PyApp/{AppName}/PyApp-Refactor.Run.md`

---

# [5] Errors
- `WF_AUDIT_REQUIRED`
- `WF_PERMISSION_DENIED`
