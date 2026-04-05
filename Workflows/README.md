# AlienCyborg — Workflows
# Location: AlienCyborg/Workflows/
# Scope: Self-contained workflow execution reference for AlienCyborg

---

## 1. Workflow Source

AlienCyborg uses **self-contained workflow definitions** maintained in:
```
Cyborg/AlienCyborg/Workflows/
```

Key workflow files:
- `TaskTypes.md` — All supported task types and their definitions
- `Pipelines.md` — Master execution pipelines

AlienCyborg is fully self-contained — no external cyborg paths are needed.

---

## 2. Self-Contained Architecture

- All workflow definitions are part of the AlienCyborg installation
- No external dependencies on other cyborg folders
- Updates are maintained within AlienCyborg's own Workflows/ directory
- Consistent execution independent of other installations

---

## 3. AlienCyborg Workflow Permissions

### Allowed
- Execute workflows as defined in this directory
- Follow workflow steps exactly
- Generate required artifacts (code, docs, planning)
- Report workflow status

### Restricted
- Cannot override workflow safety rules
- Cannot skip mandatory workflow steps
- Destructive operations require explicit authorization

---

## 4. Workflow Categories Available

| Category | Examples |
|----------|---------|
| Content | Blog, Wiki, Course, Role, Skill workflows |
| WebApp | Screen, Section, Component, CSS, Layout workflows |
| WebOS | Engine (PHP, JS, Bash, SQL) workflows |
| WebSDK | Module (API, Class, Component, Cron, JS, Screen, SQL) workflows |
| Git | Push, Pull, Clone, Stash |
| System | Report, WorkflowNew |
| Common | Docs, Meta, BugFix workflows |

---

## 5. Execution Rules

- Always read the workflow SSOT before executing
- Follow all safety gates (Manifest Gate, Target Validation, etc.)
- Never skip steps
- Report status clearly after execution
- Fail fast on missing data (description, targets, etc.)

---

# END OF WORKFLOWS README
