# Brand Structure — Detailed Documentation (Role: Software Engineer)

## 1. Overview
This document defines the **Brand System Architecture** inside the Aliens WebOS ecosystem.  
Brand determines the **visual identity**, **layout rules**, **theme logic**, **component mapping**, and **brand-specific assets** for each App running inside WebOS.

The Brand layer allows:
- Multi-brand support on the same WebOS instance  
- Shared apps with different design systems  
- Centralized UI control  
- Zero duplication of UI files across apps  
- Rapid rebranding for multiple clients  

---

## 2. Purpose of the Brand System
The Brand System exists to:
1. **Separate UI identity from app logic**  
2. **Allow multiple apps to use the same brand**  
3. **Allow the same app to support multiple brands**  
4. **Reduce UI duplication**  
5. **Apply company-wide design standards**  
6. **Enable ecosystem-level UI control**

---

## 3. Brand Folder Structure (Inside WebOS Ecosystem)

```
Brand/
 ├── {BrandCode}/
 │    ├── App/
 │    │    ├── Home/
 │    │    │    ├── index.php
 │    │    │    └── style.css
 │    │    ├── Dashboard/
 │    │    ├── Settings/
 │    │    └── …
 │    ├── Assets/
 │    │    ├── css/
 │    │    ├── js/
 │    │    ├── images/
 │    │    └── fonts/
 │    ├── Components/
 │    │    ├── Button.php
 │    │    ├── Card.php
 │    │    ├── Modal.php
 │    │    └── …
 │    ├── Layout/
 │    │    ├── header.php
 │    │    ├── footer.php
 │    │    ├── sidebar.php
 │    │    └── topbar.php
 │    ├── Theme.json
 │    └── Brand.json
 └── README.md
```

---

## 4. Mandatory Brand Files

### **4.1 Brand.json**
Defines brand identity:
- Name  
- Code  
- Primary colors  
- Typography  
- UI components mapping  

Example:
```json
{
  "name": "Aeldo",
  "code": "aeldo",
  "primary_color": "#0066ff",
  "font": "Inter",
  "components": {
    "button": "Button.php",
    "card": "Card.php",
    "modal": "Modal.php"
  }
}
```

---

### **4.2 Theme.json**
Defines brand theme rules:
- Light/Dark theme  
- Spacing scaling  
- Border radius pattern  
- Elevation levels  

---

## 5. Brand → App Mapping
Each App chooses a brand via:

### **Database → ecosystem_brand table**
or  
### **manifest.json → brand field**

Flow:
1. WebOS resolves `ecosystem`
2. Ecosystem resolves `brand`
3. Brand resolves:
   - Layout  
   - Components  
   - UI rules  

---

## 6. Brand-Level Components

### **Reusable Components Example**
- Button  
- Card  
- FormGroup  
- Table  
- Pagination  
- Alert  

These components are used across thousands of screens, so having brand-specific versions enables global design consistency.

---

## 7. Layout System
Brand defines the **master layout**:

- header.php  
- footer.php  
- sidebar.php  
- topbar.php  
- main container wrapper  

Apps only render **content**, Brand controls the shell.

---

## 8. Asset Management

```
Brand/{Brand}/Assets/css/
Brand/{Brand}/Assets/js/
Brand/{Brand}/Assets/images/
Brand/{Brand}/Assets/fonts/
```

WebOS automatically injects these assets via `Theme.php`.

---

## 9. How Apps Use Brand

### Example Code:
```php
Layout(header);
Component(Button, ["label" => "Submit"]);
Layout(footer);
```

WebOS automatically maps:
```
Brand/{BrandCode}/Components/Button.php
```

---

## 10. Brand Versioning
Every brand can have:
```
Brand/{Brand}/Version/{VersionCode}/...
```

Allowing:
- Seasonal themes  
- Special event branding  
- A/B testing UI  

---

## 11. AI Rules for Editing Brand Files
1. Never modify Brand.json unless instructed  
2. Never remove Layout files  
3. Only create new components inside Components/  
4. Maintain compatibility with:
   - WebOS Theme Engine  
   - WebOS App Layer  
   - AliensStyle guidelines  

---

## 12. Conclusion
The Brand System ensures:
- Scalability  
- Reusability  
- Zero UI duplication  
- Multi-brand support  
- Enterprise-level UI consistency  

It is one of the core strengths of **Aliens WebOS** and must be maintained carefully.
