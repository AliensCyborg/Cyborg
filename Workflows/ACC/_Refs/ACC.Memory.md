---
RefId: ACC.Memory
Type: ACC.Reference
Scope: ConversationAndTaskMemory
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-27
Audience: "AlienCyborg Agent + ACC Workflow Authors"
NonNegotiables:
  - NoCrossAppMixing
  - MemoryNeverOverridesPrompt
  - ScopeIsolationRequired
  - NoSecretsInMemory
---

# ACC.Memory (SSOT)

## [01] Purpose
This reference defines an **enterprise-grade, scoped Memory system** for AlienCyborg.

Goal:
- Store **small, high-signal** knowledge from ongoing work (conversation + workflow runs)
- Make future work faster and more consistent
- **Never** reduce quality or cause cross-app feature mixing

Memory is **supporting context**, not a source of truth.
Primary SSOT remains:
- Current `Description` (prompt payload)
- Workflow SSOT docs
- Files on disk

---

## [01.1] Team Usage Model (10% Human, 90% AI Agents)
This Memory is designed mainly for **AI agents** to maintain continuity across:
- chat history changes
- new computer / fresh session
- long-running projects where context gets lost

### Human usage (~10%)
Humans usually read only:
- `Summary.short.md` to understand “what happened” quickly
- the latest entry timestamp to know “where we left off”

### AI agent usage (~90%)
Agents will use memory more often to:
- recover last known state
- avoid repeating work
- stay consistent with past decisions/constraints

**Hard rule:** Even for agents, memory NEVER overrides the current `Description` + workflow SSOT + disk reality.

---

## [01.2] Continuity / Resume Goal (Why this exists)
If you (or any developer) switch chats or computers, Memory should help an AI agent answer:
1) “Hum kya kar rahe the?”
2) “Last time kahan chhoda tha?”
3) “Ab next kya karna hai?”

To support this, every entry should be written in a way that makes the next step discoverable:
- link to RunId / artifacts where possible
- keep summaries crisp, high-signal
- avoid noise

---

## [02] Storage Root (SSOT)
- `DevMemoryRoot` = `/Aliens/.Alien/{Developer_Username}/Memory/`

All memory entries MUST be written under this root.
Do not store memory under:
- `/Aliens/Project/` (planner CSV only)
- any repo code folders

---

## [03] Scope Model (Anti-Mixing Core Rule)
Every memory entry MUST have exactly one `ScopeKey`.

### Allowed `ScopeKey` formats (safe folder naming)
- `Global.Governance`  *(cross-cutting rules only: paths, safety, schema, quality bars)*
- `WebApp.{AppName}`
- `WebSDK.Module.{ModuleName}`
- `WebOS.Engine.{EngineBaseName}`  *(example: WebOS.Cache)*
- `Workflow.{WorkflowId}` *(rare; for workflow-design meta decisions)*

### Forbidden patterns
- Mixing multiple apps in one ScopeKey (example: `WebApp.Exchange+Wallet`)
- Generic keys like `Global` for app-specific decisions

### Scope Precedence
- If a task is about a specific App/Module/Engine, use the specific scope.
- Use `Global.Governance` ONLY for rules that are truly universal.

---

## [04] Memory Entry Types (What to store)
Memory must be **high-signal**. Prefer structured facts over long prose.

### Recommended Types
1) `Decision` — a committed decision that impacts future work
2) `Constraint` — non-negotiable rule discovered or added
3) `Pattern` — a repeated implementation convention (naming, folder rules, schema)
4) `Pitfall` — known failure mode + how to avoid
5) `OpenQuestion` — unresolved item needing user confirmation
6) `RunSummary` — short record of a completed workflow run (what changed)

### Forbidden Types
- Raw secrets / tokens / credentials
- Large code dumps
- Unverified guesses or speculation

---

## [05] Entry Format (SSOT)
Each memory entry MUST produce:
- `Summary.short.md` (5–12 lines, crisp)
- `Summary.detail.md` (detailed; decisions, constraints, rationale)
- `Meta.json` (machine-readable)

### Directory layout (deterministic)
Store each entry in its own folder:
- `/Aliens/.Alien/{Developer_Username}/Memory/{ScopeKey}/Entries/{YYYY}/{YYYY-MM}/{RunStamp}/`

Where:
- `{RunStamp}` follows ACC determinism guidance (ref: `Workflows/ACC/_WorkflowFormula.md`)

### Meta.json (minimum keys)
```json
{
  "schema": "ACC.Memory.Entry.v1",
  "timestamp_utc": "2026-01-27T12:34:56Z",
  "scope_key": "WebApp.Exchange",
  "entry_type": "Decision",
  "title": "Planner CSV root is developer-scoped",
  "source": {
    "workflow_id": "WebApp-Planner",
    "targets": "Exchange",
    "run_id": "...",
    "artifacts": []
  },
  "tags": ["governance", "paths"],
  "constraints": ["NoCrossAppMixing"],
  "confidence": "high"
}
```

---

## [06] Read Rules (When memory can be used)
Memory may be read ONLY when all are true:
- Current task scope is known (App/Module/Engine)
- The memory ScopeKey exactly matches the task scope OR is `Global.Governance`
- The memory entry is consistent with current SSOT (workflows + disk)

### Memory MUST NOT override
If memory conflicts with the current `Description` or SSOT docs:
- Ignore memory
- Fail-fast and ask for clarification

---

## [07] Write Rules (When memory should be saved)
Write memory at the end of a meaningful chunk:
- A rule/policy was introduced or changed
- A workflow schema/path SSOT was corrected
- A non-trivial design decision was made
- A bug root-cause was discovered
- A risky update was completed with constraints

Do NOT write memory for:
- trivial status updates
- transient debugging noise

---

## [08] Indexing + Retention
- Index is optional but recommended for large memory sets.
- Default retention: keep entries; do not delete.
- If pruning is required, archive (move) to `Archive/` under the same scope.

---

## [09] Related SSOT
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Refs/ACC.Manifest.md`
- `Workflows/ACC/_WorkflowFormula.md`

---

# END
