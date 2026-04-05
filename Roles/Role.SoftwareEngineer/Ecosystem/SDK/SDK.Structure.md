# SDK.Structure — Detailed Version

Location:  
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.Structure.md`

Is document ka main purpose hai:

- Aliens Ecosystem me **SDK folder** ka clear structure define karna  
- Batana ki SDK ka WebOS, Apps, Brand, Library ke saath relation kya hai  
- Batana ki ek Software Engineer (ya AlienCyborg) ko SDK ke andar **kaha kya milna chahiye**  
- Batana ki naye features / microservices / engines SDK me kaise add honge  
- AI + human developers dono ke liye **safe, predictable, scalable** pattern ready rakhna

Yeh file **SoftwareEngineer role** ke Ecosystem section ka part hai, isliye explanation WebOS + Aliens Ecosystem ke hisaab se hai.

---

## 1. SDK ka Role Aliens Ecosystem me

Aliens Ecosystem overall structure (conceptual):

- `/Aliens/WebOS` → OS (Boot, System, Kernel, Unit, OS, App, Event, Database)
- `/Aliens/SDK` → Reusable features, microservices, engines
- `/Aliens/Apps` → Har app ki UI + app-specific logic (Exchange, Wallet, Admin, ERP, etc.)
- `/Aliens/Brand` → Brand + Themes + Layouts + CSS system
- `/Aliens/Library` → Third-party ya external code

**SDK ka kaam:**

- Company ke saare **common features** ko central jagah par rakhna
- Backend (PHP) + Frontend (JS) dono side ke liye **ready-made building blocks** dena
- Aise microservices / engines provide karna jo:
  - Bar‑bar likhne ki zaroorat na pade
  - 1000+ apps me reuse ho sake
  - AI ke liye easily discoverable / callable hon

SDK basically **"Aliens Feature Warehouse"** hai.

---

## 2. SDK ke Core Goals

1. **Maximum Reusability**  
   - Ek baar likho, har jagah use karo (Apps, WebOS engines, external integrations)

2. **Loose Coupling + Strong Contracts**  
   - Features independent ho sakein
   - Lekin inka API contract clear ho

3. **Language-Agnostic Thinking, Language-Specific Implementation**  
   - Concept: "Notification Engine"  
   - Implementation: `PHP`, `JS`, future me `Python`/`Dart`

4. **AI-Friendly Design**  
   - Clear naming, folder structure, README/Docs
   - Taaki AI quickly samajh sake ki kaun sa feature kya karta hai

5. **Zero Duplication**  
   - Koi business logic WebOS + Apps + SDK me copy-paste na ho
   - Logic SDK me rahe, WebOS/App sirf use kare

---

## 3. Top-Level Folder Structure (SDK Root)

Conceptual structure:

```text
/Aliens/SDK
 ├── PHP/
 │    ├── Core/
 │    ├── Domain/
 │    ├── Integration/
 │    ├── Helpers/
 │    └── README.md
 ├── JS/
 │    ├── Core/
 │    ├── UI/
 │    ├── Integration/
 │    ├── Helpers/
 │    └── README.md
 ├── Docs/
 │    ├── Index.md
 │    ├── Engines.md
 │    └── Examples/
 ├── Tests/
 │    ├── PHP/
 │    └── JS/
 └── package.json / composer.json (future automation)
```

High-level rules:

- `PHP/` → Backend engines (business logic, services, helpers, adapters)
- `JS/` → Frontend engines (UI helpers, API clients, WebOS JS engines)
- `Docs/` → Documentation for human + AI
- `Tests/` → Self-checking suite (unit/integration tests)

---

## 4. PHP/ Folder Structure

### 4.1 PHP/Core/

Yahan **system-level reusable engines** honge jo multiple domains me use ho sakte hain.

Examples:

- `PHP/Core/Logger.php` → central logging
- `PHP/Core/Notifier.php` → multi-channel notifications (email, SMS, push, in-app)
- `PHP/Core/Queue.php` → queue abstraction
- `PHP/Core/Cache.php` → extra helper over WebOS Cache (if required)
- `PHP/Core/Validator.php` → common validation utilities

Characteristics:

- WebOS System layer ke saath compatible
- Direct DB use se pehle WebOS ke Row/DB rules ko respect karein
- Clear function signatures, minimal side effects

### 4.2 PHP/Domain/

Yahan domain-specific features honge:

- `PHP/Domain/MLM/TreeEngine.php`
- `PHP/Domain/Exchange/OrderMatching.php`
- `PHP/Domain/Wallet/BalanceEngine.php`
- `PHP/Domain/KYC/DocumentVerifier.php`
- `PHP/Domain/ERP/InvoiceEngine.php`

Rules:

- Har domain ke andar apna sub-folder
- Engines strongly typed soch ke saath (clear responsibilities)
- WebOS apps in engines ko call karte hain, khud logic repeat nahi karte

### 4.3 PHP/Integration/

External services se connect hone ke liye:

- `PHP/Integration/PaymentGateway/{GatewayName}.php`
- `PHP/Integration/SMS/{Provider}.php`
- `PHP/Integration/Email/{Provider}.php`
- `PHP/Integration/Storage/{Provider}.php`

Rules:

- Har provider ke liye separate adapter
- Ek common interface / base class jisse AI + developer samajh sake pattern

### 4.4 PHP/Helpers/

Common helpers:

- `PHP/Helpers/ArrayHelper.php`
- `PHP/Helpers/StringHelper.php`
- `PHP/Helpers/DateHelper.php`

These are generic, non-domain utilities.

---

## 5. JS/ Folder Structure

### 5.1 JS/Core/

WebOS JS engines ke liye:

- `JS/Core/WebOS.API.js`
- `JS/Core/WebOS.Cache.js`
- `JS/Core/WebOS.Screen.js`
- `JS/Core/WebOS.Table.js`
- `JS/Core/WebOS.Forms.js`

Ye files WebOS JS Library ka natural extension ho sakti hain.

### 5.2 JS/UI/

UI components + helpers:

- `JS/UI/ModalEngine.js`
- `JS/UI/ToastEngine.js`
- `JS/UI/FormEnhancer.js`
- `JS/UI/ChartHelper.js`

Goal: Fast, consistent UX across apps.

### 5.3 JS/Integration/

Frontend side integrations:

- `JS/Integration/Analytics/{provider}.js`
- `JS/Integration/Maps/{provider}.js`
- `JS/Integration/Realtime/{provider}.js` (Pusher, Ably, etc.)

### 5.4 JS/Helpers/

Small helpers:

- `JS/Helpers/DomHelper.js`
- `JS/Helpers/FormatHelper.js`

---

## 6. Naming Conventions (AliensStyle in SDK)

1. **PascalCase for files containing classes/engines**  
   - `OrderMatching.php`
   - `NotificationEngine.php`
   - `WebOS.API.js`

2. **Clear suffixes**  
   - `Engine`, `Helper`, `Adapter`, `Service`, `Client`

3. **Domain-based folders**  
   - `Domain/MLM`, `Domain/Exchange`

4. **No random util files**  
   - Har file ka ek clear purpose ho

---

## 7. SDK and WebOS Relation

### 7.1 WebOS → SDK

WebOS core:  
- Boot, System, Kernel, Unit, App, Event

WebOS ki philosophy:

- Jitna generic logic ho sakta hai, usko SDK me shift karo
- WebOS System layer mostly "orchestrator" + "rulebook" rahe

Example:

- **Wrong:** Transaction computation directly inside `App/Exchange/` PHP file
- **Right:** Core calculation in `SDK/PHP/Domain/Exchange/OrderMatching.php`  
  App code only calls: `OrderMatching::Match($OrderData)`

### 7.2 SDK → WebOS

SDK code ko kuch cheezen assume karni chahiye:

- WebOS Row / DB layer available
- Ecosystem / App / Tenant context accessible (via System objects)
- AliensStyle naming + logging pattern follow

SDK files **independent projects** ke liye bhi reusable ban sakti hain, lekin primary consumer WebOS hai.

---

## 8. SDK Versioning Strategy

Version pattern:

- Align with WebOS major versions (e.g. `PHP.25.11.xx`)
- SDK version mapping:

Examples:

- `SDK.PHP.25.11.01`
- `SDK.JS.25.11.01`

Rules:

- Breaking changes = new minor/patch version
- Stable/critical engines ko unnecessary touch nahi karna
- Doc update ke bina behavioral change nahi

---

## 9. How to Add a New SDK Engine (Developer Flow)

### Step 1: Decide Category
- Core / Domain / Integration / Helper?  
- PHP side, JS side, ya both?

### Step 2: Create File in Right Folder
Example: New Notification Engine for PHP

```text
/Aliens/SDK/PHP/Core/NotificationEngine.php
```

### Step 3: Define Clear Interface
- Public methods
- Required parameters
- Return type / structure

### Step 4: Add Doc in `/Docs/Engines.md`
- Name, category, short description, example usage

### Step 5: Wire in WebOS/App if needed
- WebOS System ya App se call integration

---

## 10. AI (AlienCyborg) Usage Rules for SDK

When AlienCyborg is acting as SoftwareEngineer:

1. Pehle check kare:
   - Kya jo feature likhna hai, already SDK me available hai?
2. Agar nahi:
   - New engine design kare → SDK me add kare → App/WebOS se use kare
3. Kabhi bhi:
   - WebOS core ya App me heavy business logic dobara se na likhe
4. Hamesha:
   - SDK ke naming conventions, folder rules and versioning follow kare
5. Documentation:
   - Har naya engine → doc update mandatory

---

## 11. Example: Exchange Order Matching Engine (Concept)

**File:**  
`/Aliens/SDK/PHP/Domain/Exchange/OrderMatching.php`

Responsibilities:

- Buy/Sell order match karna
- Fees calculate karna
- Partial fills handle karna
- DB transactions + logging handle karna (Row engine ke saath)

App code sirf high-level call kare:

```php
$Result = OrderMatching::Match($NewOrder, $Context);
```

Is tarah ke heavy engines hamesha SDK me rehne chahiye.

---

## 12. Checklists

### 12.1 For New SDK Code

- [ ] Kya yeh code ek se zyada projects me reuse hoga?
- [ ] Kya isko Domain/Core/Integration me sahi jagah rakha gaya hai?
- [ ] Kya naming clear hai? (Engine/Helper/Adapter)
- [ ] Kya AliensStyle follow ho raha hai?
- [ ] Kya minimal dependencies hain?
- [ ] Kya doc update hua hai?

### 12.2 For AI-Generated Changes

- [ ] Kya WebOS core ko bina reason touch nahi kiya?
- [ ] Kya heavy logic SDK me shift kiya?
- [ ] Kya path, naming, structure consistent hai?
- [ ] Kya tests/usage example add hua hai (agar critical engine hai)?

---

## 13. Summary

`SDK.Structure` file:

- Aliens Ecosystem me `/Aliens/SDK` ka **master blueprint** hai  
- Batata hai:
  - SDK ka purpose kya hai  
  - Kaun se folders kahan aur kyun  
  - Code kis pattern se add / organize hoga  
- AlienCyborg (SoftwareEngineer role) ke liye yeh ek **reference map** hai  
  taaki wo:
  - Har naye feature ko sahi jagah rakhe  
  - Har code ko maximum reusable banaye  
  - WebOS + Apps ko clean and thin rakhe

Future me is file ko expand karke per-engine templates, standard interfaces,  
aur CI/CD integration rules bhi detail me add kiye ja sakte hain.
