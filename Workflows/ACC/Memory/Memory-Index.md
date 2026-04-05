---
WorkflowId: Memory-Index
Title: Memory Index (Scoped)
Category: System
Type: Workflow_Singular-Documentation

Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-27

Intent:
  Summary: >
    Build/update a scoped memory index for fast retrieval without reading large history files.
  WhenToUse:
    - "After many Memory-Write entries in a scope"
    - "When Memory-Read becomes slow"

Inputs:
  Targets:
    Format: "ScopeKey"
    Examples:
      - "Global.Governance"
      - "WebApp.Exchange"
  Description:
    Format: "OPTIONAL — indexing mode or notes"

Permissions:
  NoDelete: true
  CanCreateFiles: true
  CanEditFiles: true
  CanModifyProd: false
  CanTouchDB: false

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Memory.md"

Outputs:
  Index:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Memory/{ScopeKey}/Index/"
    FileName: "Memory.Index.jsonl"

---

# [0] Purpose
`Workflow("Memory-Index", "{ScopeKey}", "{Description?}")`

Create/update a JSONL index file.

---

# [1] Index Contract
Each JSONL line represents one entry:
- timestamp_utc
- title
- entry_type
- tags
- entry_path

---

# [2] NoDelete Safe Mode
Do not delete or overwrite historical entries.
Index rebuild is allowed by rewriting `Memory.Index.jsonl` only.

---

# END
