---
RefId: ACC.UI.Premium
Type: ACC.Reference
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
Scope:
  - WebApps Theme UI (component/css/template/layout)
  - WebBrand Theme UI (brand overrides)
  - WebSDK UI surfaces (future: module component/template)
UsedBy:
  - Workflows that generate UI or UI-adjacent code
  - WebApp_Component, WebApp_Template, WebBrand_Component, WebBrand_Template (and updates)
Goal: >
  Define "Premium UI" in measurable, enforceable rules so ACC workflows can generate consistent,
  modern, enterprise-grade UI without vague interpretations.
NonNegotiables:
  - NoHindiScript: true
  - DefaultTheme: "_Basic"
  - NoDelete: true
---

# ACC.UI.Premium.md
**Meaning (Hinglish):**  
"Premium UI" ka matlab sirf "pretty" nahi. Premium UI = **modern + enterprise-grade + consistent + fast + accessible + scalable**.  
Is doc ka purpose: Jab description me "premium" / "premium UI" aaye, workflows ke paas ek **clear checklist** ho jisse output objectively evaluate ho sake.

---

## 1) When to apply Premium UI rules
Apply this ref if ANY of these are true:
- Description contains: `premium`, `premium ui`, `enterprise ui`, `modern ui`, `top level ui`
- Workflow type is UI-facing by nature:
  - WebApp_Component / WebApp_Template
  - WebBrand_Component / WebBrand_Template
  - Any future: Module Component/Template workflow

If description does not mention premium:
- Workflow may still follow *baseline modern rules*, but strict premium gates are optional.

---

## 2) Premium UI Acceptance Criteria (must-pass)
A UI output is "Premium" only if it passes ALL gates below.

### Gate A: Consistency (Design System)
- Same spacing scale everywhere (e.g., 4/8/12/16/24/32)
- Same border radius scale (e.g., 8/12/16)
- Same typography scale (e.g., 12/14/16/18/24/32)
- Same icon style family (outline vs filled — choose one per theme)
- Same shadows and elevation language across cards/modals/buttons

**Fail examples:**
- random paddings
- multiple border radius values without system
- multiple button styles with no rules

### Gate B: Responsiveness (Mobile-first)
- Works at: 360px, 768px, 1024px, 1440px
- No horizontal scroll
- No text overlap
- Touch targets >= 44px
- Critical actions visible above-the-fold on mobile

### Gate C: Accessibility (Enterprise-grade)
- Keyboard navigation works (tab order logical)
- Focus visible (not removed)
- Color contrast reasonable (avoid low-contrast grey-on-grey)
- Forms have labels and validation feedback
- Error/success states not only color-based (also text/icon)

### Gate D: Performance & UX
- UI should not require heavy JS for basic layout
- Avoid layout shift (stable height placeholders for async data)
- Use lightweight markup, avoid deep nesting without reason
- If animations: subtle, short, purposeful; never block actions

### Gate E: Product UX polish
- Empty states defined (no blank screens)
- Loading states defined (skeleton/loader)
- Error states defined (actionable message + retry)
- Success states defined (confirmation + next action hint)
- Microcopy: clear, short, user-intent based

---

## 3) Premium UI Checklist (Workflow should enforce)
Workflows should generate a `UI_Checklist` section in planning/docs and mark items as:
- MUST / SHOULD / OPTIONAL

### Layout & Structure
- MUST: Clear page hierarchy (header/content/footer or equivalent)
- MUST: Grid-based layout (consistent columns)
- SHOULD: Use cards/sections with headings
- SHOULD: Avoid excessive borders; prefer spacing + subtle background

### Typography
- MUST: Single font family per theme
- MUST: Defined heading sizes (H1/H2/H3) and body sizes
- SHOULD: Line-height comfortable (not cramped)
- OPTIONAL: Numerals monospaced for finance-like data

### Colors
- MUST: Limited palette (primary, secondary, surface, text, danger, success, warning)
- MUST: States for buttons/inputs (hover/active/disabled)
- SHOULD: Use subtle background surfaces (surface-1, surface-2)

### Components
- MUST: Buttons (primary/secondary/ghost), Inputs, Select/Dropdown, Table/List, Card, Modal/Drawer
- MUST: Each component has states:
  - default, hover, active, disabled, loading
- SHOULD: Provide icon support
- OPTIONAL: Toast notifications

### Forms & Validation
- MUST: Inline validation messages
- MUST: Required field indicators
- SHOULD: Input masks where applicable (phone, date)

### Data Views (tables, lists, dashboards)
- MUST: Sorting/Filtering UX defined where needed
- SHOULD: Sticky header for big tables
- OPTIONAL: Column resize / export (future)

---

## 4) “Premium UI” Scoring Rubric (0–10)
Workflows can self-evaluate output with a score. Target: **9+/10**.

- 0–2: raw layout, inconsistent, unusable on mobile
- 3–5: usable but no system, lacks states
- 6–7: good UI, some polish missing (empty/loading/error)
- 8: strong UI + consistent system + responsive + states
- 9: enterprise polish + accessibility + microcopy + stable performance
- 10: benchmark UI: crisp, fast, accessible, scalable, minimal bugs

**Strict rule:** If any gate in Section 2 fails, score cannot exceed 7.

---

## 5) Integration Rules with Aliens Ecosystem (WebApp/WebBrand/WebSDK)
### WebApp Theme file mapping
- Component:
  - `/Aliens/WebApp/{App}/26.00.00/theme/{App}_Basic/User/component/{Component}.php`
  - `/Aliens/WebApp/{App}/26.00.00/theme/{App}_Basic/User/css/{Component}.css`
- Template:
  - `/Aliens/WebApp/{App}/26.00.00/theme/{App}_Basic/User/template/{Template}/...sections...`
- Layout:
  - `/Aliens/WebApp/{App}/26.00.00/theme/{App}_Basic/User/layout/...`

### WebBrand override rules (priority)
Brand same structure as App, but may override app files if same name exists:
- `/Aliens/WebBrand/{Brand}/PHP.26.00.00/Theme/{Brand}_Basic/...`
Layout file naming:
- `layout/{AppName}.Nav.php` (App name prefix mandatory)

### WebSDK future UI surfaces
For future workflows (Module UI):
- `/Aliens/WebSDK/PHP.26.00.00/Module/{Module}/component/...`
- `/Aliens/WebSDK/PHP.26.00.00/Module/{Module}/template/...`
- `/Aliens/WebSDK/PHP.26.00.00/Module/{Module}/css/...`

---

## 6) Output Requirements for Premium UI (for Planning + Docs)
When premium is requested, the workflow must produce:
1) **UI Spec Summary** (what user gets)
2) **Component states list** (default/hover/disabled/loading/error)
3) **Responsive notes**
4) **Accessibility notes**
5) **Performance notes**
6) **Checklist (marked)** + Score (0–10)

Where to save:
- Planning: `/Aliens/.Alien/{Developer}/Planning/...`
- Docs: `/Aliens/Docs/...` (domain-based folder; workflow decides)

---

## 7) Rules for “Creativity first, then Human check”
Premium UI generation can have 2-phase behavior:
1) AI generates a modern premium proposal (layout + style)
2) Human review:
   - Approves it as premium
   - Or requests changes (colors, spacing, sections, brand tone)

**Workflow must include a “Review Notes” placeholder** in docs so human feedback can be appended later.

---

## 8) Common Anti-Patterns (must avoid)
- Random CSS class names without a system
- Inline styles everywhere (unless ecosystem mandates it)
- Hard-coded pixel values everywhere without scale
- Missing empty/loading/error states
- Poor contrast
- Click targets too small
- Huge DOM nesting
- Over-animated UI

---

## 9) Premium UI Keywords (helpful for workflows)
If Description includes these, treat as high priority:
- “premium”
- “enterprise”
- “modern”
- “minimal”
- “dashboard style”
- “finance grade”
- “binance like” (means: information-dense but clean, strong hierarchy)

---

## 10) Changelog
- 26.00.00 (2025-12-24): Initial premium UI measurable standard for ACC workflows
