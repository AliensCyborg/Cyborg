---
WorkflowId: Workflow_Singular-Reaudit
Type: CommonRuleFile
Scope: ACC.Workflow.Singular.Reaudit
Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-18
Author: AlienCyborg
Purpose: >
  Common rules for every Singular-Reaudit workflow. This file is NOT an executable workflow
  by itself. It defines the post-Code verification contract: cross-check that every
  "Required Item" extracted by Planning from the user's Description was actually applied
  in Code, and that NO out-of-scope changes were introduced.
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
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "/Aliens/manifest.json"
NonNegotiables:
  - NoDelete: true
  - ProdWrite: false
  - DbTouch: "not-required"
  - ReadOnlyOnCode: true
  - QualityOverSpeed: true
---

# Workflow_Singular-Reaudit.md

Yeh file *workflow nahi hai*. Yeh **Singular-Reaudit** type ke sabhi workflows ke liye
ek common rule-engine hai. Reaudit ka kaam: Code stage ke baad **prove** karna ki
Description -> Planning -> Code chain me jo bhi "Required Item" tha, woh **actually
apply** hua, aur saath hi koi **out-of-scope change nahi** hua.

> Reaudit ka meaning:
> Audit (pre-code, read-only baseline) ke baad Code chala. Reaudit (post-code, read-only
> verification) ye verify karta hai ki Description ke saare promises pure hue ya nahi.
> Agar koi item missing/partial ho ya scope-creep mile, to orchestrator ko loop wapis
> Planning par bhej deta hai.

---

## [00] Hard Rule: Read-only on Code (Non-Negotiable)
- Reaudit code ko **modify nahi** karega. Pure verification step hai.
- Reaudit ke output sirf RunRecord aur deterministic Reaudit artifact me likhe jayenge.
- Agar Reaudit ke dauran lagta hai code me change chahiye => Reaudit `noncompliant`
  return karega aur orchestrator wapas `Planning` -> `Code` chalayega.

---

## [01] Required Inputs (Mandatory)
Reaudit MUST consume:

1. **Original Description** (jo workflow ko diya gaya tha)
2. **Planning Required Items** (Planning artifact ka structured checklist)
3. **Pre-Code Audit artifact** (baseline) — *required only when `ReauditMode: update`*
4. **Post-Code state** (current files on disk + diff summary if available)

### 1.1 Mode Selector (`ReauditMode`)
Each domain Reaudit child MUST declare a `ReauditMode` in its YAML header:

- `update`  : Parent workflow modifies an EXISTING file. Pre-Code Audit artifact
              MUST exist. Path resolution: see `Workflow_Plural-Update.md`.
- `create`  : Parent workflow creates a NEW file (or wires a new part). Pre-Code
              Audit artifact is OPTIONAL; absence is not an error. Reaudit
              scores against Planning's Required Items + the file skeleton
              baseline declared in the domain child's verification catalog.

If `ReauditMode` is missing => default to `update` (safer, stricter).

### 1.2 Missing Inputs
Agar inputs me se koi missing/unreadable ho (per the mode rules above):
- Reaudit fail karega `ACC_ERR_REAUDIT_INPUT_MISSING` ke saath.
- Code modify nahi hoga.

> Note: in `create` mode, Description MAY be empty (parent's input contract may
> allow it). In that case Planning MUST still emit a `## Required Items` block
> driven by the domain child's baseline catalog; Reaudit then scores against
> that baseline-only RI set. An empty Description is NOT by itself a Reaudit
> failure in `create` mode.

---

## [02] Required Items Contract (Planning -> Reaudit handshake)
Planning artifact me ek explicit, numbered `Required Items` block hona MUST hai.
Format (markdown, machine-checkable):

```
## Required Items
- RI-001: <short imperative; what MUST be present in code after this run>
  - acceptance: <observable check; how Reaudit will verify>
  - source: Description line "<quote or paraphrase>"
- RI-002: ...
```

Rules:
- Har RI ka unique id (`RI-NNN`) mandatory.
- `acceptance` field machine ya developer-eye se check ho sake aisa hona chahiye
  (e.g., "function `Address_HistoryStatus` ka body me `RowStatus(... , $Array)` call ho").
- `source` Description ka traceable reference rakhe (line / paragraph / quote).
- Planning Required Items ki list `Description` se derive hogi, agent ki marzi se nahi.

Agar `Required Items` block missing hai => Planning quality fail; Reaudit
`ACC_ERR_REAUDIT_INPUT_MISSING` return karega aur orchestrator Planning ko
dobara chalayega.

---

## [03] Compliance Matrix (Mandatory Output)
Reaudit MUST produce a Compliance Matrix per run-item.

### 3.1 Per-item status enum
Each `RI-NNN` -> exactly one of:
- `applied`  : Acceptance check fully satisfied in current code.
- `partial`  : Some aspect satisfied, some not. Add brief gap note.
- `missing`  : Not applied at all (or removed).
- `blocked`  : Cannot verify because of an environmental issue (rare).
              Must include reason and fix hint.

### 3.2 Out-of-Scope Changes
Reaudit MUST list `OutOfScopeChanges[]`: any code-level change that is **not**
traceable to any RI in Planning.

Each entry includes:
- file path
- short description of the change
- severity (`info` | `warning` | `violation`)
- recommendation (`keep_with_approval` | `revert` | `move_to_planning_then_code`)

### 3.3 Final Verdict
- `compliant`     : every RI is `applied` AND `OutOfScopeChanges[]` has no `violation`.
- `noncompliant`  : any RI is `partial`/`missing`/`blocked`,
                    OR any `OutOfScopeChanges[]` entry is severity `violation`.

---

## [04] Output Contract (Mandatory)
Reaudit MUST write two artifacts per run-item:

1. Run-scoped (audit trail):
   - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Reaudit.md`
   - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Reaudit.json`

2. Deterministic (downstream-friendly):
   - The parent workflow's planning folder MUST also receive the same compliance
     matrix as `Reaudit.md` so future runs / loop iterations can read it without
     hunting through RunIds. The parent workflow defines the exact filename
     (e.g. `Class.Update.reaudit.md` for WebSDK module class update).

Both files MUST contain:
- Header (workflow id, run id, item id, target, iteration count)
- Verdict (`compliant` | `noncompliant`)
- Compliance Matrix table (RI-NNN | status | evidence | gap note)
- OutOfScopeChanges list
- Loop instruction (if `noncompliant`): which Planning sections to revisit + why
- Iteration history (append-only; do not erase past iterations)

---

## [05] Loop Semantics (Pipeline Contract)
Pipeline (Update workflows): `Audit -> Planning -> Code -> Reaudit -> Documentation`

Loop rules:
- If Reaudit verdict is `compliant` => orchestrator proceeds to Documentation.
- If `noncompliant` => orchestrator MUST jump back to **Planning** (not directly Code),
  passing the latest Reaudit artifact as additional context. Planning then refines
  Required Items and Code re-runs. Then Reaudit runs again.
- Iterations have **no time limit** by default (per A|iens "Quality Over Speed" rule).
  Quality is the gate, not iteration count.

Safety bounds (defensive only, not a quality shortcut):
- A workflow MAY define `MaxReauditIterations` in its YAML header (advisory).
- If the bound is hit, Reaudit returns `ACC_ERR_REAUDIT_MAX_ITERATIONS` AND
  produces a "Stuck Report" with the unresolved RIs. Orchestrator stops and
  surfaces the report for human decision (approval to deviate, or instruction
  to keep iterating). Exceeding the bound is **never** auto-approved.

---

## [06] Strict No-Modify on Code (Non-Negotiable)
- Reaudit MUST NOT call any *-Code workflow.
- Reaudit MUST NOT write to module/class/source files.
- If Reaudit needs more evidence, it can only **read** more files and add them
  to its compliance evidence section.

---

## [07] Approvals
- Reaudit itself does not require approval (read-only).
- However, if `OutOfScopeChanges[]` has any `violation` entry, the loop-back
  to Planning is mandatory. To accept a deviation **without** revert, manifest
  approval (`ApprovalMode=manual`) is required and the approval id MUST be
  recorded in the Reaudit artifact.

---

## [08] Errors (Common Set)
- `ACC_ERR_REAUDIT_INPUT_MISSING` (one of the 4 required inputs not found)
- `ACC_ERR_REAUDIT_NONCOMPLIANCE` (verdict is noncompliant; orchestrator loops back)
- `ACC_ERR_REAUDIT_MAX_ITERATIONS` (advisory bound reached; human gate)
- `ACC_ERR_SCOPE_DEVIATION` (a violation-grade out-of-scope change persisted)

Detailed definitions live in `Workflows/ACC/_Refs/ACC.Errors.md`.

---

## [09] Acceptance Criteria (for any Singular-Reaudit child)
A concrete `*-Reaudit` workflow is acceptable only if:
- It includes this common file via `require_once`.
- It defines a deterministic Reaudit artifact path (per parent workflow's planning folder).
- It binds explicit acceptance checks to each Planning RI.
- It documents iteration handling (where the iteration counter lives).

## Changelog
- 26.00.00 (2026-04-18): Initial common rules for Singular-Reaudit. Author: AlienCyborg.
