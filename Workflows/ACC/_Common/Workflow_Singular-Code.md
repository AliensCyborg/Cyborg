---
WorkflowId: Workflow_Singular-Code
Type: CommonRuleFile
Scope: ACC.Workflow.Singular.Code
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-21
Purpose: >
  Common rules for every Singular workflow that GENERATES or MODIFIES code.
  This file is NOT an executable workflow by itself. It defines the universal code-generation
  contract: context loading, target resolution, safety/approvals, edit strategy, quality gates,
  outputs, and audit logs. These rules MUST remain generic so 10,000+ workflows can inherit safely.
Compatibility:
  ACC: ">=26.00.00"
  WebOS: ">=PHP.26.00.00"
  AlienCyborg: "Agent + VSCode Copilot Custom Agent + Azure AI"
Requires:
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "/Aliens/manifest.json"
AlsoMayRequire:
  - "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  - "Workflows/ACC/_Refs/ACC.Quality.md"
NonNegotiables:
  - NoDelete: true
  - UpdateWorkflows: "modify-only (missing target => error)"
  - SecretsNeverCommit: true
  - DefaultTheme: "_Basic"
  - DefaultVersionFallback: "PHP.26.00.00"
---

# Workflow_Singular-Code.md
**Description (Hinglish):**  
Yeh file *workflow nahi hai*. Yeh **Singular-Code** type ke sabhi workflows ke liye ek **common rule-engine** hai.  
Jis bhi individual workflow ka kaam code generate/modify karna ho, us workflow me is file ko `require_once` karna mandatory hai.

> **Singular-Code ka meaning:**  
> Ek aisa workflow jo ek specific target (ya ek single target item) par code generate/modify karta hai.  
> Plural orchestration iske upar loop chala sakta hai, lekin yeh file code-quality + safety ka universal contract define karti hai.

---

## [00] Enterprise Objective Prompt (MANDATORY)
Jab bhi code generate/modify ho raha ho, yeh Objective **hamesha** apply hoga (non-negotiable).

**OBJECTIVE:**
Given file/target ke liye COMPLETE, PRODUCTION-READY, ENTERPRISE-GRADE code generate karo jo WebOS ecosystem ke saath DEEPLY INTEGRATED ho.

Design quality targets:
- Multi-App, Multi-Tenant
- High-Performance
- Secure by default
- Future-proof
- AI-auditable (10/10 score target)

Ecosystem assumption (hard):
- Target ko isolated/standalone mat samjho.
- Jahan possible ho, WebOS ke existing concepts/engines/context/routing/config/logging/hooks/lifecycle ko use karo.

Style & Standards (mandatory):
- AliensStyle fully follow ho
- AliensGrade compliance ho
- CleanCode principles strictly apply hon

CodeHeader/CodeFooter gates (mandatory; target-type aware):
- CodeHeader/CodeFooter requirements MUST be applied per AliensStyle + workflow target type.
- If a workflow/target policy requires this exact first line, enforce it:
  - `# Copyright (c) A|iens. All rights reserved.`
- One blank line after it, then full structured CodeHeader including:
  - Engine/Module Name, Version, Author, Author URI, Requires, Description,
    Responsibilities, Dependencies, Integration Notes.
- CodeFooter must include: versioning notes, extension hooks, future compatibility notes.

Enterprise quality bar (non-negotiable):
- Architecture, Security, Performance, Maintainability, Scalability, Readability
- Test-readiness, Observability-readiness, Backward compatibility

Usability goals:
- Easy/short/powerful usage
- Minimal configuration + sensible defaults
- Extensible hooks

---

## [01] Hard Rule: Common vs Unique (Very Important)
**Is file me sirf universal rules rahenge.**

Allowed here (universal):
- Context + manifest reading contract
- Target parsing + normalization contract
- Path resolution interface (how to resolve), not hard-coded per workflow
- Safe edit strategy (create/update rules; no delete)
- Quality gates (lint/format/tests posture)
- Security + approvals + dangerous actions gating
- Output + audit logs contract

NOT allowed here (unique):
- Kisi ek workflow ke unique steps (e.g., “WebSDK_Module_API me file name kaise banana hai”)
- Kisi ek module/app/brand ke specific paths (unless they are defined in ACC.Paths ref)
- Specific prompts/logic that changes behavior only for one workflow

**Reason:**  
Is file ka impact 10,000+ workflows par apply hoga.

---

## [02] Mandatory Includes (in every Singular-Code workflow)
Every Singular-Code workflow MUST require, in this order:

1) `Workflow_Singular.md` (base singular rules)
2) `Workflow_Singular-Code.md` (this file)
3) Domain references as needed (via `_Refs`), never inline hard-coded logic

---

## [03] Inputs Contract (Mandatory)
Singular-Code workflows MUST accept exactly:

- `Targets` (string)  → **single item** expected per run (Plural will split)
- `Description` (string) → human language requirement
- `RunContext` (object) → inherited context (developer + mode + approval + version + theme + permissions)
- `RunItemId` (string) → stable trace id
- `ParentWorkflowId` (string, optional but recommended)

> If the workflow is called directly (not via Plural), it must still build `RunContext` using developer manifest.

---

## [04] Target Normalization (Mandatory)
Detailed grammar: `Workflows/ACC/_Refs/ACC.Targets.md`

Common enforcement:
- Trim whitespace.
- Reject empty target.
- Normalize separators:
  - `Parent/Child` => keep as is; do not rewrite.
  - `WebOS.Cache` => treat as base-name without extension.
- Underscore conventions are semantic (Standard vs Cluster modules) but **type resolution** must be done by workflow id + ACC.Paths mapping.

If target list accidentally contains multiple items (comma), Singular-Code MUST:
- Fail with actionable error (recommended), OR
- If `AllowMultiTarget=true` in YAML header, then it must internally split and behave like a mini-plural (not recommended).

---

## [05] Path Resolution Contract (Mandatory)
Path resolution rules must NOT be hard-coded per individual workflow inside code logic.  
Instead, every Singular-Code workflow MUST resolve target file paths using:

- `ACC.Paths` reference (`Workflows/ACC/_Refs/ACC.Paths.md`)
- Developer manifest `Paths`
- Version selection policy (latest auto-detect; fallback allowed)

### Required outputs of path resolver:
A Singular-Code workflow must compute and log:

- `TargetType` (Engine/ModuleApi/Component/Template/Cron/Webhook/SQL/BrandAsset etc.)
- `TargetRoot` (WebOSRoot/WebAppsRoot/WebBrandRoot/WebSDKRoot etc.)
- `TargetFilePath` (full path to generate/modify)
- `SupportingPaths[]` (css/template/layout/sql/class files, if relevant)

If resolver cannot determine a path:
- Return error with: workflow id, target, expected patterns, and suggested fix (e.g., “use correct Parent/Child format”).

---

## [06] Create vs Update Rules (Mandatory)
### A) Normal workflows (Create allowed)
- If target file does not exist:
  - Create it (unless policy forbids)
- If target file already exists:
  - MUST NOT modify/overwrite under the non-update workflow.
  - Fail fast with actionable error to use the corresponding `*_Update` workflow.
  - ErrorCode: `WF_TARGET_EXISTS_USE_UPDATE`
  - Action: "Run the matching *_Update workflow for this target."

### B) Update workflows (`*_Update`)
**Modify-only**:
- If target file does not exist:
  - FAIL with error “Missing target; Update workflows cannot create.”
- If exists:
  - Modify it safely (minimal diff)

### C) No Delete (Global)
- Never delete any file.
- If something must be “removed”, mark deprecated inside file (comment/header) and keep record in run logs.

---

## [07] Safe Edit Strategy (Mandatory)
Singular-Code workflows MUST follow a safe edit pipeline:

1) **Read existing file** (if present)
2) **Load & verify Planning artifact** (external, saved) before writing
  - The workflow must locate the Planning output produced by `*-Planning`.
  - If planning is missing/unreadable => STOP (no code write).
  - If manual approval is required and not granted => STOP.
3) **Apply minimal patch**:
   - Prefer “insert/replace exact sections” over full rewrite
   - Preserve existing important features (no accidental removal)
4) **Validate output** (format + basic checks)
5) **Write file** (atomic write)
6) **Record diff summary** (in RunRecord)

Recommended patch anchors:
- Section headers
- Stable markers
- Known function/class boundaries

If anchors are missing:
- Use conservative strategy: append new functionality and avoid destructive rewrites.

---

## [08] Example-Driven Generation (Mandatory)
Aapka rule: *“is workflow me 4 example files ke path hongay”* (individual workflow specific).

Common requirements:
- Every Singular-Code workflow MUST declare `ExampleFiles` (>= 4) in its own YAML header.
- The workflow MUST read those examples to match:
  - style
  - folder conventions
  - naming
  - patterns (CRUD usage, facade calls, etc.)

If examples are missing:
- Workflow must NOT guess wildly.
- It must:
  1) log warning “Examples missing”
  2) fallback to highest-level standards in `_Refs` (AliensStyle/AliensGrade references if present)
  3) produce conservative **but enterprise-baseline** code (complete contracts, validation, error model, logging, footer docs)

---

## [09] Code Standards Contract (Mandatory)
This is the universal contract; exact standards depend on target type:

### A) PHP targets
- Must follow **AliensStyle** (FULL mandatory rules, not partial)
- Must respect **AliensCRUD** if CRUD logic is needed
- Must not delete/rename existing important features
- Must keep naming conventions consistent with your ecosystem

AliensStyle enforcement (hard gate):
- If an individual workflow declares `Quality.MustFollow: AliensStyle` in its YAML header (or equivalent policy), it MUST:
  1) read the canonical style reference:
     - `/Aliens/Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Skills/AliensStyle.md`
  2) apply **every mandatory** AliensStyle rule applicable to the target type
  3) treat missing mandatory style elements as a **quality gate failure** (do not ship)

Minimum checks (best-effort, language/target dependent):
- Header + Footer present (AliensStyle CodeHeader/CodeFooter)
- Version footprint + `LastModified` present in header
- Naming convention applied (PascalCase where required)
- Logging standard applied (and guarded where required by platform)

### B) JS targets
- Must align with WebOS frontend facade conventions (if applicable)
- Must avoid breaking global engine loader patterns

### C) SQL targets
- Must be migration-safe
- Must include rollback guidance where possible
- Must never run destructive changes without approval

### D) Bash targets
- Must be idempotent and safe by default
- Must log actions, avoid silent failures

> Note: Exact style refs should live in `_Refs` so this common file stays generic.

---

## [10] Security + Approvals (Mandatory)
Rules source of truth: `Workflows/ACC/_Refs/ACC.Safety.md` + developer `manifest.json`

Singular-Code workflow must classify its actions:
- `ProdWrite`? (writing into production code paths)
- `DbTouch`? (SQL generation/execution)
- `MassChange`? (many files / wide scope)

Approval behavior:
- If manifest `ApprovalMode=manual` and action is dangerous:
  - Create approval request under:
    `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`
  - Stop before writing dangerous output (unless policy says write-to-draft only)
- If `ApprovalMode=auto`:
  - Proceed, but log “auto-approved” decision

Secrets:
- Never write secrets into repository.
- Use `.Alien/{Developer}/Secrets/` (gitignored) for tokens/keys.

---

## [11] Quality Gates (Mandatory)
Every Singular-Code workflow MUST run quality checks appropriate for the target:

Minimum checks:
- File exists after write
- Syntax sanity (basic lint/parsing if available)
- Consistent formatting
- No obvious placeholder strings left (e.g., “TODO: remove” in production sections) unless explicitly allowed

### [11.B] New Global Hard Gates (Enterprise Policy)
These gates are mandatory across all code workflows:

1) **Planning-first gate**
- Planning artifact must exist and must be enterprise-grade (feature coverage matrix + multi-app/multi-tenant/perf/security/audit plan).

2) **No skeleton / No empty outputs**
- Empty files or trivial skeleton-only files are forbidden.
- If the implementation is incomplete relative to the plan => FAIL.

3) **“Large file” meaning (clarification)**
- “Large” is not random bloat; it means **feature-complete**.
- When the plan covers all relevant features, the resulting code naturally becomes comprehensive (similar to existing large WebOS engines).

4) **No artificial shrinking / summarization**
- Do not compress implementation to fit “small output”.
- If the requested scope implies an engine/module/component, the output must include all required sections and contracts.

### [11.C] Enterprise Completeness Checklist (Scope-Based)
This is a universal checklist to prevent tiny/skeleton outputs.

If the target is a *new* Engine/Module/Component (or a major expansion), the generated code should normally include:
- Clear public contract (inputs/outputs) + consistent error envelope
- Input validation + normalization
- Config surface + defaults
- Security posture (authZ/authN assumptions, secret handling, injection safety)
- Observability (debug guard, structured logs, deterministic tracing hooks)
- Performance posture (caching notes, batching, timeouts, limits)
- Extensibility points (hooks/events/overrides)
- Backward compatibility notes (when updating)
- Code-Footer with usage + troubleshooting + changelog notes

If any of the above is required by the plan/Description and is missing, the workflow must FAIL (do not ship a tiny file).

Recommended checks (when tooling available):
- PHP: lint + static checks
- JS: lint + basic build check (if configured)
- SQL: validate syntax + migration review notes
- Bash: shellcheck-like validation (if available)

Output must include a short “QualitySummary” in RunRecord:
- `QualityScore` (0-10)
- `Warnings[]`
- `FixSuggestions[]`

---

## [12] Outputs + Audit (Mandatory)
Detailed schema: `Workflows/ACC/_Refs/ACC.Output.md`

Common requirements for Singular-Code:
- Write the code file to resolved `TargetFilePath`
- Save run artifacts:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`
    - `input.json`
    - `context.json`
    - `plan.md` (short)
    - `diff-summary.md`
    - `result.json`

Also:
- If workflow generated additional helper files (css/sql/sections), list them in `CreatedFiles[]`/`UpdatedFiles[]`.

---

## [13] Error Model (Mandatory)
Errors must be actionable. Every error MUST include:
- `WorkflowId`
- `Target`
- `WhatFailed`
- `WhyLikely`
- `HowToFix` (exact next step)

Common failure cases:
- Invalid target grammar
- Path cannot be resolved
- Missing file for `_Update`
- Approval required but not granted (manual mode)
- Conflicting concurrent lock detected

---

## [14] EmployeeMode Behavior (Important)
If `Mode=employee`:
- Workflow must respect locks and state files
- It must never block forever
- If approval is required:
  - Create approval request
  - Mark task as “waiting-approval” in run record
  - Return control to worker loop

---

## [15] Change Control
Because this file affects 10,000+ workflows, any change MUST:
- Be backward compatible where possible
- Be documented in changelog
- Prefer adding new optional rules over changing defaults

### Changelog
- 26.00.00 (2025-12-24): Initial Common Rules for Singular Code Generation
