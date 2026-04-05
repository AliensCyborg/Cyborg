# Brand.Structure — Software Engineer Role (SSOT)

This is the **top-level brand structure entrypoint** for the Software Engineer role.

Detailed reference:
- `Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.Structure.md`

---

## 1) Canonical Brand Root
- `/Aliens/WebBrand/`

Common pattern:
- `/Aliens/WebBrand/{Brand}/PHP.{Version}/Theme/{Brand}_Basic/...`

---

## 2) What Brand Controls
Brand is the UI override layer:
- layout shell (header/footer/nav)
- components mapping
- CSS system
- shared assets

Brand exists to prevent UI duplication across apps.

---

## 3) Override Rules (Priority)
General rule:
- If brand has a file with the same mapping/name, it overrides the app-level file.

Do not change override order unless explicitly instructed.

---

## 4) Cross-References
- Detailed brand docs: `Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.Structure.md`
- Brand layout rules: `Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.LayoutRules.md`
- Brand override system: `Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.OverrideSystem.md`

---

# END
