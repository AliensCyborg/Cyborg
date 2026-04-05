---
WorkflowId: Role_ACLE
Type: Workflow_Plural
Domain: Role
Category: Role SOP Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: given one-or-more Role SOP document paths (ya CSV file ya source directory path),
  ACLE (Aliens Cinematic Learning Experience) cinematic video lessons generate karo by delegating
  to child workflows. Yeh workflow khud code generate nahi karta — sirf coordination +
  target resolution + safety/approval enforce karta hai, aur child workflows ko
  deterministic order me call karta hai.
  Source: /Aliens/Role/*.md (ultra detailed Role SOP documents, flat structure — no language subdirs).
  Output: /Aliens/Role_ACLE/ (cinematic HTML + narrations + audio).
Invocation:
  Signature:
    - Workflow("Role_ACLE", "Roles.csv", "Description : 1 to 10")
    - Workflow("Role_ACLE", "Software_Engineer, CEO, Product_Manager", "Convert role SOPs")
    - Workflow("Role_ACLE", "/Aliens/Role", "Description : 1 to 25")
    - Workflow("Role_ACLE", "Software_Engineer", "Single role SOP convert karo")
  TargetsMeaning: >
    3 input formats supported:
    (1) CSV filename — batch mode with per-row tracking.
    (2) Comma-separated role names — each = filename stem from /Aliens/Role/ without .md extension.
    (3) Directory path — source Role directory path; .md files scan honge.
    (4) Single role name — no comma, no .csv = single Role SOP file.
    Range filtering Description me aata hai, Targets me nahi. Two range types supported:
    - Numeric: "1 to 10" — roles alphabetically sort karke #1 se #10 pick.
    - Alphabetical: "A to D" — role files filter by first-letter range A-D.
  DescriptionMeaning: >
    Human language constraints/requirements. Must NOT expand scope beyond Role ACLE conversion.
    Optional range suffix supported (two types):
    (a) Numeric range: "Description : N to M" — roles alphabetically sort karke #N se #M filter.
    (b) Alphabetical range: "Description : A to D" — role files jinke naam A-D se start
        hote hain, sirf wohi include.
    Range sirf Description me valid hai, Targets me nahi.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Role_ACLE/"
    Pattern: "{RoleSlug}.plan.md"
  Code:
    Path: "/Aliens/Role_ACLE/{RoleSlug}.html"
    Pattern: "*.html + *_narrations.json + audio_*/ (per role)"
  Documentation:
    Path: "/Aliens/Docs/Role_ACLE/"
    Pattern: "{RoleSlug}.md"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Role_ACLE.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Role_ACLE-Planning
  - Role_ACLE-Code
  - Role_ACLE-Documentation
Safety:
  NoDelete: true
  SourceReadOnly: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Source Role repo (`/Aliens/Role/`) is strictly READ-ONLY. Koi file modify/delete nahi.
  - Output Role_ACLE repo (`/Aliens/Role_ACLE/`) me write hota hai.
  - edge-tts (Microsoft Azure TTS) ke liye internet connection mandatory hai.
  - Source articles .md format me hain (ultra detailed Role SOP documents, Hinglish).
  - Source structure: /Aliens/Role/{RoleSlug}.md (FLAT — no language subdirs).
  - Output structure: /Aliens/Role_ACLE/{RoleSlug}.html + narrations + audio
  - Converter scripts: `/Aliens/Cyborg/AlienCyborg/Code/convert_role_acle.py` (single role) +
    `batch_role_acle.py` (batch orchestrator).
  - Batch progress tracking: `/Aliens/.Alien/{Developer_Username}/State/Role_ACLE/batch_progress.json`
  - Branding: "Aliens Role" (not "Aliens Wiki" or "Aliens Blog").
  - RoleSlug = filename stem (e.g., Software_Engineer, CEO, Product_Manager).
  - Role SOP files are large: 300-1800+ lines, ultra detailed, Hinglish.
SpeedMode:
  Enabled: true
  Description: >
    Planning aur Documentation child workflows bypass hote hain for speed.
    Sirf Code (conversion to cinematic HTML + audio) execute hota hai.
---

# Role_ACLE

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope sirf orchestration hai:
- Manifest + Targets validation enforce karna
- Target resolution (CSV / comma-list / directory-path / single-role → role paths list)
- Description parsing for range filter ("Description : N to M")
- Child workflows ko deterministic order me call karna
- Output paths sirf YAML header ke hisaab se resolve karna

Forbidden:
- Direct code generate/modify is file me (child workflow karega)
- Extra roles invent karna / unrelated scope add karna
- Description me jo nahi bola, woh add karna
- Source Role repo (`/Aliens/Role/`) me koi bhi modify/delete

# [03] Manifest Gate (Mandatory)
1) Read manifest: "/Aliens/manifest.json"
2) Approval policy:
   - Approval=manual => approval-gated steps par proceed se pehle approval required
   - Approval=auto   => proceed without approval
3) Permissions (minimum):
   - Planning: allowed (default)
   - Code generation (role conversion): allowed (writes to Role_ACLE, not prod code)

# [04] Target Resolution (Deterministic)
## 4.1 — CSV Mode
If Targets ends with `.csv`:
- Resolve: `/Aliens/Project/{Developer_Username}/{Targets}`
- CSV schema: same as Wiki_ACLE with Role adaptations.
- Per-run one row (CSV single-row rule).

## 4.2 — Comma-Separated Mode
If Targets contains `,`:
- Split by comma, trim each.
- Each = role filename stem from `/Aliens/Role/` without `.md`
- Resolve: `/Aliens/Role/{stem}.md` per item.

## 4.3 — Directory Path Mode
If Targets starts with `/` or `\` or contains `:\`:
- Interpret as absolute directory path.
- Scan for `.md` files (skip LICENSE/README).
- Apply range/letter-range from Description if given.

## 4.4 — Single Role Mode
If Targets is single word/name, no comma, no `.csv`:
- Resolve as single role path: `/Aliens/Role/{Targets}.md`
- If file doesn't exist, fail with `WF_TARGET_MISSING`.

# [05] For Each Role:
Call child workflows in deterministic order:
1. `Role_ACLE-Planning(RolePath, Description)` — [BYPASSED in SpeedMode]
2. `Role_ACLE-Code(RolePath, Description)` — execute conversion
3. `Role_ACLE-Documentation(RolePath, Description)` — [BYPASSED in SpeedMode]
