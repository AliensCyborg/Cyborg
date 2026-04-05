# PHP Skillset — Ultra Detailed Version

## 1. Overview
This document defines the complete PHP-level skillset for Role.SoftwareEngineer inside AlienCyborg. It includes architecture, coding conventions, DB strategy, API design, WebOS integration, and best practices for enterprise-grade systems.

## 2. Core Philosophy
- Write PHP like an OS engineer, not a normal developer.
- Use AliensStyle naming, structure, and clarity.
- Maintain zero-error, zero-duplication code.
- Always integrate with WebOS System, Kernel, and Row engines.

## 3. Language Mastery
### 3.1 Syntax & Operators
- Variables: strong naming discipline.
- Operators: strict usage rules.
- Arrays: associative arrays preferred.
- Object usage: class-based where needed.

### 3.2 Functions
- All functions follow AliensStyle format.
- Default values always included.
- Echo-based debugging blocks used.

### 3.3 Classes
- PascalCase naming.
- Clear segmentation: public, protected, private.

## 4. WebOS Integration
### 4.1 Boot Layer Usage
- Understand Boot/os.php, Boot/shell.php, Boot/template.php.
- Correct integration patterns.

### 4.2 System Engines
- System/Row.php
- System/WebOS_Row.php
- System/User.php
- System/Ecosystem.php
- System/Theme.php
- System/Git.php
- System/App.php
- System/UID.php

### 4.3 Kernel Rules
- Minimal modification.
- Strict access control patterns.

### 4.4 Unit & Event Layers
- Unit-level reusable modules.
- Event-level handlers for schedules, hooks, etc.

## 5. Database Mastery
### 5.1 MDBMS
- Multi-database handling rules.
- Automatic table selection.

### 5.2 Row Engine
- Usage patterns.
- Logging ($echo blocks).
- Status model (1,0,9).

### 5.3 Queries
- Only Row-based queries.
- Strict rules for joins, filters, insertion, update, soft delete.

## 6. API Engineering
### 6.1 Structure
- API folder inside modules.
- Naming conventions.

### 6.2 Security
- Token rules.
- Role-based access.
- Data consistency.

## 7. Module Development
### 7.1 Folder structure
- API, CSS, JS, Template, Component.

### 7.2 Reuse & Scaling
- WebOS engines.
- SDK features.
- Libraries.

## 8. Enterprise Techniques
### 8.1 CI/CD
- Version branching.
- Auto deploy.

### 8.2 Logging
- WebOS echo system.
- File logs.

### 8.3 Architecture
- Maintain single responsibility.
- Multi-tenant awareness.

## 9. Error Handling
- Controlled error exposure.
- Internal debugging patterns.

## 10. Performance Optimization
- Caching rules.
- Query optimization.
- Minimal loops.
- Chunk-based heavy task execution.

## 11. Security
- Input validation.
- SQL injection protection.
- Session rules.

## 12. Advanced Patterns
- Factories.
- Singletons (in controlled form).
- Strategies.
- AliensStyle design patterns.

## 13. Testing
- Manual test flows.
- API test sequences.
- Debug echo wrappers.

## 14. Deployment
- A1, A2, A3 server rules.
- Aliens folder positioning (/Aliens vs /var/vhosts/...).

## 15. MAXPOWER Upgrades
- AI-first coding.
- Predictive module structure.
- Autonomous error correction.
- Automated refactoring rules.
