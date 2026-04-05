---
WorkflowId: Memory-Write
Title: Memory Write (Scoped)
Category: System
Type: Workflow_Singular-Documentation

Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-27

Intent:
  Summary: >
    Persist a high-signal, scoped Memory entry (short + detailed + machine-readable meta)
    under `/Aliens/.Alien/{Developer_Username}/Memory/` without ever mixing contexts across apps.
  WhenToUse:
    - "After completing a meaningful chunk of work (decision/constraint/root-cause)."
    - "After finishing a workflow run that created/updated artifacts and you want durable recall."

Inputs:
  Targets:
    Format: "ScopeKey"
    Examples:
      - "Global.Governance"
      - "WebApp.Exchange"
      - "WebSDK.Module.User"
      - "WebOS.Engine.WebOS.Cache"
  Description:
    Format: "REQUIRED — this is the source prompt payload for what to remember"
    Examples:
      - "Summarize today’s changes: dev-scoped planner CSV SSOT and schema compatibility rules."

Defaults:
  Version: "latest"
  VersionFallback: "PHP.26.00.00"

Permissions:
  NoDelete: true
  CanCreateFiles: true
  CanEditFiles: true
  CanModifyProd: false
  CanTouchDB: false

Approval:
  Mode: "manifest"
  RequiredActions: []

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Memory.md"

Outputs:
  MemoryEntry:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Memory/{ScopeKey}/Entries/{YYYY}/{YYYY-MM}/{RunStamp}/"
    Files:
      - "Summary.short.md"
      - "Summary.detail.md"
      - "Meta.json"

---

# [0] Purpose
`Workflow("Memory-Write", "{ScopeKey}", "{Description}")`

Write a scoped memory entry.

---

# [1] Hard Guardrails (Non-Negotiable)
- Scope isolation: `ScopeKey` MUST be exactly one scope (ref: `ACC.Memory`).
- No cross-app mixing: never include rules/features from other apps inside this entry.
- No secrets: do not store credentials/tokens/private keys.
- Memory never overrides prompt: memory is supporting context only.

If `ScopeKey` is ambiguous or looks unsafe:
- STOP and fail with `ACC_ERR_TARGET_INVALID`.

---

# [2] What to Write (Structure)
Create these three files:

## A) Summary.short.md
- 5–12 lines
- Crisp bullets
- Only the essentials: what changed / decision / constraint / next action

## B) Summary.detail.md
Include sections (as applicable):
- Context
- Decisions (with rationale)
- Constraints / Non-negotiables
- Risks + mitigations
- Open questions
- Related artifacts/paths

## C) Meta.json
Must be valid JSON.
Minimum keys (SSOT):
- `schema` = `ACC.Memory.Entry.v1`
- `timestamp_utc`
- `scope_key`
- `entry_type` (Decision | Constraint | Pattern | Pitfall | OpenQuestion | RunSummary)
- `title`
- `tags` (array)
- `source.workflow_id` / `source.targets` (if applicable)

---

# [3] Error Codes
- `ACC_ERR_DESCRIPTION_REQUIRED`
- `ACC_ERR_TARGET_INVALID`
- `ACC_ERR_PERMISSION_DENIED`

---

# END
