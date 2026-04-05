---
WorkflowId: Role_ACLE-Documentation
Type: Workflow_Singular-Documentation
Domain: Role
Category: Role SOP Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Role_ACLE
Purpose: >
  Singular Documentation workflow: given a single Role SOP document path, uski ACLE conversion ka
  documentation generate karo — covering document overview, conversion results,
  quality metrics, aur usage guide. Documentation file Docs/Role_ACLE/ me save hoti hai.
Invocation:
  Signature: Workflow("Role_ACLE-Documentation", "Software_Engineer", "Document the ACLE Role conversion results")
  TargetsMeaning: >
    Single role filename stem from /Aliens/Role/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "Software_Engineer" implies conversion results at
    "/Aliens/Role_ACLE/Software_Engineer.html".
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Role_ACLE/"
    Pattern: "{RoleSlug}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Notes:
  - Documentation is generated AFTER conversion (Code workflow) completes.
  - Documentation reflects actual conversion results from disk (not guessed).
  - Source Role repo is READ-ONLY.
  - This workflow does NOT run any conversion. It only documents what happened.
  - RoleSlug = filename stem (e.g., Software_Engineer, CEO, Product_Manager).
---

# Role_ACLE-Documentation

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/ACC.Paths.md"
require_once "Workflows/ACC/_Refs/ACC.Output.md"

# [03] Strict Scope Lock
- Sirf specified role ki documentation banao
- Extra roles add karna forbidden
- Source Role repo modify karna forbidden
- Conversion output files modify karna forbidden
- Converter scripts modify karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE role filename stem
- Filename stem from `/Aliens/Role/` without `.md` extension
- Comma-separated list => error (`WF_TARGETS_INVALID`)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"
- Documentation write permission (allowed by default; writes to `/Aliens/Docs/`).

# [06] Discovery Phase (Mandatory — Data from Disk)

## [06.1] Source Document Discovery
Read: `/Aliens/Role/{RoleSlug}.md`
- Document title, role name, version, department
- Word count, line count, section count
- Tables count, Mermaid diagrams count, code blocks count

## [06.2] Conversion Results Discovery
### HTML Check:
- Path: `/Aliens/Role_ACLE/{RoleSlug}.html`
- Exists? Size? (must be > 5KB for success)
- Branding check: "Aliens Role" present?

### Narrations JSON Check:
- Path: `/Aliens/Role_ACLE/{RoleSlug}_narrations.json`
  OR: `/Aliens/Role_ACLE/audio_{RoleSlug}/{RoleSlug}_narrations.json`
- Exists? Valid JSON? Narration count?

### Audio Check:
- Path: `/Aliens/Role_ACLE/audio_{RoleSlug}/`
- Directory exists? MP3 count? Total size?

# [07] Documentation Output Path
- `/Aliens/Docs/Role_ACLE/{RoleSlug}.md`

# [08] Documentation Format
Documentation MUST include:
1) Document Overview (role title, department, source path, version, line count)
2) Conversion Results (HTML size, slide count, audio count, total duration estimate)
3) Quality Check (branding, structure, completeness)
4) File Inventory (all generated files with paths + sizes)
5) Notes (any issues, suggestions, special considerations for large documents)

# [09] Acceptance Criteria
Documentation success tabhi:
- Output file exists and non-empty
- All discovery sections present
- Conversion results accurately reflect disk state
