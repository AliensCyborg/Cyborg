# ACC Memory Workflows (v26)
# Folder: Workflows/ACC/Memory/
# Status: Draft
# LastUpdated: 2026-01-27

This folder contains the **enterprise-grade Memory system** workflows.

SSOT reference:
- `Workflows/ACC/_Refs/ACC.Memory.md`

Workflows:
- `Memory-Write` — write a scoped memory entry (short + detail + meta)
- `Memory-Read` — read scoped memory safely (no cross-app mixing)
- `Memory-Index` — build/update a scope index for fast retrieval
- `Memory-Prune` — safe archival guidance (NoDelete compliant)

Hard rule:
- Memory is supporting context only; current task `Description` + SSOT docs win.
