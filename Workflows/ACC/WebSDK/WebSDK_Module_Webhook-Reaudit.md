---
WorkflowId: WebSDK_Module_Webhook-Reaudit
WorkflowName: WebSDK Module Webhook (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_Webhook
Domain: WebSDK
TargetKind: "Module/Webhook"
ReauditMode: create
Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-18
Author: AlienCyborg
Outputs:
  RunRecord:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileNames: ["Reaudit.md", "Reaudit.json"]
  DeterministicArtifact:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Webhook/"
    FileName: "{WebhookName}.reaudit.md"
Safety:
  NoDelete: true
  ReadOnlyOnCode: true
  ProdWrite: "no"
  DbTouch: "not-required"
  MissingTargetPolicy: "error"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Reaudit.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
---

# WebSDK_Module_Webhook-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebSDK_Module_Webhook`. Read-only on Code.

## [01] Targets
- Format: `ModuleName/WebhookName`
- File: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/webhook/{WebhookName}.php`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (Webhook Baseline)
### 3.1 File Skeleton
- AliensStyle CodeHeader; Author = `AlienCyborg`; CodeFooter present.
- `<?php` start; `Access();` near top.

### 3.2 Webhook Surface
- Signature verification implemented (HMAC / shared-secret per provider).
- Replay protection (nonce / timestamp window) — at minimum noted in header.
- Idempotency on the same event id.
- 2xx response only after successful processing; 4xx for invalid signature.
- Logs every inbound event id + outcome via `$echo`+`d()`.

### 3.3 Safety
- Secrets read from manifest / env, never hardcoded.
- Rate-limit hook present or noted.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
