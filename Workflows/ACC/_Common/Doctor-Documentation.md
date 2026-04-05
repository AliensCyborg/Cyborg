---
WorkflowId: Doctor-Documentation
WorkflowName: Doctor Documentation (From Logs)
Type: Workflow_Singular-Documentation
Domain: Common
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
---

# Doctor-Documentation.md

## Goal
Docs me yeh cover ho:
- Issue summary (error signature)
- Affected file(s)
- What was changed (if any) + why it's safe
- Verification (lint/test) results
- Rollback note (if changes reverted)

## Output
- `/Aliens/Docs/Doctor/{RunId}.md`
