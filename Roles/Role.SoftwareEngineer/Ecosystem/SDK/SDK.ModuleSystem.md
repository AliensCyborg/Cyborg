# SDK.ModuleSystem — Detailed Version

Location:  
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.ModuleSystem.md`

Is document ka main purpose hai:

- Aliens SDK ke andar **Module System** ka clear concept define karna  
- Batana ki **SDK Modules** kya hote hain, kaise design hote hain, kaise load hote hain  
- WebOS + Apps + Brand + Library ke saath inka connection samjhana  
- Human developers + AI (AlienCyborg) ke liye ek **standard pattern** ready rakhna

Yeh file **SoftwareEngineer role** ke Ecosystem/SDK section ka deep technical guide hai.

---

## 1. SDK Module System — High Level Idea

Normal WebOS world me:

- **App Modules** → App ke andar functional blocks (Wallet, KYC, Orders, etc.)  
- **Brand Modules** → Layout/Theme/Components type cheezen  
- **System Units** → Unit/, System/ engines

SDK ke context me:

**SDK Modules = Reusable Feature Packages**  
Jo:

- Ek feature / domain ko **self-contained package** me wrap karte hain  
- Backend (PHP) + Frontend (JS) + Docs + Tests **ek hi module** ke under ho sakte hain  
- Multiple Apps / Projects / Ecosystems me plug-in style se reuse kiye ja sakte hain

Socho:

> “MLM Tree Engine” ek module hai.  
> “Order Matching Engine” ek module hai.  
> “Notification Suite” ek module hai.

Har module ka apna structure, lifecycle, and API contract hota hai.

---

## 2. Goals of SDK Module System

1. **Plug-and-Play Reuse**  
   - New app banate samay sirf modules select karo, pure feature set mil jaye.

2. **Ultra-Clean Separation**  
   - Module ke andar domain-specific logic ho  
   - WebOS + Apps sirf **consume** karein, duplicate na karein.

3. **AI-First Discoverability**  
   - Har module ka `module.json` / `Module.md` ho jisse AI easily samajh jaye:
     - Is module ka naam
     - Iska purpose
     - Public functions
     - Dependencies

4. **Safe Upgrade Path**  
   - Module versioning se pata rahe:
     - Kaun sa app kaun sa version use kar raha hai  
     - Breaking change kab aaye

5. **Consistent Developer Experience**  
   - Har module ka structure same ho → kisi bhi module ko open karo, flow same lage.

---

## 3. Physical Structure of an SDK Module

Top-Level:

```text
/Aliens/SDK/Modules/
 └── {ModuleCode}/
      ├── module.json
      ├── Module.md
      ├── PHP/
      │    └── ...
      ├── JS/
      │    └── ...
      ├── Assets/
      │    └── ...
      └── Tests/
```

Example:

```text
/Aliens/SDK/Modules/
 ├── MLM.Tree/
 │    ├── module.json
 │    ├── Module.md
 │    ├── PHP/
 │    │    └── TreeEngine.php
 │    ├── JS/
 │    │    └── TreeUI.js
 │    └── Tests/
 │         └── TreeEngine.test.php
 └── Exchange.OrderMatching/
      ├── module.json
      ├── Module.md
      ├── PHP/
      │    └── OrderMatching.php
      └── Tests/
```

### 3.1 `module.json` (Core Metadata)

Sample:

```json
{
  "code": "MLM.Tree",
  "name": "MLM Binary/Unilevel Tree Engine",
  "version": "1.0.0",
  "domain": "MLM",
  "layer": "Backend+Frontend",
  "status": "stable",
  "requires": [
    "Core.Logger >=1.0.0",
    "Core.Validator >=1.0.0"
  ],
  "provides": [
    "Tree.CalculateBV",
    "Tree.AssignSponsor",
    "Tree.BuildDownline"
  ]
}
```

Use:

- WebOS / Apps / Tools module ko discover kar sakein  
- AI read karke samajh sake “ye module kya kaam karta hai”

### 3.2 `Module.md` (Human + AI Documentation)

Isme:
- Conceptual explanation  
- Inputs/Outputs  
- Use cases  
- Example code snippets

---

## 4. Module Types

SDK me modules 4 broad categories me aa sakte hain:

1. **Core Modules**  
   - Logging, Validation, Notification, Queue, etc.

2. **Domain Modules**  
   - MLM, Exchange, Wallet, KYC, ERP, etc.

3. **Integration Modules**  
   - Payment gateways, SMS/Email providers, KYC APIs, Storage providers.

4. **UI/UX Modules**  
   - Ready-made UI flows (KYC wizard, OTP flow, Deposit/Withdraw UI).

Har module ka type `module.json` me `domain` ya `category` key se mark hoga.

---

## 5. Module Lifecycle (From Creation to Usage)

### Step 1: Design

- Business requirement samjho
- Decide karo: ye logic **SDK module** banne layak hai ya sirf ek app-specific file
- If reusable across 2+ apps/clients → Module

### Step 2: Create Folder

```text
/Aliens/SDK/Modules/{Category}.{Name}/
```

Example:
- `MLM.Tree`, `Exchange.OrderMatching`, `Wallet.Ledger`

### Step 3: Add `module.json`

- Code, version, dependencies, provided features define karo.

### Step 4: Implement PHP Logic

- `PHP/` me core engine likho
- WebOS Row/DB rules follow karo
- AliensStyle naming + $echo logging use karo jaha relevant

### Step 5: Implement JS Logic (If needed)

- `JS/` me UI ya client helpers likho

### Step 6: Add Tests

- `Tests/` me minimal but meaningful tests rakho

### Step 7: Wire to WebOS / Apps

- WebOS System / App modules me is engine ka usage add karo
- But actual logic inside SDK hi rahe

---

## 6. Module Loading System

### 6.1 Discovery

WebOS ya Apps ek central **Module Loader** use kar sakte hain jo:

- `/Aliens/SDK/Modules/` scan kare
- `module.json` read kare
- Active/installed modules list bana de

Iska pattern doc-level:

- Ek `ModuleRegistry` jaisa concept ho sakta:
  - `SDK.ModuleRegistry.List()`
  - `SDK.ModuleRegistry.Get("MLM.Tree")`

### 6.2 Activation

Activation flags kaha ho sakte:

- DB (per ecosystem / per app / per tenant)
- Config files
- WebOS System settings

Example:

- Tenant A: `MLM.Tree` + `Wallet.Ledger`
- Tenant B: sirf `Wallet.Ledger`

### 6.3 Version Handling

Rules:

- `module.json` me version required
- Apps specify required version ya min-version
- Major version change → breaking change assume

---

## 7. Relationship: SDK Modules vs WebOS Modules vs App Modules

### 7.1 SDK Module

- Pure **reusable** feature package
- WebOS ke bahar bhi theoretically use ho sake
- No direct UI template (except JS helpers)

### 7.2 WebOS Modules (System / Unit side)

- WebOS ke part
- More OS-level features
- Zyada tightly ecosystem aware

### 7.3 App Modules

- App-specific logic + screens
- Directly user-facing
- Often built on top of SDK modules

Think hierarchy:

> **SDK Module** → raw reusable engine  
> **WebOS Unit/System** → orchestrator / context-aware wrapper  
> **App Module** → actual user feature bundle

---

## 8. AI (AlienCyborg) Rules for Module System

When acting as **SoftwareEngineer** and dealing with modules:

1. Agar koi new feature **2+ apps me kaam aa sakta hai** → SDK Module candidate.
2. App / WebOS core me heavy domain logic likhne se pehle:
   - Check karo: kya existing SDK module se solve ho sakta?
3. Naye SDK module banate time:
   - `module.json` mandatory
   - `Module.md` mandatory
4. Refactor suggestions:
   - Jab dekho duplicates in different apps → propose **SDK Module extraction**.

AI ko hamesha yaad rakhna chahiye:

> “Core domain logic lives in SDK modules. WebOS & Apps orchestrate.”

---

## 9. Example Module — Notification Suite (Concept)

**Path:**  
`/Aliens/SDK/Modules/Notification.Suite/`

- `module.json`
- `Module.md`
- `PHP/Core/NotificationEngine.php`
- `PHP/Channel/Email.php`
- `PHP/Channel/SMS.php`
- `PHP/Channel/InApp.php`
- `JS/ToastClient.js`
- `Tests/NotificationEngine.test.php`

Ek app me usage:

```php
$Echo = 'Sending notification...'; d($Echo);

NotificationEngine::Send([
  'user_id'   => $UID,
  'channels'  => ['email', 'sms'],
  'template'  => 'WELCOME_USER',
  'data'      => [
    'name' => $User['name'],
  ],
]);
```

Is tarah koi bhi app / module easily notifications use kar sakta hai.

---

## 10. Checklists

### 10.1 New SDK Module Checklist

- [ ] Kya module ka naam unique aur meaningful hai?
- [ ] `module.json` ban gaya?
- [ ] `Module.md` me purpose + usage likha gaya?
- [ ] PHP/ code WebOS DB rules follow karta hai?
- [ ] JS/ code (agar hai) WebOS JS standards follow karta hai?
- [ ] Tests/ me kuch base coverage hai?
- [ ] Kya koi similar logic already kahin aur toh nahi likha hua (duplicate)?

### 10.2 AI-Generated Module Checklist

- [ ] Folder structure sahi hai?
- [ ] Paths, naming, category correct?
- [ ] Versioning pattern follow hua?
- [ ] App code ke andar heavy logic nahi chhoda, sab engine me gaya?
- [ ] Docs me practical examples diye?

---

## 11. Summary

**SDK.ModuleSystem** document define karta hai:

- SDK ke andar **Modules** ka full concept
- Folder structure, lifecycle, metadata, loading, activation
- WebOS + Apps + Brand ke saath relation
- AI + human developers ke liye exact rules

Is system ka main benefit:

- Ek baar soch ke properly module bana do  
- Phir har naye project / ecosystem / tenant me wohi module dubara use hota rahe  
- Development speed → ultra fast  
- Quality & consistency → high  
- And of course, AlienCyborg ko har feature **modular + reusable** dikhai de.

Ye file future me:
- Tools ke liye blueprint ban sakti hai (jaise SDK Module Manager UI)
- Automatic module discovery + install wizards ka base ban sakti hai.
