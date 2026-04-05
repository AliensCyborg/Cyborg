---
WorkflowId: Workflow_Plural-Update
Type: CommonRuleFile
Scope: ACC.Workflow.Plural.Update
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-05
Purpose: >
  Common rules for every Plural-Update workflow. This file is NOT an executable workflow by itself.
  It standardizes update-only orchestration (Audit -> Planning -> Code -> Documentation), missing-target
  policy, approvals, run-records, and failure semantics.
Compatibility:
  ACC: ">=26.00.00"
Requires:
  - "Workflows/ACC/_Common/Workflow_Plural.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "/Aliens/manifest.json"
NonNegotiables:
  - NoDelete: true
  - UpdateOnly: true
  - MissingTargetPolicy: "error"
  - DefaultVersionFallback: "PHP.26.00.00"
...

# Workflow_Plural-Update.md

Yeh file *workflow nahi hai*. Yeh Plural Update workflows ke liye common rule-engine hai.

## [01] Update-Only Contract (Hard)
- Target MUST already exist (missing target => fail with `WF_TARGET_MISSING`).
- No create behavior allowed in update workflows.

## [02] Mandatory Pipeline (Hard Order)
Per target, the canonical update pipeline is:
1) Audit
2) Planning
3) Code
4) Documentation

## [03] Approvals + Safety
- ProdWrite updates follow manifest approvals.
- NoDelete is always enforced.

## [04] Run Records (Audit-grade)
- Each target MUST produce RunItem evidence for audit/planning/code/docs.

## [05] Failure Semantics
- If Audit fails: skip remaining steps for that target.
- If Planning fails: skip Code+Docs.
- If Code fails: Docs may still run only if the workflow explicitly allows “doc drift report”; default is skip.
