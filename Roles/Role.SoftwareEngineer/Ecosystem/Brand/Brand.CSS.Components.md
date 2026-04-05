# Brand.CSS.Components — Detailed Version

Location:  
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.CSS.Components.md`

Is document ka purpose hai:

- Aliens WebOS ecosystem me **Brand-level CSS components** ka standard define karna  
- Taaki har Brand ke andar:
  - Buttons
  - Forms
  - Tables
  - Cards
  - Modals
  - Alerts
  - Badges / Tags
  - Chips / Pills
  - Tabs
  - Toasts / Notifications  
  sab ek hi grammar follow karein  
- AlienCyborg (SoftwareEngineer role) + human devs dono ke liye **clear CSS system** ready ho

Yeh document functional + conceptual dono level par likha gaya hai, taaki AI ko bhi pattern samajh aaye.

---

## 1. CSS Component System Goals

1. **Consistency Across Apps + Brands**  
   Core components ka behaviour same rahe, chahe Brand change ho jaye.
2. **Brand Theming via Tokens, Not Copy-Paste**  
   Colours, radius, shadows tokens ke through set hon.
3. **Reusability**  
   Same button / card / table style 1000+ apps me reuse ho sake.
4. **AliensStyle Compliant**  
   Naming, structure, file split sab AliensStyle ke rules follow karein.
5. **AI-Friendly System**  
   CSS structure predictable ho, taaki AI confidently edit / extend kar sake.

---

## 2. Folder Structure for CSS Components

Recommended Brand CSS structure (conceptual):

```text
Brand/{BrandCode}/Assets/css/
 ├── base.css
 ├── tokens.css
 ├── layout.css
 ├── components/
 │    ├── button.css
 │    ├── form.css
 │    ├── table.css
 │    ├── card.css
 │    ├── modal.css
 │    ├── alert.css
 │    ├── badge.css
 │    ├── chip.css
 │    ├── tabs.css
 │    ├── toast.css
 │    └── utilities.css
 └── brand.custom.css   (optional overrides)
```

- `tokens.css` = design tokens (colors, radius, spacing, etc.)
- `base.css` = resets + typography
- `layout.css` = overall layout (header, sidebar, content regions)
- `components/*.css` = ek component / family per file
- `brand.custom.css` = special cases only (minimal use)

---

## 3. Design Tokens (tokens.css)

Tokens har component ka backbone hain.

### 3.1 Color Tokens

Example variables:

```css
:root {
  --brand-color-primary: #0066ff;
  --brand-color-primary-hover: #0052cc;
  --brand-color-secondary: #f97316;

  --brand-color-bg: #0b1120;
  --brand-color-bg-alt: #020617;

  --brand-color-border: #1e293b;
  --brand-color-border-soft: #111827;

  --brand-color-text-main: #e5e7eb;
  --brand-color-text-muted: #9ca3af;

  --brand-color-success: #22c55e;
  --brand-color-danger: #ef4444;
  --brand-color-warning: #f59e0b;
  --brand-color-info: #0ea5e9;
}
```

### 3.2 Radius Tokens

```css
:root {
  --brand-radius-xs: 2px;
  --brand-radius-sm: 4px;
  --brand-radius-md: 8px;
  --brand-radius-lg: 12px;
  --brand-radius-xl: 16px;
}
```

### 3.3 Spacing Tokens

```css
:root {
  --brand-space-xs: 4px;
  --brand-space-sm: 8px;
  --brand-space-md: 12px;
  --brand-space-lg: 16px;
  --brand-space-xl: 24px;
  --brand-space-2xl: 32px;
}
```

### 3.4 Shadow Tokens

```css
:root {
  --brand-shadow-soft: 0 4px 12px rgba(15, 23, 42, 0.45);
  --brand-shadow-strong: 0 10px 30px rgba(15, 23, 42, 0.8);
}
```

Har component inhi tokens ka use karega — direct hard-coded values se avoid karein.

---

## 4. Buttons (button.css)

### 4.1 Base Button Class

```css
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: var(--brand-space-sm) var(--brand-space-lg);
  border-radius: var(--brand-radius-md);
  border: 1px solid transparent;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.15s ease, border-color 0.15s ease, transform 0.1s ease, box-shadow 0.15s ease;
  gap: var(--brand-space-xs);
}
```

### 4.2 Button Variants

```css
.btn-primary {
  background-color: var(--brand-color-primary);
  color: #ffffff;
  box-shadow: var(--brand-shadow-soft);
}
.btn-primary:hover {
  background-color: var(--brand-color-primary-hover);
}

.btn-secondary {
  background-color: transparent;
  border-color: var(--brand-color-border);
  color: var(--brand-color-text-main);
}

.btn-danger {
  background-color: var(--brand-color-danger);
  color: #ffffff;
}
```

### 4.3 Size Variants

```css
.btn-sm {
  padding: var(--brand-space-xs) var(--brand-space-md);
  font-size: 12px;
}

.btn-lg {
  padding: var(--brand-space-md) var(--brand-space-xl);
  font-size: 15px;
}
```

### 4.4 State Classes

```css
.btn:disabled,
.btn.disabled {
  opacity: 0.6;
  cursor: not-allowed;
  box-shadow: none;
}
```

**Rule:** Button component hamesha `.btn` ke base se extend hoga, direct custom style se nahi.

---

## 5. Forms (form.css)

### 5.1 Input Basics

```css
.form-control {
  width: 100%;
  padding: var(--brand-space-sm) var(--brand-space-md);
  border-radius: var(--brand-radius-md);
  border: 1px solid var(--brand-color-border);
  background-color: var(--brand-color-bg-alt);
  color: var(--brand-color-text-main);
  font-size: 14px;
}
.form-control:focus {
  outline: none;
  border-color: var(--brand-color-primary);
  box-shadow: 0 0 0 1px var(--brand-color-primary);
}
```

### 5.2 Label & Help Text

```css
.form-label {
  display: block;
  margin-bottom: var(--brand-space-xs);
  font-size: 13px;
  color: var(--brand-color-text-main);
}

.form-help {
  margin-top: 2px;
  font-size: 12px;
  color: var(--brand-color-text-muted);
}
```

### 5.3 Error State

```css
.form-control.is-error {
  border-color: var(--brand-color-danger);
}
.form-error {
  margin-top: 2px;
  font-size: 12px;
  color: var(--brand-color-danger);
}
```

### 5.4 Form Group

```css
.form-group {
  margin-bottom: var(--brand-space-md);
}
```

---

## 6. Tables (table.css)

### 6.1 Table Wrapper

```css
.table-wrapper {
  width: 100%;
  overflow-x: auto;
  background-color: var(--brand-color-bg-alt);
  border-radius: var(--brand-radius-lg);
  border: 1px solid var(--brand-color-border);
}
```

### 6.2 Table Element

```css
.table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}

.table thead {
  background-color: rgba(15, 23, 42, 0.9);
}

.table th,
.table td {
  padding: var(--brand-space-sm) var(--brand-space-md);
  border-bottom: 1px solid var(--brand-color-border-soft);
  text-align: left;
}

.table th {
  font-weight: 600;
  color: var(--brand-color-text-main);
}

.table tbody tr:hover {
  background-color: rgba(30, 64, 175, 0.18);
}
```

### 6.3 Action Column

```css
.table-actions {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: var(--brand-space-xs);
}
```

---

## 7. Cards (card.css)

### 7.1 Card Base

```css
.card {
  background-color: var(--brand-color-bg-alt);
  border-radius: var(--brand-radius-lg);
  border: 1px solid var(--brand-color-border);
  padding: var(--brand-space-lg);
  box-shadow: var(--brand-shadow-soft);
}

.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: var(--brand-space-md);
}

.card-title {
  font-size: 15px;
  font-weight: 600;
  color: var(--brand-color-text-main);
}
```

### 7.2 Card Variants

```css
.card-muted {
  background-color: #020617;
  border-style: dashed;
}

.card-highlight {
  border-color: var(--brand-color-primary);
}
```

---

## 8. Modals (modal.css)

### 8.1 Backdrop + Dialog

```css
.modal-backdrop {
  position: fixed;
  inset: 0;
  background-color: rgba(15, 23, 42, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
}

.modal-dialog {
  width: 100%;
  max-width: 520px;
  background-color: var(--brand-color-bg-alt);
  border-radius: var(--brand-radius-xl);
  box-shadow: var(--brand-shadow-strong);
  border: 1px solid var(--brand-color-border);
  padding: var(--brand-space-lg);
}
```

### 8.2 Header / Body / Footer

```css
.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: var(--brand-space-md);
}

.modal-title {
  font-size: 16px;
  font-weight: 600;
}

.modal-body {
  margin-bottom: var(--brand-space-md);
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: var(--brand-space-sm);
}
```

---

## 9. Alerts (alert.css)

```css
.alert {
  border-radius: var(--brand-radius-md);
  padding: var(--brand-space-sm) var(--brand-space-md);
  display: flex;
  align-items: flex-start;
  gap: var(--brand-space-sm);
  font-size: 13px;
}

.alert-icon {
  margin-top: 2px;
}

.alert-success {
  background-color: rgba(34, 197, 94, 0.12);
  border: 1px solid rgba(34, 197, 94, 0.4);
  color: #bbf7d0;
}

.alert-danger {
  background-color: rgba(248, 113, 113, 0.12);
  border: 1px solid rgba(248, 113, 113, 0.4);
  color: #fecaca;
}

.alert-warning {
  background-color: rgba(253, 224, 71, 0.08);
  border: 1px solid rgba(253, 224, 71, 0.4);
}
```

---

## 10. Badges / Tags (badge.css)

```css
.badge {
  display: inline-flex;
  align-items: center;
  padding: 2px 8px;
  border-radius: 999px;
  font-size: 11px;
  border: 1px solid transparent;
}

.badge-success {
  background-color: rgba(34, 197, 94, 0.12);
  border-color: rgba(34, 197, 94, 0.5);
  color: #bbf7d0;
}

.badge-danger {
  background-color: rgba(248, 113, 113, 0.12);
  border-color: rgba(248, 113, 113, 0.5);
}

.badge-muted {
  background-color: rgba(148, 163, 184, 0.12);
  border-color: rgba(148, 163, 184, 0.5);
}
```

---

## 11. Tabs (tabs.css)

```css
.tabs {
  display: flex;
  align-items: center;
  gap: var(--brand-space-sm);
  border-bottom: 1px solid var(--brand-color-border);
}

.tab {
  padding: var(--brand-space-sm) var(--brand-space-md);
  font-size: 13px;
  cursor: pointer;
  color: var(--brand-color-text-muted);
  border-bottom: 2px solid transparent;
}

.tab-active {
  color: var(--brand-color-text-main);
  border-bottom-color: var(--brand-color-primary);
}
```

---

## 12. Toasts / Notifications (toast.css)

```css
.toast-container {
  position: fixed;
  right: var(--brand-space-lg);
  bottom: var(--brand-space-lg);
  display: flex;
  flex-direction: column;
  gap: var(--brand-space-sm);
  z-index: 60;
}

.toast {
  min-width: 240px;
  max-width: 360px;
  background-color: var(--brand-color-bg-alt);
  border-radius: var(--brand-radius-lg);
  padding: var(--brand-space-sm) var(--brand-space-md);
  box-shadow: var(--brand-shadow-soft);
  border: 1px solid var(--brand-color-border);
  font-size: 13px;
}
```

---

## 13. Utilities (utilities.css)

Common utility classes jo layouts + components ko fast design karne me help karein, without breaking AliensStyle:

```css
.u-flex {
  display: flex;
}
.u-flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}
.u-flex-between {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.u-text-muted {
  color: var(--brand-color-text-muted);
}

.u-mt-sm { margin-top: var(--brand-space-sm); }
.u-mt-md { margin-top: var(--brand-space-md); }
.u-mb-sm { margin-bottom: var(--brand-space-sm); }
.u-mb-md { margin-bottom: var(--brand-space-md); }
```

**Rule:** Utilities limited hone chahiye, tailwind-style 1000 utilities avoid karein. Sirf woh utilities jo har app me help karein.

---

## 14. AI Usage Guidelines (For AlienCyborg)

Jab AlienCyborg CSS generate kare:

- Always:
  - Tokens ka use kare (colors, radius, spacing)
  - Base component classes (`.btn`, `.card`, `.form-control`, etc.) se extend kare
  - Naming clean, semantic, AliensStyle-friendly rakhe
- Never:
  - Random inline styles spam kare
  - Hard-coded colours everywhere use kare
  - Component ke base contract ko break kare

Goal: **Fast + readable + reusable + brand-consistent CSS**.

---

## 15. Summary

`Brand.CSS.Components`:

- Brand ke CSS components ka **master rulebook** hai  
- Ensure karta hai ki sab Apps + Brands:
  - Same CSS backbone share karein  
  - Easy theming possible ho  
  - AI + human milke быстро & safely UI bana saken  

Is file ko future me expand karke: charts, skeletons, steppers, timelines, etc. bhi add kiye ja sakte hain, but base grammar same rehni chahiye.
