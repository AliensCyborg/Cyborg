# DebuggingFramework — Software Engineer Role (SSOT)

This file expands:
- `Roles/Role.SoftwareEngineer/Rules.md` → Section 4 (Debugging Rules)

Goal: Provide a **repeatable, root-cause-first debugging system** for WebOS/WebApp/WebBrand/WebSDK/DB work.

---

## 1) Debugging Principles
- **Root-cause only** (no band-aids)
- **No blind changes** (read context first)
- **Smallest correct fix** (surgical edits)
- **Verify with evidence** (searches, reproducible steps)

---

## 2) The 7-Step Debugging Ladder

### Step 1 — Reproduce
- Identify exact input/state that triggers the issue.
- Confirm whether it is create vs update flow.

### Step 2 — Locate the failing boundary
Examples:
- Path resolution (wrong root like `/Aliens/WebApps/`)
- Case sensitivity mismatches (e.g. `Unit/` vs `unit/`)
- Missing file vs wrong file

### Step 3 — Inspect SSOT
- Check relevant SSOT docs:
  - `Workflows/ACC/_Refs/ACC.Paths.md`
  - `Workflows/ACC/_Refs/ACC.Safety.md`
  - Role ecosystem docs

### Step 4 — Trace the flow
- For PHP: add `$echo` trace points at decisions.
- For DB: trace Row-engine parameters (table, tenant, ecosystem, status).

### Step 5 — Form a single hypothesis
- One hypothesis at a time.
- Prefer the simplest hypothesis that explains all symptoms.

### Step 6 — Apply the minimal correct fix
- Fix the source of truth (SSOT paths, canonical structure).
- Avoid rewriting unrelated sections.

### Step 7 — Lock the fix
- Add guardrails:
  - update shared references if required
  - run searches to ensure no other references remain

---

## 3) Debugging Categories & Playbooks

### 3.1 Workflow failures
Common causes:
- Wrong canonical root (`WebApp` vs `WebApps`)
- Update workflow creating new files
- Writes outside allowed roots

Actions:
- Search for the wrong root string.
- Patch SSOT refs first, then child workflows.

### 3.2 DB issues
Common causes:
- Missing tenant/ecosystem filter
- Raw SQL bypassing Row engine
- Missing index

Actions:
- Verify Row usage.
- Validate required columns and indexes.

### 3.3 UI regressions
Common causes:
- missing empty/loading/error states
- breaking layout overrides

Actions:
- Check Brand override rules.
- Confirm component naming and file mapping.

---

## 4) What “Done” Means
A bug is fixed only when:
- The root-cause is addressed
- The system no longer allows the same class of failure
- Verification search/check passes

---

# END
