---
WorkflowId: Memory-Read
Title: Memory Read (Scoped)
Category: System
Type: Workflow_Singular

Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-27

Intent:
  Summary: >
    Retrieve relevant memory entries for a given ScopeKey safely. Enforces strict scope isolation
    to prevent cross-app feature/rule mixing.
  WhenToUse:
    - "Before planning/coding for an App/Module/Engine to recall prior decisions and constraints."
    - "When you want to check previously stored governance rules or known pitfalls."

Inputs:
  Targets:
    Format: "ScopeKey"
    Examples:
      - "Global.Governance"
      - "WebApp.Exchange"
  Description:
    Format: "OPTIONAL query. If empty, show latest N entries (default 5)."
    Examples:
      - "Find decisions about planner CSV schema and path rules"

Defaults:
  MaxEntries: 5

Permissions:
  NoDelete: true
  CanCreateFiles: false
  CanEditFiles: false
  CanModifyProd: false
  CanTouchDB: false

Approval:
  Mode: "manifest"
  RequiredActions: []

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Singular.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Memory.md"

---

# [0] Purpose
`Workflow("Memory-Read", "{ScopeKey}", "{Description?}")`

Read memory for the given scope.

---

# [1] Guardrails (Non-Negotiable)
- Only read from: `/Aliens/.Alien/{Developer_Username}/Memory/{ScopeKey}/...`
- Do NOT read other scopes.
- If query implies another scope, STOP and ask for correct ScopeKey.

---

# [2] Output Contract
Return in response:
- Latest N entries (or query-matched subset)
- For each entry: timestamp + title + short summary + paths to detail/meta

---

# [3] Error Codes
- `ACC_ERR_TARGET_INVALID`
- `ACC_ERR_PERMISSION_DENIED`

---

# END
