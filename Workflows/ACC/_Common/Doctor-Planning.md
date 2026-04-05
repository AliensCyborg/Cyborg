---
WorkflowId: Doctor-Planning
WorkflowName: Doctor Planning (From Logs)
Type: Workflow_Singular-Planning
Domain: Common
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
---

# Doctor-Planning.md

## Goal
Audit output ke base par ek deterministic checklist plan.
Plan MUST be safe:
- Non-breaking
- No business logic change
- Prefer mechanical fixes

## Output
- `/Aliens/.Alien/{Developer}/Planning/Doctor/{RunId}/Doctor.Plan.md`
