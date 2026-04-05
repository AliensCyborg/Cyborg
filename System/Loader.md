# AlienCyborg — System Loader
# File: AlienCyborg/System/Loader.md
# Scope: Exact loading order for the Cyborg OS

---

## 1. Purpose of Loader

The Loader defines exact loading order for the AlienCyborg OS.
It ensures:
- No file conflicts
- No circular dependencies
- No "guessing" by AI
- Clean role-switching
- Complete control over knowledge loading
- Fast initialization with zero ambiguity

This is the official boot sequence of AlienCyborg.

---

## 2. Loading Order (Top → Bottom)

1. Identity Layer
2. System Layer
3. Active Role Layer
4. Ecosystem Layer (Role-specific)
5. Workflows Layer
6. Memory Layer (Global + Role)

---

## 3. Identity Layer

Load Path: `Cyborg/AlienCyborg/Identity/`

Files:
- Identity/Profile.md
- Identity/Personality.md
- Identity/ThinkingPatterns.md
- Identity/Values.md
- Identity/Memory.md

---

## 4. System Layer

Load Path: `Cyborg/AlienCyborg/System/`

Files:
- System/Behavior.md
- System/Safety.md
- System/Mapping.md
- System/Knowledge.Index.md
- System/Knowledge.Loader.md

---

## 5. Active Role Layer

Load Path: `Cyborg/AlienCyborg/Roles/<ActiveRole>/`

Default active role:
- Role.SoftwareEngineer

Files:
- Profile.md
- Rules.md
- Prompts.md
- Memory.md

---

## 6. Skills Load

Load Path: `Roles/<Role>/Skills/`

Files:
- AliensStyle.md
- GitHub.DevOps.md
- JavaScript.md
- PHP.md
- SQL.md
- WebOS.md

---

## 7. Knowledge Load

Load Path: `Roles/<Role>/Knowledge/`

Files:
- WebOS.Master.md
- WebOS.Tech.md
- WebOS.Architecture.md
- Modules.Index.md
- FileTemplates.md

---

## 8. Ecosystem Load (Role-specific)

Load Path: `Roles/<Role>/Ecosystem/`

Folders:
- WebOS/*
- Apps/*
- Brand/*
- SDK/*
- Database/*

---

## 9. Workflows Load

Load Path: `Cyborg/AlienCyborg/Workflows/`

Files:
- TaskTypes.md
- Pipelines.md

All workflow definitions are self-contained inside AlienCyborg/Workflows/.

---

## 10. Memory Layer

Load Path: `Cyborg/AlienCyborg/Memory/`

Files:
- Global.Memory.md

Role Memory:
- `Roles/<Role>/Memory/` (role-specific memory)

---

## 11. Loader Rules

1. NEVER auto-load unknown files
2. Role = Mandatory
3. Identity loads BEFORE System
4. Ecosystem loads only for technical roles
5. Never bypass Mapping.md or Knowledge.Index.md
6. Only load files from Cyborg/AlienCyborg/ (self-contained, no external cyborg paths)

---

## 12. Loader Summary

Identity → System → Role → Skills → Knowledge → Ecosystem → Workflows → Memory
