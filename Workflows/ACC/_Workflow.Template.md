# ACC Workflow Template (v26.00.00)
# File: Workflows/ACC/_Workflow.Template.md
# Part of: ACC (AlienCyborg Codes)
# Agent: AlienCyborg
# Scope: Global – applies to all ACC workflow files (Plural + Singular variants)
# LastUpdated: 2025-12-24

---

## 1) Purpose

This file defines the **standard, mandatory structure** for every ACC workflow file.

ACC Workflow file is:
- Not a “prompt dump”
- Not a random script
- Not a loose checklist

ACC Workflow file is a **deterministic execution contract** between:
- Human intent (Targets + Description)
- ACC language (`Workflow(WorkflowId, Targets, Description)`)
- AlienCyborg Agent (VS Code Copilot Agent / Azure AI / WebOS backend runners)
- Aliens Ecosystem (WebOS / WebSDK / WebApps / WebBrand)

---

## 2) ACC Workflow Call (Public Contract)

ACC executes a workflow using:

`Workflow(WorkflowId, Targets, Description)`

Where:
- **WorkflowId** → Predefined workflow file id (maps to one `.md` file)
- **Targets** → Target scope (single or comma-separated list)
- **Description** → Human intent (any language; constraints, quality, requirements)

### Mandatory Enterprise Objective (Planning + Code)
Jab bhi planning banai jaye ya code generate/modify ho, `Description` ko sirf “nice-to-have” mat samjho.
Isme yeh **objective** enforce karna mandatory hai:

OBJECTIVE:
- COMPLETE, PRODUCTION-READY, ENTERPRISE-GRADE output
- WebOS ecosystem ke saath DEEPLY INTEGRATED (not standalone)
- Multi-App, Multi-Tenant, High-Performance, Secure-by-default, Future-proof
- AI-auditable (10/10 score target)

Standards (mandatory): AliensStyle + AliensGrade + CleanCode

### Targets grammar (high level)
- Comma `,` means: **run the same workflow multiple times** (one run per item).
- Slash `Parent/Child` means: relationship (resolved by WorkflowId).
  - Example:
    - `WebSDK_Module_API + User/UserNew` → Module=User, API=UserNew
    - `WebApp_Component + Exchange/Balance` → App=Exchange, Component=Balance
- Dot `WebOS.Cache` means: **base filename without extension**; extension decided by workflow.
- Underscore rule (Module/Theme naming):
  - `User` → Standard module (typically 1 table, 1 main class)
  - `User_` (underscore present in base) → Cluster module (multiple tables with prefix)

Detailed rules MUST live in:
- `Workflows/ACC/_Refs/ACC.Targets.md`

---

## 3) Workflow File Types (Mandatory)

ACC has 5 core types:

1) **Workflow_Plural**  
   Orchestrator workflow. Calls one or more child workflows (Plural/Singular) in a loop over targets.

2) **Workflow_Singular**  
   Executes one bounded task for a single target item. (Can be called directly or by Plural.)

3) **Workflow_Singular-Planning**  
   Generates planning artifacts for a single target item.

4) **Workflow_Singular-Code**  
   Generates/updates code artifacts for a single target item.

5) **Workflow_Singular-Documentation**  
   Generates/updates documentation artifacts for a single target item.

> Common Engine Files are NOT executable workflows.
> They are shared rule sets required by individual workflow files.

Common Engines:
- `Workflows/ACC/_Common/Workflow_Plural.md`
- `Workflows/ACC/_Common/Workflow_Singular.md`
- `Workflows/ACC/_Common/Workflow_Singular-Planning.md`
- `Workflows/ACC/_Common/Workflow_Singular-Code.md`
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## 4) Mandatory YAML Header (Strict)

Every workflow file MUST start with a YAML block like this:

```yaml
WorkflowId: <UNIQUE_WORKFLOW_ID>
Title: <Human readable name>
Category: <WebOS | WebSDK | WebApps | WebBrand | System>
Type: <Workflow_Plural | Workflow_Singular | Workflow_Singular-Planning | Workflow_Singular-Code | Workflow_Singular-Documentation>

Status: <Draft | Stable | Deprecated>
Version: 26.00.00
LastUpdated: 2025-12-24

Intent:
  Summary: >
    <One paragraph explaining what this workflow achieves>
  WhenToUse:
    - <Use case 1>
    - <Use case 2>

Inputs:
  Targets:
    Format: "<comma-separated | single>"
    Examples:
      - "Exchange/Balance, Exchange/Withdraws"
      - "User/UserNew, Web3/Web3_Accounts"
  Description:
    Format: "Any human language"
    Examples:
      - "Premium UI hona chahiye, enterprise rules follow karo"
      - "Secure by default, best performance"

Defaults:
  Version: "latest"         # if not resolvable, fallback allowed
  VersionFallback: "PHP.26.00.00"
  Theme: "_Basic"

Permissions:
  NoDelete: true
  CanCreateFiles: true
  CanEditFiles: true
  CanModifyProd: true
  CanTouchDB: true

Approval:
  Mode: "manifest"          # manifest decides: auto/manual
  RequiredActions:
    - "prod_write"
    - "db_touch"

Requires:
  Common:
    - "Workflows/ACC/_Common/<CommonEngine>.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
  EcosystemRefs:
    - "Workflows/ACC/_Refs/WebOS_Engines.md"
    - "Workflows/ACC/_Refs/WebOS_System.md"
    - "Workflows/ACC/_Refs/WebSDK.md"

Calls:
  ChildWorkflows:
    - "<WorkflowId>-Planning"
    - "<WorkflowId>-Code"
    - "<WorkflowId>-Documentation"

Outputs:
  RunRecord: true
  Planning: true
  Code: true
  Documentation: true
```

### YAML hard rules
- `WorkflowId` must be globally unique.
- `Type` must match the actual file’s role.
- `NoDelete` must always be true (global policy).
- Any file that is a common engine must use `Type: CommonRuleFile` and must NOT claim to be executable.

---

## 5) Common Require Rules (Mandatory)

Each workflow file MUST explicitly include its common engines.

### If Type = Workflow_Plural
Must include:
- `Workflows/ACC/_Common/Workflow_Plural.md`

### If Type starts with Workflow_Singular*
Must include:
- `Workflows/ACC/_Common/Workflow_Singular.md`
- plus one of:
  - `Workflows/ACC/_Common/Workflow_Singular-Planning.md`
  - `Workflows/ACC/_Common/Workflow_Singular-Code.md`
  - `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

> Unique rules MUST remain inside the individual workflow file.
> Common engines MUST contain only global rules that apply to 10,000+ workflows.

---

## 6) Execution Lifecycle (Canonical)

### A) Plural workflow lifecycle (Orchestrator)
1. Load Developer Context (from `/Aliens/manifest.json`)
2. Validate permissions + mode (auto/manual)
3. Parse + normalize Targets (ordered list; allow duplicates)
4. For each TargetItem:
   - Call `-Planning`
   - Call `-Code`
   - Call `-Documentation`
5. Save RunRecord + Summary

### B) Singular workflow lifecycle (One TargetItem)
1. Receive payload (single TargetItem + Description + Context)
2. Resolve paths (WebOS/WebApps/WebBrand/WebSDK)
3. Validate existence rules (Update workflows: missing → error)
4. Generate artifact(s)
5. Save artifact(s) + Update RunRecord

---

## 7) Output & Save Rules (Mandatory)

Global output contract is defined in:
- `Workflows/ACC/_Refs/ACC.Output.md`

Minimum required locations:
- Run records:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/...`
- Planning outputs:
  - `/Aliens/.Alien/{Developer}/Planning/...`
- Documentation outputs:
  - `/Aliens/Docs/<Domain>/...` (domain derived by workflow category)

---

## 8) Safety & Approval Rules (Mandatory)

Global safety rules are defined in:
- `Workflows/ACC/_Refs/ACC.Safety.md`

Workflow must obey developer manifest:
- If `ApprovalMode=manual` → create approval request and block risky actions.
- If `ApprovalMode=auto` → continue, but log “auto approved” decision.

Never allowed:
- Deleting files (NoDelete=true).
- Writing secrets to tracked repo.
- Destructive DB changes without explicit approval policy.

---

## 9) Quality Rules (Mandatory)

Quality definition must be measurable and aligned with:
- `Workflows/ACC/_Refs/ACC.UI.Premium.md` (for UI targets)
- AliensStyle / AliensGrade (for code targets, referenced by the Singular-Code common engine)

Workflow must:
- Extract quality expectation from Description (e.g., Premium)
- Apply relevant standards
- Report quality checks in final summary

---

## 10) Workflow Body Template (Steps Section)

Every executable workflow file MUST contain a steps block in markdown:

```md
## Steps
1) Context load + validation
2) Targets normalization
3) Pre-checks (existence / update policy)
4) Planning / Code / Docs actions (as per Type)
5) Output save + run record update
6) Summary + next actions
```

Plural workflows will additionally define:

```md
## Calls
- <ChildWorkflowId>-Planning
- <ChildWorkflowId>-Code
- <ChildWorkflowId>-Documentation
```

---

## 11) Hard Constraints (Non-Negotiable)

- One workflow = one `.md` file (WorkflowId maps to exactly one file).
- Common engine files are NOT workflows; they are shared rule sets.
- NoDelete = true (always).
- Update workflows: modify-only; missing target must error.
- Defaults: Version=latest (fallback PHP.26.00.00), Theme=_Basic.
- Comma means “repeat runs” (do not auto-deduplicate unless explicitly configured).

---

## 12) Recommended Companion Files (Global)
(Highly useful for scaling to 10,000+ workflows)

- `Workflows/ACC/_Refs/ACC.Workflows.Index.md`
- `Workflows/ACC/_Refs/ACC.Workflows.Index.json`

These enable:
- Fast lookup of WorkflowId → file path
- Capabilities / permissions discovery
- Tooling integration for VS Code and backend

---
