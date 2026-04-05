---
WorkflowId: Memory-Prune
Title: Memory Prune (Archive-Only; NoDelete)
Category: System
Type: Workflow_Singular

Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-27

Intent:
  Summary: >
    Reduce active memory clutter by archiving old entries WITHOUT deleting them.
    This workflow is NoDelete-compliant: it may copy entries to an Archive folder but must not remove originals.
  WhenToUse:
    - "Memory scope has too many entries and you want an archive split"

Inputs:
  Targets:
    Format: "ScopeKey"
    Examples:
      - "WebApp.Exchange"
  Description:
    Format: "Retention/archival rule in plain language (example: archive older than 90 days)"

Permissions:
  NoDelete: true
  CanCreateFiles: true
  CanEditFiles: true
  CanModifyProd: false
  CanTouchDB: false

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Singular.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Memory.md"

---

# [0] Purpose
`Workflow("Memory-Prune", "{ScopeKey}", "{Description}")`

Archive old entries safely.

---

# [1] NoDelete Compliance
- Allowed: copy older entries under:
  - `/Aliens/.Alien/{Developer_Username}/Memory/{ScopeKey}/Archive/...`
- Forbidden: deleting or moving originals.

---

# [2] Output Contract
Return:
- How many entries considered
- How many archived
- Archive paths

---

# END
