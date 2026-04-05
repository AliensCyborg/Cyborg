---
WorkflowId: Doctor-Audit
WorkflowName: Doctor Audit (From Logs)
Type: Workflow_Singular-Audit
Domain: Common
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Audit.md"
---

# Doctor-Audit.md

## Goal
Logs se:
- Error signature extract
- File paths resolve (workspace ke andar)
- Per file: existence + size + quick syntax/lint status

## Output
Must write (minimum):
- RunItem `Artifacts/Doctor.Audit.md`
- RunItem `Artifacts/Doctor.Audit.json`

No prod code change in Audit.
