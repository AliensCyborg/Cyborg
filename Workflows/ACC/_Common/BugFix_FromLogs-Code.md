---
WorkflowId: BugFix_FromLogs-Code
WorkflowName: BugFix Code (Mechanical Safe Patches)
Type: Workflow_Singular-Code
Domain: Common
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
---

# BugFix_FromLogs-Code.md

## Hard Rule
Only apply mechanical safe patches.
If fix requires logic change => STOP and report plan only.

## Allowed Mechanical Fixes (Examples)
- Remove UTF-8 BOM causing syntax error
- Neutralize duplicate `<?php` tags and `?>` close tags (comment-out, no line-loss)

## Mandatory Gates
- Before/After snapshots
- Lint must pass after patch OR rollback
- No deletions
