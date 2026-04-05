# CodingRules — Software Engineer Role (SSOT)

This file is the **coding discipline expansion** of:
- `Roles/Role.SoftwareEngineer/Rules.md` → Section 3 (Coding Rules)

It exists so that AI + developers have a **single, explicit, repeatable** checklist for writing production-grade code in the Aliens ecosystem.

---

## 1) Primary Goal
Produce code that is:
- **Correct** (zero guessing)
- **Predictable** (no surprises)
- **Enterprise-grade** (handles edge-cases, validation, failure modes)
- **WebOS-aligned** (Boot → System → Kernel → Unit → OS → Event → App)
- **AliensStyle-compliant** (structure + naming + PHP echo tracing)

---

## 2) Naming & Structure Rules (Non‑Negotiable)

### 2.1 PascalCase
- Files containing engines/classes use **PascalCase**.
- Public functions/methods use **PascalCase** unless the codebase pattern forces otherwise.

### 2.2 Suffix Conventions
Use explicit suffixes so intent is instantly readable:
- `*Engine`, `*Service`, `*Adapter`, `*Client`, `*Helper`, `*Validator`, `*Repository`

### 2.3 No New Folder Names (SSOT)
- Never invent new top-level folders.
- Respect canonical roots:
  - `/Aliens/WebOS/`
  - `/Aliens/WebApp/`
  - `/Aliens/WebBrand/`
  - `/Aliens/WebSDK/`
  - `/Aliens/WebDB/`
  - `/Aliens/Docs/`
  - `/Aliens/.Alien/{Developer}/...`

If a folder is missing, create it only when it is **explicitly part of SSOT**.

---

## 3) PHP Coding Rules (AliensStyle)

### 3.1 Echo-based trace (mandatory for complex flows)
- Use `$echo = ''` to narrate **steps**.
- Append step messages at key decisions.
- Keep messages deterministic and helpful.

### 3.2 Input + Validation
- Never trust `$_GET/$_POST` directly.
- Normalize names (e.g. `ComponentNameNormalized`) consistently.
- Validate:
  - required keys
  - data types
  - allowed values
  - length constraints

### 3.3 Database Access
- Default rule: **Row Engine only**.
- No raw SQL unless explicitly required.
- Enforce ecosystem + tenant scoping for every DB operation.
- Follow status lifecycle:
  - `1` active
  - `0` inactive
  - `9` soft deleted

### 3.4 Error Handling
- Prefer explicit error objects/messages over silent failures.
- `_Update` workflows must not create new targets.

---

## 4) JavaScript Coding Rules

### 4.1 Predictability
- Avoid hidden side effects.
- Prefer small, named functions.
- Avoid global state unless the codebase mandates a global WebOS namespace.

### 4.2 DOM + UI
- Never assume element exists; validate selector results.
- Keep UI code resilient:
  - empty state
  - loading state
  - error state

---

## 5) Workflow-safe Coding

### 5.1 Pre-write checklist
Before creating/editing any file:
- Confirm the **canonical root** (e.g. `/Aliens/WebApp/` not `/Aliens/WebApps/`).
- Confirm **Create vs Update** rules.
- Confirm target path is inside allowed roots.

### 5.2 Post-write checklist
- Re-run a workspace search for duplicate/wrong paths.
- Ensure cross-file references remain consistent.

---

## 6) Definition of “Enterprise-grade” (for this repo)
Code is enterprise-grade if it:
- Validates inputs and names
- Handles failure paths
- Preserves backward compatibility unless instructed
- Minimizes unrelated changes
- Uses existing engines/patterns before inventing new ones

---

## 7) Quick Review Checklist
- [ ] Naming matches AliensStyle
- [ ] Path roots are SSOT
- [ ] Validation present
- [ ] DB uses Row engine
- [ ] Update never creates
- [ ] Error paths are explicit
- [ ] No new folder names invented

---

# END
