---
WorkflowId: BugFix_FromLogs-Audit
WorkflowName: BugFix Audit (From Logs)
Type: Workflow_Singular-Audit
Domain: Common
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Audit.md"
---

# BugFix_FromLogs-Audit.md

## Goal
Logs se:
- Error signature extract
- File paths resolve (workspace ke andar)
- Per file: existence + size + quick syntax/lint status

## Output
Must write (minimum):
- RunItem `Artifacts/BugFix.Audit.md`
- RunItem `Artifacts/BugFix.Audit.json`

No prod code change in Audit.
