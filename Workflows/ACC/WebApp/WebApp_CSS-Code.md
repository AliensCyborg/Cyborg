---
WorkflowId: WebApp_CSS-Code
Type: Workflow_Singular-Code
Domain: WebApp
ParentWorkflowId: WebApp_CSS
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/ACC.Safety.md
NonNegotiables:
  - "No deletes"
  - "App-level CSS should be reusable + minimal surprises"
  - "No custom JS assumptions"
...

# [0] Purpose
`User/css/0.css` generate karna (global design system).

Yeh file app-level:
- theme overrides
- behavior CSS
- layout + utilities
- popup/flyout/overlay baseline

---

# [1] Output Path (Deterministic)
- `{AppRoot}/User/css/0.css`

If AppRoot missing => `WF_APPROOT_NOT_FOUND`.

---

# [2] File Structure (Mandatory Sections)
Generated CSS MUST include these sections (in order):

1) `/* App Base */`
- minimal global tweaks safe for all screens

2) `/* Theme Tokens */`
- `:root { ... }` overrides + alias tokens

3) `/* Behavior */`
- `.btn1` hover/active
- focus outlines (accessible)

4) `/* Overlays */`
- `popups` baseline
- `Flyouts` baseline

5) `/* Layout Utilities */`
- containers + grid helpers
- spacing + radius helpers

6) `/* Screen Layout */`
- `screen` wrapper conventions + responsive helper

Hard rule:
- Keep selectors generic (no specific component names).

---

# [3] Variable Safety
- Do not remove keys from base variable system.
- Add new app-level tokens with `--app-*` prefix.

---

# [4] Responsive Rules
- Provide safe defaults for both:
  - desktop (`min-width: 999px`)
  - mobile (`max-width: 999px`)
