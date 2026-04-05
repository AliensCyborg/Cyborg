# SDK.API.Rules — Detailed Version  
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.API.Rules.md`

This file defines **the complete rulebook** for how APIs must be designed, documented, validated, secured, versioned, and consumed across the entire **Aliens Ecosystem** — including WebOS, Apps, SDK Modules, and Third‑Party Integrations.

The purpose of this file is to ensure:

- Every API follows **one strict engineering standard**  
- No developer or AI ever writes inconsistent or unsafe APIs  
- APIs remain predictable, testable, secure, scalable  
- Future automation + auto‑generated APIs can follow the same rules  
- AlienCyborg can perfectly understand & create APIs without mistakes  

---

# 1. API Philosophy — Why Rules Exist

API design is the backbone of:

- WebOS ↔ Apps
- Apps ↔ SDK Modules
- Apps ↔ External Services
- Multi‑tenant logic
- AI-based auto‑generation

If API rules break → whole ecosystem collapses.

Therefore, all APIs MUST follow:

**Simple to call  
Simple to understand  
Difficult to misuse  
Impossible to break the system**

---

# 2. API Categories in Aliens Ecosystem

### **2.1 WebOS System APIs**
These are system-level APIs, usually internal:
- DB operations
- Module discovery
- Theme mapping
- Ecosystem resolution

### **2.2 App APIs**
These serve app-specific use cases:
- Login, Register
- Deposit, Withdraw
- User profile
- Orders, Transactions
- Notifications

### **2.3 SDK Module APIs**
Each module (MLM Tree, Order Matching, etc.) exposes its own APIs.
These MUST be:

- Autonomous
- Self-documented
- Reusable
- Not dependent on App UI logic

### **2.4 External Integrations**
These include:
- Payment Gateway APIs
- SMS/Email APIs
- KYC APIs
- AI APIs (Azure/OpenAI)
- Blockchain Nodes

All integrations must be wrapped in **one unified SDK wrapper**.

---

# 3. API Naming Rules

### **3.1 Naming Structure**
```
/api/{AppCode}/{Module}/{Action}
```

Examples:
```
/api/wallet/balance/get
/api/exchange/order/create
/api/mlm/tree/build
```

### **3.2 Allowed Name Patterns**
- Lowercase only  
- Words separated by hyphens ONLY if needed  
- No spaces  
- No camelCase  
- No snake_case  

API names must be **URL-friendly and unconditional**.

---

# 4. Request Rules

### **4.1 All API requests MUST be POST**
Reasons:
- Prevent URL logging of sensitive information  
- Standardization  
- App-wide consistency  
- Security benefit  

### **4.2 Required Headers**
```
Content-Type: application/json
X-App-Key: {APP_ID}
X-Auth: {TOKEN or UID}
X-Device: {Browser / Android / iOS / WebOS}
```

### **4.3 Allowed Payload Format**
```
{
  "uid": "optional",
  "tenant": "optional",
  "ecosystem": "optional",
  "data": { ... }
}
```

Everything MUST be wrapped in a `data` object.

### **4.4 Mandatory Fields**
- `uid` (if request requires logged user)
- `tenant` (tenant-aware systems)
- `ecosystem` (multi-ecosystem systems)
- `data` (actual payload)

---

# 5. Response Rules

### **5.1 Standard Response Format**
```
{
  "status": 1,
  "message": "Success",
  "data": { ... }
}
```

### **5.2 Error Response Format**
```
{
  "status": 0,
  "error_code": "INVALID_INPUT",
  "message": "Input value missing: email",
  "data": {}
}
```

### **5.3 Status Values**
- `1` → Success  
- `0` → Error  

### **5.4 API MUST NEVER return raw DB rows**
Always use cleaned keys.

---

# 6. API Validation Rules

### **6.1 Every API Needs a Validation Layer**
Validations include:
- Required fields  
- Field types  
- Length / Range  
- Format (email, phone, etc.)
- Data ownership (user can access only own data)  
- Tenant permission  

### **6.2 Validation MUST Exist Before Business Logic**

Wrong:
```
Save to DB → Validate afterwards
```

Correct:
```
Validate → Sanitize → Execute → Format output
```

### **6.3 Common Validation Patterns**
- `validate_required(fields)`
- `validate_email(email)`
- `validate_uid(uid)`
- `validate_tenant(tenant)`
- `validate_permissions(uid, module)`
- `validate_input(schema)`

### **6.4 All validation errors MUST use error_code**

---

# 7. API Security Rules

### **7.1 No API can be accessed without AppKey**
Except public APIs.

### **7.2 Sensitive APIs MUST require Auth Token**

### **7.3 Use UID universally**
Numeric IDs should NEVER be sent in request or response.

### **7.4 Rate limiting MUST be applied**
(Handled by WebOS Firewall or Nginx)

### **7.5 SQL should NEVER be directly exposed**
Always use Row() engine.

---

# 8. Multi-Ecosystem & Multi-Tenant Rules

### **8.1 Every API must support**
- `ecosystem`
- `tenant`

Unless explicitly ignored.

### **8.2 Row Engine automatically handles**
- DB selection  
- Table selection  
- Prefix selection  

### **8.3 APIs must not assume**
- Ecosystem = Aliens  
- Tenant = 1  
- App = fixed  

Everything dynamic.

---

# 9. API Versioning Rules

### **9.1 Version must be part of manifest**
```
api_version: "1.0.0"
```

### **9.2 Breaking changes require major version bump**

### **9.3 Version is NOT part of URL**
Handled internally.

---

# 10. Logging Rules

### **10.1 Every API MUST log**
- uid  
- app  
- tenant  
- ecosystem  
- endpoint  
- input  
- output status  
- error if any  

### **10.2 Use WebOS Log Engine**

---

# 11. Error Handling Rules

### **11.1 No raw PHP errors to client**

### **11.2 All errors follow standard format**

### **11.3 Internal Errors → error_code: "INTERNAL_ERROR"**

---

# 12. API Documentation Rules

### **12.1 Each API must have File Documentation**
```
# Purpose
# Input Format
# Output Format
# Error Codes
# Example Request
# Example Response
```

### **12.2 Apps MUST NOT store API docs inside random folders**

Correct place:
```
App/{AppName}/API/{Module}/{Action}.md
```

### **12.3 SDK Modules must store API docs inside module folder**
```
SDK/Modules/{Module}/Module.md
```

---

# 13. AI (AlienCyborg) Behavior Rules for API Work

### **13.1 Before writing API logic**
AI must check:
- Does this API already exist?
- Can an SDK module handle this?
- Is this App-specific or global?

### **13.2 AI must follow**
- Row engine  
- AliensStyle  
- WebOS boot rules  

### **13.3 AI must never**
- Write raw SQL  
- Skip validation  
- Skip error codes  
- Create hidden business logic  

### **13.4 AI must always**
- Use $echo debugging blocks in PHP  
- Follow Boot → System → Module → API pattern  
- Keep output predictable  

---

# 14. API Checklist (Critical)

### Before creating API:
- [ ] Is API necessary?  
- [ ] Does an existing API already solve this?  
- [ ] Should this be SDK module API instead?  
- [ ] Does business logic belong to App or SDK?  

### During creation:
- [ ] Validate  
- [ ] Sanitize  
- [ ] Use Row()  
- [ ] Handle multi‑tenant  
- [ ] Handle multi‑ecosystem  
- [ ] Standard response format  
- [ ] Add logs  

### After creation:
- [ ] Document  
- [ ] Add sample request/response  
- [ ] Add error codes  
- [ ] Add version number  

---

# 15. Summary

This file defines the **official and mandatory API rulebook** for the entire Aliens ecosystem.

If ANY API does NOT follow these rules:

- AI must treat it as incorrect  
- Human dev must refactor it  
- System must not approve production deployment  

These rules ensure:

- Zero inconsistency  
- Maximum speed  
- Maximum automation  
- Maximum AI compatibility  
- Ultra-clean architecture  

---

End of File  
`SDK.API.Rules.md`  
