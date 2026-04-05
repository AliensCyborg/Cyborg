---
WorkflowId: Workflow_Singular-Audit
Type: CommonRuleFile
Scope: ACC.Workflow.Singular.Audit
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-05
Purpose: >
  Common rules for every Singular-Audit workflow. This file is NOT an executable workflow by itself.
  It standardizes audit artifacts, evidence recording, and safe read-only behavior.
Compatibility:
  ACC: ">=26.00.00"
Requires:
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "/Aliens/manifest.json"
NonNegotiables:
  - NoDelete: true
  - ProdWrite: false
  - DbTouch: "not-required"
  - DefaultVersionFallback: "PHP.26.00.00"
...

# Workflow_Singular-Audit.md

Yeh file *workflow nahi hai*. Yeh Singular Audit workflows ke liye common rules define karti hai.

## [01] Read-only Guarantee (Hard)
- Audit workflows MUST be read-only (no prod writes).
- Outputs are audit artifacts under RunRecord only.

## [02] Mandatory Output
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

## [03] Evidence Pack (Minimum)
Audit artifact MUST record:
- Resolved target + paths + version
- What was checked (style/contract/security/coverage/docs drift)
- Findings with severity (Critical/High/Medium/Low)
- Actionable remediation notes

## [04] No Guessing
If evidence is missing, explicitly mark "Not found".
