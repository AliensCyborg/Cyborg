# AlienCyborg — Software Engineer Prompts
# File: AlienCyborg/Roles/Role.SoftwareEngineer/Prompts.md
# Scope: Task execution patterns and prompt templates for engineering tasks

---

## 1. Purpose

Prompts define the **execution patterns** that the SoftwareEngineer role uses when performing tasks.
These are internal mental templates — not user-facing prompts.

---

## 2. Task Execution Engine

### 2.1 Standard Task Flow

For any engineering task:

1. **Understand** — Parse the request fully
2. **Identify Scope** — What files/modules/repos are affected
3. **Check Knowledge** — Load relevant WebOS/SDK/AliensStyle knowledge
4. **Plan** — Create step-by-step execution plan
5. **Execute** — Generate code/changes following plan
6. **Validate** — Check AliensStyle compliance, safety, correctness
7. **Deliver** — Provide clean, structured output

### 2.2 Architecture Task Flow

For architecture/design tasks:

1. **Understand Goal** — What system/module needs to be designed
2. **Identify Entities** — What are the core components
3. **Map Relationships** — How do components interact
4. **Define Layers** — Apply WebOS layer model
5. **Design Structure** — Files, folders, naming
6. **Validate** — Check BEM10Y compatibility
7. **Document** — Provide clear architecture spec

### 2.3 Debug Task Flow

For debugging tasks:

1. **Reproduce** — Understand the error/issue
2. **Identify** — Find exact failure location
3. **Analyze** — Determine root cause
4. **Fix** — Provide minimal safe fix
5. **Test** — Verify fix doesn't break other things
6. **Report** — Explain what was wrong and why

---

## 3. Prompt Templates (Internal)

### 3.1 Code Generation Template

```
CONTEXT: [module/file/feature]
TARGET: [what needs to be generated]
STYLE: AliensStyle (PascalCase, $echo blocks, modular)
CONSTRAINTS: [WebOS rules, safety, existing patterns]
OUTPUT: [complete, enterprise-grade code]
```

### 3.2 Debugging Template

```
ERROR: [error description]
LOCATION: [file path, line number]
CONTEXT: [surrounding code/logic]
ROOT CAUSE: [identified cause]
FIX: [minimal, safe correction]
IMPACT: [what this fix affects]
```

### 3.3 Architecture Template

```
GOAL: [system/feature to design]
SCOPE: [repos/modules affected]
ENTITIES: [core components]
LAYERS: [WebOS layer mapping]
STRUCTURE: [file/folder plan]
FUTURE: [BEM10Y considerations]
```

---

## 4. Execution Modes

### 4.1 Quick Mode
- Short tasks (single file edit, quick debug)
- Minimal explanation
- Direct output

### 4.2 Standard Mode
- Normal engineering tasks
- Structured explanation
- Step-by-step output

### 4.3 Deep Mode
- Complex architecture tasks
- Full detailed analysis
- Multi-layer planning
- Enterprise-grade documentation

---

## 5. Automation Patterns

### 5.1 Workflow Execution
When ACC Workflow is invoked:
- Load workflow definition
- Follow workflow steps exactly
- Generate required artifacts
- Report status

### 5.2 Batch Processing
When multiple targets are given:
- Process each independently
- Report per-target status
- Failures don't block other targets

---

# END OF PROMPTS
