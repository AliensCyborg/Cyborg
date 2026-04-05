# WebOS.Overview — Role.SoftwareEngineer Ecosystem
(Detailed Version)

## 1. Introduction
This document provides the complete overview of the WebOS ecosystem for the Role.SoftwareEngineer inside AlienCyborg. It explains the architecture, system layers, workflows, runtime behaviour, and internal rulebooks required for engineering inside Aliens WebOS.

## 2. Purpose of This File
- Give the Software Engineer a full understanding of WebOS internal mechanics.
- Provide a single source of truth for Boot → System → Kernel → Unit → OS → Event → App design.
- Serve as base knowledge for AI execution, debugging, refactoring, and building new modules/apps.

## 3. WebOS Core Philosophy
- Multi‑App  
- Multi‑Tenant  
- Multi‑Brand  
- AI‑Friendly Architecture  
- Enterprise‑Grade Boot → System → Kernel flow  
- Minimal Duplication  
- Centralised System Engines  
- Code once, use everywhere  

## 4. WebOS Architecture Overview
WebOS is divided into 8 core layers:
1. Boot  
2. System  
3. Kernel  
4. Unit  
5. OS  
6. Event  
7. Database  
8. App  

Each layer is isolated and serves a specific purpose.

## 5. Boot Layer
Purpose:
- Prepare environment
- Load system
- Resolve ecosystem/app
- Initialize template engine
- Call System & Kernel securely

Key files:
- Boot/os.php
- Boot/shell.php
- Boot/template.php
- Boot/sql.php

## 6. System Layer
Purpose:
Central engine of WebOS. All reusable logic lives here.

Important engines:
- Row Engine
- WebOS_Row Engine
- Ecosystem Engine
- User Engine
- UID Engine
- Theme Engine
- App Engine
- Module Engine
- Cache Engine
- Git Engine

## 7. Kernel Layer
Acts as the "OS Kernel".  
Responsibilities:
- Hard security  
- Access rules  
- Protected execution  
- No direct edits allowed  

## 8. Unit Layer
Reusable building blocks shared across apps.

Examples:
- Analytics Unit  
- Notification Unit  
- Wallet Unit  
- UserMeta Unit  

## 9. OS Layer
System‑level tools:
- System health  
- OS tasks  
- Shell operations  
- CLI utilities  

## 10. Event Layer
Handles internal & external triggers:
- Webhooks
- Cron jobs
- App/Business events

## 11. Database Layer
Contains migration files and structure files.

Key concepts:
- MDBMS (Multi Database Management System)
- Soft Delete (status 9)
- Row API always required

## 12. App Layer
App folders contain:
- App views
- Templates
- API endpoints
- Modules, components

App follows:
System → Kernel → Unit → App flow

## 13. Ecosystem Concept
Ecosystem controls:
- Brand identity  
- App list  
- Modules  
- Themes  
- Tenant configurations  

## 14. The Role of Theme
Theme decides:
- UI layout
- Colors, typography
- Components
- Header/footer/sidebars
- Page structure

## 15. Module Architecture
Module contains:
- API  
- Component  
- Template  
- CSS  
- JS  
- Events  

## 16. WebOS Constants Overview
- i → Logged‑in User  
- ecosystem → Current ecosystem  
- app → Current app  
- manifest → Active app manifest  
- aliens → Global config  
- webos → WebOS config  

## 17. Request Flow Summary
1. Browser hits index  
2. Boot runs  
3. System initializes  
4. Kernel authenticates  
5. Ecosystem resolved  
6. App resolved  
7. Unit + Module executed  
8. Template rendered  
9. Response returned  

## 18. WebOS for AI (Critical)
AI must:
- Never bypass System engines  
- Never write SQL directly unless allowed  
- Always follow Boot → System → Kernel hierarchy  
- Use Row Engine for DB  
- Respect Status Model  
- Maintain AliensStyle  

## 19. Versioning
Versions follow pattern:
PHP.25.xx.yy

Each increase represents:
- Improved engine  
- Updated architecture  
- New micro‑framework features  

## 20. End of Overview
This file serves as WebOS foundation for AlienCyborg Software Engineer role.
