# Apps.UI.Rules — Detailed Version

This document defines the **standard UI rules** for all Apps running inside the Aliens WebOS ecosystem, specifically for the  
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Apps` layer.

Iska main purpose hai ki:

- Har App ka UI **predictable, consistent, fast aur AliensStyle-compliant** ho.  
- Brand, Theme, App, Module, Screen, Component — sab ek hi language bolen.  
- Human developers + AlienCyborg (SoftwareEngineer role) + other AI tools, sab ek hi **UI rulebook** follow karein.

---

## 1. Core Philosophy of Aliens UI

### 1.1 Goals

1. **Clarity First, Fancy Later**  
   UI hamesha crystal clear ho, user ko sochna na pade ki “kya karna hai”.

2. **Speed of Work, Not Just Beauty**  
   Design ka main goal hai **workflow speed**. Har screen ka measure hai:  
   - “Ek normal user kitni fast kaam complete kar sakta hai?”

3. **Consistency Across All Apps**  
   Chaahe Coinpods Exchange ho, Aeldo Shop, HX Wallet ya koi ERP app:  
   - Layout pattern, form style, table style, buttons, spacing — sab ka grammar same hona chahiye.

4. **Brand + WebOS Balance**  
   - Brand: colors, logo, visual identity  
   - WebOS: structure, layout, UX patterns  
   UI me dono ka **balance** maintain rehna chahiye.

5. **AI-Friendly Structure**  
   - Code + HTML + CSS aise structure me likhe jaayen ki AI easily:  
     - Read kar sake  
     - Modify kar sake  
     - Extend kar sake  
     without breaking layout.

---

## 2. UI Responsibility Split: WebOS vs Brand vs App

### 2.1 WebOS Responsibility

WebOS define karta hai:

- Overall **layout layers**:
  - Header, Sidebar, Content, Footer
- Base **grid system**
- Base **form standards**
- Base **table standards**
- Base **spacing, typography scale, breakpoints**

### 2.2 Brand Responsibility

Brand define karta hai:

- Colors (Primary, Secondary, Accent, Background, Border)
- Typography family (fonts)
- Logo, favicon, icon pack
- Button styling variations
- Theme-specific components (branding blocks, banners, cards)

### 2.3 App Responsibility

App define karta hai:

- Screens:
  - Dashboard
  - Transactions page
  - KYC Screens
  - User Management, etc.
- Screen-level layout combinations
- Module-level forms and tables

**Important Rule**:  
App should **not** override WebOS base rules aggressively.  
App should **extend** and **compose**, not re-invent UI core.

---

## 3. Layout Rules for All Apps

### 3.1 Standard Page Layout

Every full-screen app view should follow this abstract structure:

```text
+------------------------------------------+
| Header (Brand + User + Navigation)       |
+-------------------+----------------------+
| Sidebar / Menu    |  Content Area        |
| (if applicable)   |  - Page Title        |
|                   |  - Breadcrumb        |
|                   |  - Filters / Actions |
|                   |  - Main Content      |
+-------------------+----------------------+
| Footer (optional)                         |
+------------------------------------------+
```

### 3.2 Header Rules

Header must:

- Show:
  - Brand logo / app logo
  - App name (short)
  - User info (name/avatar)
  - Quick actions (notifications, settings, logout)
- Not be overloaded with too many buttons.
- Height should be consistent across all apps.

### 3.3 Sidebar Rules

Sidebar (agar use ho raha hai) should:

- Contain primary navigation only.  
- Use **menu.json** se driven structure:  
  - Icon + Label + Route
- Show active state clearly:
  - Highlight background or left border
- Collapsible mode allow karna (desktop vs mobile).

### 3.4 Content Area Rules

Content area ke basic building blocks:

1. **Page Title Row**
   - Title (H1/H2)
   - Short description (optional)
   - Right side actions (buttons, quick filters)

2. **Filter/Action Bar**
   - Search bar
   - Date range selector
   - Dropdown filters
   - Primary action (e.g. “Add New”, “Export”)

3. **Main Content**
   - Either:
     - Table view
     - Form view
     - Dashboard cards
     - Mixed layout

4. **Footer (Optional)**
   - Pagination
   - Status info (e.g. “Showing 1-20 of 350 results”)

---

## 4. Forms — Rules & Patterns

### 4.1 General Form Rules

- Labels always visible (no placeholder-only forms).  
- Fields aligned in **logical groups**.  
- Max 2–3 columns per row (desktop).  
- On mobile: collapse to single column.

### 4.2 Field Types

- Text input: For short strings
- Textarea: For long descriptions
- Select dropdown: For fixed options
- Date / Time pickers
- Toggle/switch: For boolean fields
- Radio / checkbox: For multi-select or yes/no

**Rule**:  
Har field ka **label + help text + validation error** clearly dikhna chahiye.

### 4.3 Validation UX

- On error:
  - Field border highlight
  - Error text under field
- On success:
  - Optional subtle success indication
- No alert popups for normal validation errors, unless very critical.

### 4.4 Form Actions

- Primary buttons:
  - “Save”, “Create”, “Update”, “Submit”  
- Secondary buttons:
  - “Cancel”, “Reset”, “Back”  

Buttons ki position:

- Right aligned (desktop)
- Full width (mobile)

---

## 5. Table & List View Rules

### 5.1 Table Basics

Every table should follow:

- Clear header row:
  - Bold labels, consistent casing
- Row hover effect (subtle)
- Column alignment:
  - Numbers right-aligned
  - Text left-aligned
  - Dates center/left

### 5.2 Action Columns

Last column mostly reserved for actions:

- Icons or buttons:
  - View
  - Edit
  - Delete
  - More (…) menu

**Rule**:  
Agar 2–3 se zyada actions hai, use a dropdown / "..." menu.

### 5.3 Pagination

- Always show:
  - Current page
  - Total pages or “Showing X–Y of Z”
- Page size control optional but recommended.

### 5.4 Empty State

Jab table me data nahi ho:

- Show friendly message:
  - “No records found”
- Provide CTA:
  - “Add First Record” (if applicable)

---

## 6. Dashboard & Cards

### 6.1 Dashboard Layout

- Use cards for:

  - KPIs (Total Users, Balance, Revenue, etc.)
  - Quick actions
  - Status sections

- Grid-based layout:
  - 2–4 cards per row (desktop)
  - Single column (mobile)

### 6.2 Card Design Rules

Each card may contain:

- Title
- Primary number / metric
- Small trend indicator / secondary info
- Optional icon

Cards should be:

- Clean, minimal, not overloaded
- Uniform height (per row) where possible

---

## 7. Typography & Spacing Rules

### 7.1 Heading Hierarchy

- Page title: H1
- Section title: H2
- Subsection: H3

Avoid skipping hierarchy (H1 → H3 without H2).

### 7.2 Font Sizes (Example Scale)

- H1: 24–28px
- H2: 20–22px
- H3: 16–18px
- Body: 14–16px
- Caption: 12–13px

Exactly scale Brand + Theme define karega, but ratio consistent rehna chahiye.

### 7.3 Spacing Rules

- Section gap: 24–32px
- Card internal padding: 16–24px
- Form field vertical spacing: 12–16px
- Table row height: Comfortable, not too tight

**Rule**: “Breathing space” hamesha maintain karo. UI suffocated nahi lagna chahiye.

---

## 8. Color & State Rules

### 8.1 Semantic Colors

Standard semantic colors honge:

- Success: Green
- Error: Red
- Warning: Orange/Amber
- Info: Blue
- Neutral: Gray

Brand colors semantic color ko replace nahi karenge, they will **wrap around** them.

### 8.2 State Indicators

For status-like columns in tables:

- Badges:
  - `Active`, `Pending`, `Blocked`, `Deleted`
- Color-coded backgrounds
- Clear text label, not just icon

---

## 9. Responsive Rules

### 9.1 Breakpoints (Example)

- Mobile: < 768px
- Tablet: 768px–1024px
- Desktop: > 1024px

### 9.2 Behaviour

- Sidebar collapses on mobile
- Tables may:
  - Use horizontal scroll
  - Collapse into cards for critical UX flows

- Buttons:
  - Full width on mobile for primary actions

---

## 10. Componentization Rules

### 10.1 Reusable Components

Every App should reuse these core components:

- Button
- Input
- Select
- Table
- Card
- Modal
- Tabs
- Toast/Notification

### 10.2 File / Component Location

Preferred structure:

```text
Brand/{BrandCode}/Components/
App/{AppCode}/Components/
```

Brand = visual components  
App = business-flow-specific components

### 10.3 Naming

- Components should use clear names:
  - `UserForm`
  - `TransactionTable`
  - `WalletCard`
- No cryptic names like `Box1`, `Layout2`.

---

## 11. Error, Empty & Loading States

### 11.1 Loading State

- Use skeletons or spinners
- Avoid blocking full page with big spinner for long time

### 11.2 Error State

- Show:
  - Short message
  - Optional “Try Again”
- For serious errors:
  - Log internally via WebOS
  - Show generic safe message to user

### 11.3 Empty State

- Clear text
- Icon/illustration optional
- Helpful action hint

---

## 12. AliensStyle Integration

AliensStyle defines:

- Naming rules
- Layout conventions
- PHP + HTML mixing style
- Comment style (`$echo` logs for backend, clean semantic HTML for frontend)

UI rules must:

- Respect AliensStyle for:
  - File naming
  - Structure
  - Partial reuse
- Avoid inline, hard-coded experiments in production code.

---

## 13. Accessibility Basics (Minimum Level)

Even if full WCAG target nahi hai, minimum:

- Text contrast sufficient
- Interactive elements reachable via keyboard
- Focus states visible
- Use semantic tags where possible:
  - `<header>`, `<nav>`, `<main>`, `<footer>`, `<section>`, `<button>`, etc.

---

## 14. AI Usage Guidelines (For AlienCyborg)

Jab AlienCyborg UI generate kare:

- Always:
  - Follow layout structure
  - Use proper headings and spacing
  - Use consistent button and form patterns
  - Keep code readable and modular

- Never:
  - Mix too many visual experiments in one screen
  - Break layout rules just for “creative” looks
  - Create one-off weird patterns

AI ka main role:

- Speed up screen creation
- Maintain consistency
- Suggest UX improvements

---

## 15. Checklist for Any New Screen

Before screen “done” mark karo:

- [ ] WebOS layout follow ho raha hai  
- [ ] Brand theme apply ho raha hai  
- [ ] Heading hierarchy sahi hai  
- [ ] Spacing comfortable hai  
- [ ] Forms clean & validated UX ke saath hain  
- [ ] Tables readable + responsive hain  
- [ ] Actions clear: primary vs secondary  
- [ ] Empty, loading, error states handle kiye gaye hain  
- [ ] No inline hard-coded styles (where avoidable)  
- [ ] AliensStyle & WebOS rules follow ho rahe hain  

---

## 16. Conclusion

`Apps.UI.Rules` ensure karta hai ki:

- Har App ka UI **professional, fast, and consistent** ho  
- Team aur AI easily milke **1000+ Apps** build kar sake bina design chaos ke  
- Brand + WebOS + App responsibilities clearly separate rahein  

For AlienCyborg (SoftwareEngineer role), yeh document ek **visual grammar** hai — jiske base par koi bhi naya App, Screen, ya Module UI confidently design & implement kiya ja sakta hai.
