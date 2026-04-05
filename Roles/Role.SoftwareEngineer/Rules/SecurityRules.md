# SecurityRules — Software Engineer Role (SSOT)

This file expands:
- `Roles/Role.SoftwareEngineer/Rules.md` → Section 6 (Security Rules)

Goal: Enforce **ecosystem/tenant isolation**, safe IO, safe DB, safe UI.

---

## 1) Security Baseline
- Default stance: **deny by default**.
- Never trust client input.
- Never bypass Kernel protections.

---

## 2) Identity & Access Control
- Always validate identity (`i`) for protected operations.
- Enforce:
  - ecosystem scope
  - tenant scope
  - role/permission scope

---

## 3) Database Security
- Row engine is the default gateway.
- No raw SQL unless explicitly required.
- Never interpolate user input into SQL.
- Enforce `status != 9` for reads unless explicitly requested.

---

## 4) UI Security
- Escape output (XSS prevention).
- Do not render raw HTML from untrusted input.
- Prefer server-side rendering rules consistent with existing patterns.

---

## 5) File/Path Security
- Only write within allowed roots.
- No path traversal (`../`).
- Canonical roots must be used (example: `/Aliens/WebApp/`).

---

## 6) Update Workflow Safety
- `_Update` workflows must:
  - verify target exists
  - refuse to “invent” a new file

---

## 7) Secrets & Logging
- Never log secrets.
- Never print credentials.
- Keep debug output structured and removable.

---

# END
