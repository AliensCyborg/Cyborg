# WebOS Engines Reference (v26)

Generated automatically on 2025-12-24 16:12:31 UTC from live sources under `WebOS/PHP.26.00.00/Engine`. Each section lists the engine purpose, location, and every public static entry exposed through the WebOS facade.

## Routing Rules
- Use `WebOS.<Engine><Action>()` from Loader/Router layers; `Main()` executes when no action is specified.
- `EngineRouter` auto-loads classes from `WebOS/PHP.26.00.00/Engine`; keep methods static and side-effect free outside their logic blocks.
- Hooks run before/after corresponding engines when `WebOS.Hook.*` files exist; overrides in `Custom.*` take precedence over core engines.
- Always pass Context arrays (`Tenant/App/Brand/User/Unit`) without mutating them; return mutated copies through payloads.
- Maintain deterministic logging + deny-by-default checks so safety wrappers (lock, rate, policy) stay reliable across retries.

## Quick Index
- [WebOS.ABTest](#webos-abtest) — Enterprise-grade A/B Testing & Experiment Decision Engine Multi-Tenant + Multi-App + Multi-Brand compatible Deterministic, sticky, cache-first variant…
- [WebOS.Abuse](#webos-abuse) — Behaviour-based abuse detection & prevention engine Adaptive risk scoring (not fixed rate limit) Multi-App + Multi-Tenant safe High-performance (cache-first,…
- [WebOS.Account](#webos-account) — Central Account Engine for WebOS Backend Operating System Multi-App + Multi-Tenant Account Management High-Performance, Cache-Aware, Audit-Ready Acts as…
- [WebOS.Agent](#webos-agent) — Autonomous Agent Orchestration Engine for WebOS Acts as AI / Automation Workforce Layer Multi-App + Multi-Tenant safe Enterprise Governance + Policy aware
- [WebOS.AI](#webos-ai) — Enterprise Grade AI Orchestration Engine for WebOS (Multi-App / Multi-Tenant) Unified AI Runtime: Chat / Run / Embed / RAG / AgentRun Safe-by-default: scope,…
- [WebOS.Alert](#webos-alert) — WebOS Nervous System: Alert Event → Dedup → Incident → Dispatch → Delivery Multi-App + Multi-Tenant safe routing and isolation High-Performance, non-blocking…
- [WebOS.Analytics](#webos-analytics) — OS-level analytics & telemetry engine for WebOS Multi-App, Multi-Tenant, High-Performance analytics core Unified tracking for API, Cron, Worker, Webhook,…
- [WebOS.API](#webos-api) — Central API Gateway Engine for WebOS Enterprise-grade API orchestration Multi-App, Multi-Tenant, High-Performance backend core
- [WebOS.Appointment](#webos-appointment) — Universal, multi-tenant, multi-app Appointment & Scheduling Engine Supports clinics, services, meetings, demos, education, enterprise use Conflict-safe booking…
- [WebOS.AssetMgmt](#webos-assetmgmt) — Enterprise-grade Asset Management Engine Multi-App + Multi-Tenant compatible Physical, Digital, License, Subscription assets Full lifecycle: Create → Assign →…
- [WebOS.Assignment](#webos-assignment) — Enterprise-Grade Assignment Management Engine Multi-App + Multi-Tenant + High-Performance Education, Training, Enterprise Tasks compatible
- [WebOS.Attendance](#webos-attendance) — Enterprise-grade Attendance Management Engine Multi-App, Multi-Tenant, High-Performance OS-level backend component for WebOS
- [WebOS.Audit](#webos-audit) — Centralized, tamper-evident audit engine for WebOS Append-only audit fabric (API / Cron / Webhook / System) Multi-App + Multi-Tenant isolation High-performance…
- [WebOS.Auth](#webos-auth) — Enterprise Auth Brain for WebOS (Multi-App + Multi-Tenant) Identity (User/App/Tenant) Context Session validation + Force logout hooks Token parsing…
- [WebOS.Automation](#webos-automation) — System-level Automation Engine for WebOS. Provides enterprise-grade event → rule → pipeline → action orchestration across Multi-App, Multi-Brand, Multi-Tenant…
- [WebOS.Backup](#webos-backup) — OS-level Backup & Restore Engine for WebOS Multi-App, Multi-Tenant, Enterprise-grade Manifest-driven, Secure, Verifiable backups
- [WebOS.Balance](#webos-balance) — Balance state management (Available / Locked / Pending / Total) Ledger / Transaction driven balance updates Multi-Tenant + Multi-App compatible High-…
- [WebOS.Billing](#webos-billing) — Unified Billing Engine for WebOS Multi-App + Multi-Tenant + Multi-Brand billing brain Handles Products, Prices, Invoices, Subscriptions coordination
- [WebOS.Blockchain](#webos-blockchain) — Blockchain abstraction engine for WebOS Chain-agnostic (EVM, Tron, Solana, Internal Ledger) Secure orchestration of wallets, balances, transfers & indexing…
- [WebOS.Boot](#webos-boot) — Initialize complete Aliens ecosystem for each request Highlights : Normalizes PHP environment (timezone, encoding, limits) Uses Aliens['WebOS']['version'] +…
- [WebOS.BotDetect](#webos-botdetect) — Enterprise-grade bot detection + abuse scoring for all WebOS entry points. Works in Multi-App + Multi-Tenant environment using Context isolation. Returns…
- [WebOS.Brand](#webos-brand) — Brand resolution layer for WebOS Multi-App + Multi-Tenant + Multi-Domain support White-label / Enterprise branding engine
- [WebOS.Cache](#webos-cache) — Enterprise-grade universal caching engine for WebOS 26.x DESIGN : Mode-Detection, Auto-UID, TTL, Logging, Force-Fresh.
- [WebOS.Cart](#webos-cart) — Universal Cart Engine for WebOS Works for ANY type of backend: E-commerce Food / Booking Subscription Services Marketplace Multi-Tenant + Multi-App safe High-…
- [WebOS.Category](#webos-category) — Universal Category / Taxonomy Engine for WebOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph…
- [WebOS.Certificate](#webos-certificate) — Centralized Certificate Engine for WebOS Issue, Validate, Revoke, Renew digital certificates Multi-App + Multi-Tenant compatible Secure signing, verification…
- [WebOS.Chat](#webos-chat) — Enterprise-grade Chat backbone for any WebOS App (Multi-App + Multi-Tenant) DM / Group / Channel chat spaces Messaging, membership, receipts scaffolding High-…
- [WebOS.Class](#webos-class) — Universal Class / Module loader for WebOS Multi-App + Multi-Tenant safe instantiation High-performance registry + reflection cache Dependency-aware lightweight…
- [WebOS.Cluster](#webos-cluster) — Provide cluster-level intelligence to WebOS Enable multi-node, multi-region, multi-tenant backend execution Act as the backbone for high-availability,…
- [WebOS.Comment](#webos-comment) — Universal Comment Engine for WebOS Multi-App + Multi-Tenant + Enterprise Grade Threaded comments with moderation, reactions, audit
- [WebOS.Compliance](#webos-compliance) — Central Compliance & Governance Engine for WebOS Enforces data policies, consent, retention, audit Multi-App + Multi-Tenant + Region aware
- [WebOS.ComplianceHR](#webos-compliancehr) — HR Compliance Operating Layer (Policy → Rules → Decision → Audit) Multi-App + Multi-Tenant compatible via Context (app/brand/ecosystem/user) High-Performance:…
- [WebOS.Config](#webos-config) — Centralized configuration manager for WebOS Multi-App, Multi-Tenant config storage In-Memory + File + Database hybrid config system Auto-load configs from: App…
- [WebOS.Constitution](#webos-constitution) — WebOS ka Supreme Governance Layer (Policy + Rules + Enforcement) Multi-App + Multi-Tenant + Enterprise Grade control plane Unit Execution…
- [WebOS.Constraint](#webos-constraint) — Universal Constraint Enforcement Engine for WebOS Enforce hard/soft/observe rules across API, Data, Security, Business layers Multi-App + Multi-Tenant +…
- [WebOS.Container](#webos-container) — Central runtime container for WebOS Context holder (Tenant, App, Brand, User, Env) Service registry with lazy-loading Multi-App + Multi-Tenant isolation…
- [WebOS.Content](#webos-content) — Universal Content Operating Engine for WebOS Supports ANY website / webapp / SaaS / app backend Multi-Tenant + Multi-App + Multi-Brand aware Draft → Publish →…
- [WebOS.Context](#webos-context) — Centralized Context Manager for entire WebOS lifecycle Replaces scattered globals ($i, $app, $brand, $ecosystem) Provides unified API: ContextGet, ContextSet,…
- [WebOS.Core](#webos-core) — WebOS ka "Brain" / Central Kernel Multi-App + Multi-Tenant core registry Request Context (User, App, Brand, Tenant, Request) System Context (Version, Path,…
- [WebOS.Course](#webos-course) — Enterprise-grade Course backend engine (Multi-App + Multi-Tenant) Clean, scoped CRUD + enrollment + progress Loader-only usage via WebOS::CourseAction()
- [WebOS.Cron](#webos-cron) — Enterprise-grade Cron / Scheduler Engine for WebOS Multi-App + Multi-Tenant + Cluster-Safe job orchestration Central brain for all scheduled/background jobs
- [WebOS.Crypto](#webos-crypto) — Enterprise-grade Crypto Engine for WebOS Multi-App, Multi-Tenant, Chain-Agnostic crypto layer Wallet, Balance, Transfer, Ledger (Phase-1)
- [WebOS.CSRF](#webos-csrf) — Enterprise-grade CSRF protection for browser + session(cookie) requests Multi-App + Multi-Tenant isolation by scope (Tenant/App/Brand/User/Session) Supports…
- [WebOS.Currency](#webos-currency) — Enterprise-grade Currency + Money + FX conversion engine Multi-App + Multi-Tenant defaults, policies, and allowed currencies High-precision decimal safety (no…
- [WebOS.Database](#webos-database) — Central Database Operating Layer for WebOS Multi-App + Multi-Tenant + High-Performance DB Engine Read/Write split, Transactions, Safe Queries Powers AliensCRUD…
- [WebOS.DataPolicy](#webos-datapolicy) — OS-level Data Governance & Lifecycle Engine Retention, Archival, Purge, Legal Hold, Masking, Export Control Enforced across ALL units (API, Cron, Webhook, SDK)…
- [WebOS.Debugger](#webos-debugger) — Enterprise-grade Debugging Engine for WebOS Safe production debugging (no data leaks) Multi-App + Multi-Tenant isolation Integrated with Log, Trace, Metric,…
- [WebOS.DebuggerAI](#webos-debuggerai) — System-level autonomous debugging & incident intelligence engine Central error, anomaly, trace and incident brain for WebOS Safe, scoped, auditable debugging…
- [WebOS.Decision](#webos-decision) — Central deterministic decision engine for WebOS Access / Policy / Routing / Scoring decisions Multi-App, Multi-Brand, Multi-Tenant compatible Explainable,…
- [WebOS.Department](#webos-department) — Enterprise-grade Department engine (Multi-Tenant + Multi-App) Department hierarchy (Parent/Path/Depth), move, tree Members mapping, role-ready, policy-ready…
- [WebOS.Deployment](#webos-deployment) — Enterprise-grade deployment orchestration engine Multi-App + Multi-Brand + Multi-Tenant deployments Zero-downtime, rollback-first philosophy OS-style…
- [WebOS.Deposit](#webos-deposit) — Enterprise-grade Deposit engine (Multi-App + Multi-Tenant) Deposit Intent → Provider Init → Webhook/Confirm → Ledger Posting Idempotency + Locking + Audit +…
- [WebOS.Device](#webos-device) — Enterprise-grade Device Identity + Trust + Risk Layer for WebOS Multi-App + Multi-Tenant device registry Session binding support (anti-hijack baseline)…
- [WebOS.DigitalTwin](#webos-digitaltwin) — Runtime Digital Twin for WebOS (System / App / Tenant) Snapshot, Diff, Replay, Simulation, Forecast Safe experimentation + predictive intelligence
- [WebOS.Doc](#webos-doc) — Make WebOS a self-documenting backend Operating System Auto-generate documentation for Engines, Units, Modules Multi-App + Multi-Brand + Multi-Tenant aware AI-…
- [WebOS.Doctor](#webos-doctor) — Enterprise Grade Doctor Domain Engine (Multi-App + Multi-Tenant) Doctor Identity, Verification, Clinic Linking, Schedule, Availability High-Performance Read…
- [WebOS.Download](#webos-download) — Secure, high-performance, enterprise-grade download engine Multi-App + Multi-Tenant safe delivery Supports private/public/signed downloads
- [WebOS.EducationAI](#webos-educationai) — Enterprise Grade AI Engine for Education Systems Powering LMS, Tutors, Exams, Content Factory Multi-App + Multi-Tenant + High Performance
- [WebOS.Email](#webos-email) — Enterprise-grade Email Engine for WebOS Multi-App, Multi-Brand, Multi-Tenant High-performance async email orchestration Template + Provider abstraction Queue-…
- [WebOS.Embedding](#webos-embedding) — Enterprise-grade Embedding orchestration engine Multi-App, Multi-Tenant, High-Performance Provider-agnostic (Azure/OpenAI/Local/Future) Works as Semantic…
- [WebOS.Emergency](#webos-emergency) — Enterprise-grade Incident / Emergency OS Layer for WebOS Multi-App + Multi-Tenant safe incident lifecycle: New → Get → Update → Escalate → Action → Close →…
- [WebOS.Employee](#webos-employee) — Enterprise-grade Employee Engine Multi-Tenant + Multi-App Workforce Core Central employee abstraction for HR, Payroll, Attendance, Projects
- [WebOS.Emulator](#webos-emulator) — Provide a virtualized execution layer for WebOS Units Emulate API / Cron / Webhook / Component execution safely Enable Sandbox, Replay, Shadow, Chaos and Perf…
- [WebOS.Encryption](#webos-encryption) — Central cryptography engine for WebOS Encryption / Decryption (AEAD) Hashing & Password Hashing HMAC Sign / Verify Random / Nonce generation
- [WebOS.Env](#webos-env) — Purpose not documented in source.
- [WebOS.Environment](#webos-environment) — Enterprise-grade Environment Control Plane for WebOS (Multi-App + Multi-Tenant) Single source of truth: Env Profile (perf + security + ops + log + flags)…
- [WebOS.EventBus](#webos-eventbus) — Central nervous system for WebOS Durable, multi-tenant, multi-app event orchestration High-performance publish / subscribe / consume
- [WebOS.Exam](#webos-exam) — Central Exam Engine for WebOS Works for LMS, HR, Certification, Medical, Compliance exams OS-level exam lifecycle management Highlights : Loader + EngineRouter…
- [WebOS.Executor](#webos-executor) — Central execution engine for all Units (API/Cron/Webhook/Asset/Component/Template) Boot files remain orchestration-only (no functions/
- [WebOS.Experiment](#webos-experiment) — Enterprise-grade Experimentation Engine for WebOS A/B Tests, Canary Releases, Progressive Rollouts Multi-App + Multi-Tenant safe by design Cache-first, ultra-…
- [WebOS.Failover](#webos-failover) — Central failover decision & state engine for WebOS Policy-driven, multi-scope failover orchestration Supports Global / App / Tenant / Dependency scopes
- [WebOS.Feature](#webos-feature) — Enterprise-grade Feature Flag & Toggle Engine Multi-App, Multi-Tenant, Role/User scoped Deterministic rollout, variant & kill-switch support
- [WebOS.FeatureFlag](#webos-featureflag) — OS-level Feature Flag system for WebOS Runtime feature enable/disable without deployment Multi-App + Multi-Tenant + Role/User aware Deterministic rollout (hash…
- [WebOS.Feed](#webos-feed) — Universal Feed / Activity / Timeline Engine Works for Social, Enterprise, LMS, E-Commerce, System Feeds Multi-App + Multi-Tenant + High-Performance Push / Pull…
- [WebOS.Filter](#webos-filter) — Universal, secure, high-performance filter engine One DSL → DB / Analytics / API ready Policy-driven, tenant-safe, cache-aware filtering
- [WebOS.Firewall](#webos-firewall) — Central request firewall for WebOS Entry-layer protection for API / Webhook / Asset / Cron / Component Multi-App + Multi-Tenant aware High-performance, cache-…
- [WebOS.Formula](#webos-formula) — Enterprise-grade Formula Engine for WebOS Backend OS Safe formula evaluation without eval() Multi-App + Multi-Tenant + Multi-Brand scoping via Context Compile…
- [WebOS.Friend](#webos-friend) — Enterprise-grade Friend / Relationship Engine Multi-Tenant + Multi-App compatible Symmetric friendship graph (A↔B) Privacy, abuse-safe, audit-ready
- [WebOS.Generator](#webos-generator) — Enterprise-grade code, unit, app, module generator Backend factory for WebOS (OS-level capability) Deterministic, auditable, multi-layer aware generation
- [WebOS.Governor](#webos-governor) — Global control & protection layer for WebOS. Acts as: Resource Governor Policy Enforcer Quota & Limit Controller Circuit Breaker Gate Multi-Tenant Safety Layer
- [WebOS.Health](#webos-health) — Enterprise Health System for WebOS (Multi-App + Multi-Tenant) Liveness / Readiness probes for Load Balancers & Orchestrators Quick/Standard/Deep health reports…
- [WebOS.HealthMonitor](#webos-healthmonitor) — System, App, Tenant & Unit health monitoring Liveness, Readiness & Deep Health checks Metric ingestion with ultra-low overhead SLO breach detection & degrade…
- [WebOS.HTTP](#webos-http) — Enterprise-grade HTTP Client Engine for WebOS Secure, observable, multi-tenant outbound HTTP layer SSRF-safe by default Retry, timeout, cache, trace, log…
- [WebOS.Identity](#webos-identity) — Enterprise-grade Identity Fabric for WebOS (Multi-App + Multi-Tenant) Login / Register / Session / Token / OTP orchestration via WebOS Engines Centralized…
- [WebOS.Index](#webos-index) — Central Index Intelligence Layer for WebOS Fast Unit Resolution (Exact + Wildcard) Cache Index & Scope Safety Index Registry, Health, and Audit
- [WebOS.Indexer](#webos-indexer) — Universal indexing engine for all data types (Post, Page, Product, User…) Multi-App + Multi-Tenant safe indexing Incremental, idempotent, audit-ready indexing…
- [WebOS.Insurance](#webos-insurance) — Universal Insurance Engine for Multi-App + Multi-Tenant WebOS Covers: Policies, Plans, Claims, Payouts, Audit, Documents, Integrations Designed as "Coverage…
- [WebOS.Inventory](#webos-inventory) — Enterprise-grade Inventory Operating Engine (Multi-App, Multi-Tenant) Ledger-first design (truth = immutable ledger; balance = projection) Oversell protection…
- [WebOS.Invoice](#webos-invoice) — Enterprise-grade Invoicing Operating Layer for WebOS Multi-App + Multi-Tenant + High-Performance + Audit-Proof Strict Loader/Router compatibility…
- [WebOS.Knowledge](#webos-knowledge) — Central Knowledge Engine for WebOS Multi-App, Multi-Tenant, Enterprise Knowledge Layer Facts, Documents, Entities, Relations, Graph & AI-ready
- [WebOS.KnowledgeGraph](#webos-knowledgegraph) — Enterprise-grade Knowledge Graph Engine for WebOS Multi-App + Multi-Tenant semantic data layer Acts as intelligence backbone for Search, AI, Recommendation,…
- [WebOS.LabReport](#webos-labreport) — Enterprise-grade Lab Report lifecycle engine Multi-App + Multi-Tenant + High-Security medical data handling Works as core backend module for Hospital / Lab /…
- [WebOS.Learning](#webos-learning) — WebOS ka OS-level Learning Engine (Observe → Learn → Suggest/Act → Verify) Multi-App + Multi-Tenant compatible (Context based scoping) High-performance, async-…
- [WebOS.Leave](#webos-leave) — Enterprise-grade Leave Management Engine Multi-App, Multi-Tenant, Policy-driven Fully compatible with WebOS.Loader & EngineRouter
- [WebOS.Ledger](#webos-ledger) — Immutable, enterprise-grade ledger engine Multi-App + Multi-Tenant financial journal Append-only, idempotent, audit-ready
- [WebOS.Lesson](#webos-lesson) — Universal Lesson Engine for WebOS OS-level Lesson primitive (not LMS-only) Multi-App + Multi-Tenant + Multi-Brand compatible
- [WebOS.Like](#webos-like) — Universal Like / Unlike / Reaction engine Works with ANY entity (Post, Comment, Product, Video, Profile, etc.) Multi-App + Multi-Tenant + Enterprise Grade
- [WebOS.Linter](#webos-linter) — Enterprise-grade static code analysis engine for WebOS Enforces AliensStyle + AliensGrade contracts Validates Engine, Boot, Unit, SDK, App, Brand code Acts as…
- [WebOS.LoadBalancer](#webos-loadbalancer) — Logical load balancing for WebOS backend OS Multi-App, Multi-Tenant, Multi-Cluster routing High-performance decision engine (app-level LB) Zero-downtime…
- [WebOS.Locale](#webos-locale) — Enterprise-grade Locale Operating System layer for WebOS Multi-App + Multi-Brand + Multi-Tenant locale resolution + formatting Safe, deterministic, cached…
- [WebOS.Location](#webos-location) — Enterprise-grade Location & Geo-Intelligence Engine Unified location resolution for API, Web, Mobile, SaaS Multi-App + Multi-Tenant aware Security, Privacy &…
- [WebOS.Lock](#webos-lock) — Distributed Lock Engine for WebOS Kernel Prevent race conditions, double execution, data corruption Support API, Cron, Queue, Worker, Task, EventBus
- [WebOS.Log](#webos-log) — Enterprise-grade structured logging backbone for WebOS Multi-App + Multi-Tenant safe (App/Brand/Ecosystem/User/RequestId) High-performance buffered writes with…
- [WebOS.Login](#webos-login) — Enterprise-grade Login Engine for WebOS Multi-App, Multi-Tenant, Secure Authentication Gateway Acts as Identity Entry Point (Password-based v1)
- [WebOS.Logout](#webos-logout) — Enterprise-grade Logout & Access Revocation Engine Multi-App, Multi-Tenant, Multi-Device compatible Token, Session, Cache & Permission invalidation Policy,…
- [WebOS.Manifest](#webos-manifest) — Single Source Of Truth (SSOT) for all configuration Enterprise-grade Manifest engine for: Multi-App Multi-Brand Multi-Tenant Multi-Version WebOS Load + Merge +…
- [WebOS.Media](#webos-media) — Unified Media Engine for WebOS Backend OS Upload, Store, Secure, Deliver, Audit media assets Multi-App + Multi-Tenant safe by design
- [WebOS.MedicalRecord](#webos-medicalrecord) — Universal Medical Record Engine for WebOS Multi-App + Multi-Tenant + Enterprise Grade Versioned, Audited, Secure Medical Records
- [WebOS.Memory](#webos-memory) — Unified system-level memory engine for WebOS Tier-0 (Request Memory) + Tier-2 (Shared Cache) Strict Tenant + App + Scope isolation Safe, fast, auditable memory…
- [WebOS.Message](#webos-message) — Universal Messaging Engine for WebOS Supports chat, system messages, notifications, workflows Multi-Tenant + Multi-App safe High-performance, async-ready,…
- [WebOS.Metric](#webos-metric) — Enterprise-grade Metrics Engine for WebOS Ultra low-overhead counters, gauges, histograms Multi-App + Multi-Tenant safe instrumentation High-cardinality guard…
- [WebOS.Migration](#webos-migration) — Enterprise-grade Migration Engine for WebOS Multi-App + Multi-Tenant schema migration system Deterministic, Idempotent, Audited migrations Loader +…
- [WebOS.Mock](#webos-mock) — Enterprise-grade Mock Engine for WebOS API / HTTP / DB / Email / SMS / Webhook mocking Multi-App + Multi-Tenant safe mock resolution Deterministic + Chaos…
- [WebOS.Module](#webos-module) — [] WebOS ke liye OS-level Module Registry + ModuleMap builder Multi-App + Multi-Tenant module enable/disable, bind/unbind Ultra-fast Resolve + Units/Map cache…
- [WebOS.Monitor](#webos-monitor) — Enterprise-grade observability engine for WebOS Monitor API / Cron / Webhook / Asset / Component lifecycle Measure execution time, memory usage, result state…
- [WebOS.Node](#webos-node) — Distributed Node Management Engine for WebOS Represents a physical / virtual runtime instance (Node) Supports Multi-App + Multi-Tenant execution Provides Node…
- [WebOS.OAuth](#webos-oauth) — OAuth2 Authorization Server + Client Engine for WebOS Central identity authorization fabric Supports Code Flow + PKCE + Refresh + Introspection Fully Loader +…
- [WebOS.Optimize](#webos-optimize) — Self-optimization engine for WebOS Performance, cache, DB, concurrency, degradation planning Multi-App + Multi-Tenant aware optimization
- [WebOS.Orchestrator](#webos-orchestrator) — Engine-of-Engines: universal execution orchestration for WebOS Sync + Async dispatch (Run / Enqueue / Dispatch) Pipeline / Workflow runner (step-based…
- [WebOS.Order](#webos-order) — Enterprise Grade Order Operating Engine (Multi-App + Multi-Tenant) Full Order Lifecycle: New, Get, List, Update, Confirm, Cancel, Pay, Fulfill, Ship, Deliver,…
- [WebOS.Organization](#webos-organization) — Enterprise-grade Organization (Tenant) Engine for WebOS Multi-App + Multi-Tenant organization lifecycle, membership, roles, units, settings, policies, audit…
- [WebOS.Page](#webos-page) — Page Operating Engine for WebOS Multi-App + Multi-Brand + Multi-Tenant Page Resolution Security-aware, Cache-aware, AI-ready Page Backend
- [WebOS.Partition](#webos-partition) — High-performance data partitioning OS layer Multi-App + Multi-Tenant aware Time / Tenant / Policy driven partition resolution Zero-downtime rotation & routing…
- [WebOS.Patient](#webos-patient) — Enterprise Grade Patient Engine (Multi-App + Multi-Tenant) OS-level entity management: Patient identity, lifecycle, privacy-ready High-performance patterns:…
- [WebOS.Payment](#webos-payment) — Unified payment orchestration engine for WebOS Multi-App, Multi-Tenant, Multi-Provider payment backbone Gateway-agnostic normalized payment lifecycle
- [WebOS.Payout](#webos-payout) — Unified outgoing funds engine (Payout / Withdraw / Settlement) Multi-App, Multi-Tenant, Multi-Gateway Async-first, Idempotent, Audit-proof Highlights : Loader…
- [WebOS.Payroll](#webos-payroll) — Enterprise-grade Payroll Operating Engine Multi-App, Multi-Tenant, Policy-Driven Payroll Deterministic, Auditable, High-Performance
- [WebOS.Performance](#webos-performance) — WebOS High-Performance Operating System Engine Measure, track, score and optimize runtime performance Request → Unit → Span → Metrics lifecycle
- [WebOS.Permission](#webos-permission) — Enterprise Grade Permission Engine for WebOS (Multi-App + Multi-Tenant) Central permission gate for API/Cron/Webhook/Assets and business actions RBAC-first…
- [WebOS.Pipeline](#webos-pipeline) — OS-level Pipeline / Workflow Engine Deterministic, idempotent, resumable execution Multi-Tenant + Multi-App safe Sync + Async (Queue/Worker) execution…
- [WebOS.Planner](#webos-planner) — Convert Intent → Deterministic Plan (execution-ready blueprint) Act as OS-level orchestration brain for WebOS Support Multi-App, Multi-Brand, Multi-Tenant…
- [WebOS.Policy](#webos-policy) — Central Policy Engine for WebOS (System Constitution) Access, Security, Rate, Cache, Feature, Compliance policies Multi-Tenant + Multi-App + Multi-Brand aware…
- [WebOS.PolicyAI](#webos-policyai) — Central Policy + Decision Engine for WebOS Deterministic + AI-assisted policy evaluation Multi-App, Multi-Brand, Multi-Tenant safe decisions
- [WebOS.Post](#webos-post) — Universal Content / Post Engine for WebOS Supports Blog, News, Docs, Articles, CMS, Headless APIs Enterprise Grade + Multi-App + Multi-Tenant safe
- [WebOS.Predict](#webos-predict) — Enterprise-grade prediction engine for WebOS Works across all Apps, Tenants, Brands Deterministic, auditable, cache-safe predictions Designed for AI / ML /…
- [WebOS.Prescription](#webos-prescription) — Enterprise-grade Prescription Management Engine Multi-App, Multi-Tenant, Multi-Role compatible Secure, auditable, high-performance medical workflow core
- [WebOS.Product](#webos-product) — Universal Product / Catalog Engine for WebOS Multi-App + Multi-Tenant + Enterprise-Grade Supports Products, Variants, Pricing, Inventory, Media, SEO Backend…
- [WebOS.Profile](#webos-profile) — Universal Profile Engine for WebOS Multi-App + Multi-Tenant + Multi-Brand user identity layer Public / Private / Admin profile isolation Schema-driven, cache-…
- [WebOS.Profiler](#webos-profiler) — High-performance profiling engine for WebOS Measure execution time, memory, spans, violations Feed data to Monitor, Metric, Trace, Log engines Multi-Tenant +…
- [WebOS.Push](#webos-push) — Unified Push Notification Engine for WebOS Supports Multi-App, Multi-Tenant architecture OS-grade abstraction over push delivery Queue-ready, Provider-…
- [WebOS.Quarantine](#webos-quarantine) — Central isolation engine for risky entities Multi-App + Multi-Tenant safe quarantine system Works as ACTION layer after detection engines
- [WebOS.Query](#webos-query) — Secure, Multi-Tenant, Multi-App Query Operating System SQL abstraction with strict safety & performance rules Zero raw SQL by default Prepared statements only…
- [WebOS.QueryAI](#webos-queryai) — Intelligent Query Engine for WebOS Natural Language → Secure Query Plan → Retrieval → Answer Multi-App + Multi-Tenant + Permission-Aware by default
- [WebOS.Queue](#webos-queue) — Enterprise-grade Queue Engine for WebOS (Multi-App + Multi-Tenant) High-performance job enqueue, reserve/lease, ack, fail, retry, DLQ OS-level async backbone…
- [WebOS.RateLimit](#webos-ratelimit) — Enterprise-grade Rate Limiting for WebOS Units (API/Webhook/Cron/etc.) Multi-App + Multi-Tenant isolation via Context-aware keys High-performance counters with…
- [WebOS.Recommend](#webos-recommend) — Universal Recommendation Engine for WebOS (Multi-App + Multi-Tenant) Provide "Suggest / Track / Profile / Explain" as a backend OS capability Safe,…
- [WebOS.Recovery](#webos-recovery) — Enterprise-grade Account Recovery Engine Password reset, access regain, emergency lockdown Multi-App + Multi-Tenant + High-Security recovery workflows
- [WebOS.Recruitment](#webos-recruitment) — Enterprise-grade Recruitment / ATS backend engine Multi-Tenant + Multi-App + Multi-Org safe Works as a core OS-level backend service Fully compatible with…
- [WebOS.Refund](#webos-refund) — Enterprise Grade Refund Management Engine Multi-App + Multi-Tenant safe by Context High-performance state machine + audit timeline Idempotent request support…
- [WebOS.Region](#webos-region) — Region / Geo Intelligence as OS-level capability (Multi-App + Multi-Tenant) Canonical region resolution (Country / Subdivision / City / ZIP) Policy overlay…
- [WebOS.Release](#webos-release) — Enterprise-grade Release Lifecycle Engine for WebOS Multi-App + Multi-Tenant + Multi-Strategy Releases Deterministic, Auditable, Rollbackable OS-level Releases
- [WebOS.Relevance](#webos-relevance) — Enterprise-grade relevance scoring engine Deterministic + explainable relevance for any entity Multi-App, Multi-Tenant, Multi-Mode (search/feed/recommend)…
- [WebOS.Replica](#webos-replica) — Read / Write DB replica routing for WebOS Multi-App + Multi-Tenant aware Lag-aware, policy-driven replica selection Fail-safe primary fallback
- [WebOS.Report](#webos-report) — Universal Reporting Engine for WebOS Multi-App, Multi-Tenant, Enterprise-Grade Reporting OS Layer Live + Async + Materialized report execution orchestration
- [WebOS.Restore](#webos-restore) — Enterprise-grade Restore Orchestration Engine Multi-App, Multi-Tenant, Safe & Auditable Restore Snapshot / PITR / Partial Restore Support Zero-downtime capable…
- [WebOS.Result](#webos-result) — Enterprise-grade Result Management Engine Multi-App + Multi-Tenant safe High-performance, audit-ready, reproducible result system
- [WebOS.Retry](#webos-retry) — Deterministic, policy-driven retry system Supports Inline, Deferred, Scheduled retry modes Enforces retry budget, idempotency, and safety Multi-App + Multi-…
- [WebOS.Role](#webos-role) — Enterprise-grade Authorization Engine (Role + Optional Permission Gate) Multi-App + Multi-Tenant safe role resolution and checks Strict, deterministic,…
- [WebOS.RoleHR](#webos-rolehr) — Enterprise-grade HR Role Management Engine Multi-Tenant + Multi-App + Multi-Scope role orchestration Authoritative HR role source for Permission & Auth engines
- [WebOS.Root](#webos-root) — Global Root Orchestrator for WebOS Single entry governance layer for all Units (API, Cron, Webhook, etc.) Builds RootContext (Tenant, App, User, Policy, Unit)…
- [WebOS.Row](#webos-row) — [] WebOS ka core CRUD engine. Enterprise-grade, multi-app, multi-tenant projects ke liye high-performance Row / Rows / Insert / Update / Status / Delete /…
- [WebOS.RuleEngine](#webos-ruleengine) — Central Decision & Rule Evaluation Engine for WebOS Guard, Compute, Route, Policy & Workflow Rules Multi-App, Multi-Tenant, Multi-Brand aware High-performance,…
- [WebOS.Sandbox](#webos-sandbox) — Secure execution envelope for Units / APIs / Hooks / Plugins Enforce policy-based isolation (Multi-App + Multi-Tenant) Runtime guards: time, memory, output,…
- [WebOS.Scaling](#webos-scaling) — OS-level adaptive scaling brain for WebOS Multi-App, Multi-Tenant, Enterprise-grade performance control Policy-driven, metric-aware, safe auto-scaling
- [WebOS.Schema](#webos-schema) — Treat Database Schema as OS-Level Asset Schema Registry (Source of Truth) Live DB Introspection Drift Detection (Registry vs Reality) Safe Migration Planning…
- [WebOS.SDK](#webos-sdk) — Enterprise SDK Orchestrator for WebOS (Multi-App + Multi-Tenant) Deterministic SDK Module/Provider resolution with layered overrides Registry-driven SDK…
- [WebOS.Search](#webos-search) — Enterprise-grade Search Engine for Multi-App + Multi-Tenant WebOS Query, Suggest, Index (hooks-ready), Health, Explain Permission-aware + Cache-safe + High-…
- [WebOS.Secrets](#webos-secrets) — Centralized, secure, enterprise-grade Secrets Management Engine Multi-Tenant + Multi-App + Multi-Brand aware Supports versioning, rotation, policies, audit…
- [WebOS.SecRuleurity](#webos-secruleurity) — WebOS OS-Level Rule Engine (Enterprise Grade) Multi-App + Multi-Tenant Policy / Decision Layer Data-driven rules (no hard-coded if-else across apps) Fast…
- [WebOS.Security](#webos-security) — Universal Category / Taxonomy Engine for WebOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph…
- [WebOS.SecurityScan](#webos-securityscan) — Enterprise-grade static & policy security scanner for WebOS Multi-App + Multi-Tenant aware Release gate + audit-ready security reports WebOS-specific rule…
- [WebOS.SelfTest](#webos-selftest) — Self-healing engine for WebOS Detect, plan, apply and rollback system repairs Multi-App + Multi-Tenant safe Policy driven, audit ready, zero-downtime friendly
- [WebOS.Semantic](#webos-semantic) — Provide semantic intelligence layer for WebOS Meaning-based search, indexing, embeddings & ranking Multi-App, Multi-Tenant, Enterprise-grade isolation
- [WebOS.Server](#webos-server) — Server-level Operating System engine for WebOS Provides node identity, health, capacity, modes & cluster signals Works across Single Server → Multi-Server →…
- [WebOS.Session](#webos-session) — Enterprise-grade Session Operating Layer for WebOS (Multi-App, Multi-Tenant) Supports Browser Cookie Sessions + API Bearer Sessions + Refresh Tokens High-…
- [WebOS.Shard](#webos-shard) — Enterprise-grade Sharding Engine for WebOS (Multi-App + Multi-Tenant) Deterministic shard routing (tenant/user/table/module) Directory-based shard map with…
- [WebOS.Share](#webos-share) — Enterprise Grade Share System (Multi-App + Multi-Tenant) Create secure share links (tokenized, scoped, revoke-able) Validate/Open share…
- [WebOS.Shipping](#webos-shipping) — Enterprise-grade Shipping & Logistics Engine Multi-App + Multi-Tenant + OS-Level abstraction Carrier-agnostic, Rule-driven, Auditable shipping system
- [WebOS.Signal](#webos-signal) — WebOS Control-Plane Signaling Engine (OS-Level Signals) Multi-App + Multi-Tenant safe signaling (Scope enforced) High-performance signals using adapters (Cache…
- [WebOS.Signature](#webos-signature) — Enterprise-grade request signing & verification engine Protect APIs, Webhooks, internal service calls Prevent tampering, replay attacks, cross-tenant abuse
- [WebOS.Simulation](#webos-simulation) — Enterprise-grade Simulation Engine for WebOS (Multi-App + Multi-Tenant) Run realistic sandbox simulations of API/Cron/Webhook/Engine scenarios Deterministic…
- [WebOS.SmartContract](#webos-smartcontract) — Smart Contract Operating System Layer (Registry + Deploy + Call + Send) Multi-App + Multi-Tenant compatible (Context enforced) High-Performance, Cache-first,…
- [WebOS.SMS](#webos-sms) — Enterprise-grade SMS orchestration layer for WebOS Backend OS Multi-App + Multi-Tenant provider-agnostic SMS sending OTP suite (Send OTP + Verify) with anti-…
- [WebOS.SocialGraph](#webos-socialgraph) — Enterprise-grade Social Graph kernel for Multi-App + Multi-Tenant WebOS Generic Node↔Node relationships (FOLLOW, FRIEND, BLOCK, MUTE, MEMBER...) High-…
- [WebOS.Socket](#webos-socket) — WebOS real-time backend backbone (WS/SSE/Long-Poll orchestration) Gateway + Backplane driven architecture (PHP = policy + routing + publish) Multi-App + Multi-…
- [WebOS.Sorting](#webos-sorting) — Centralized, secure, deterministic sorting engine SQL-level + Memory-level sorting Multi-App, Multi-Tenant, Role-aware Stable sorting with forced tie-breakers…
- [WebOS.State](#webos-state) — OS-level State Management for WebOS (Multi-App + Multi-Tenant) Strong State Read/Write + Atomic Transitions + Audit/History High-performance cache-first reads…
- [WebOS.Stream](#webos-stream) — High-Performance Streaming Engine for WebOS Supports SSE, chunked streaming, publish/subscribe Multi-App + Multi-Tenant safe Enterprise-grade audit, policy &…
- [WebOS.Subscription](#webos-subscription) — Enterprise Grade Subscription Engine (Multi-App + Multi-Tenant) Plan Catalog + Subscription Lifecycle + Entitlements + Usage Metering (OS Layer) Deterministic…
- [WebOS.Tag](#webos-tag) — Universal Tag Engine for WebOS Multi-App + Multi-Tenant + High-Performance Entity-agnostic tagging (Post, Product, User, Order, etc.)
- [WebOS.Task](#webos-task) — Universal Task Orchestration Engine for WebOS Convert any backend work into reliable Tasks Multi-App + Multi-Tenant safe execution Enterprise-grade retry,…
- [WebOS.Tax](#webos-tax) — Central Tax Decision & Calculation Engine for WebOS Works for any Website / WebApp / Mobile App / SaaS / Marketplace Multi-Tenant + Multi-App + Multi-…
- [WebOS.Template](#webos-template) — Enterprise-grade Template Rendering Engine for WebOS Multi-App + Multi-Brand + Multi-Tenant aware Secure, Cacheable, Overrideable View System
- [WebOS.Tenant](#webos-tenant) — Enterprise-grade Multi-Tenant Runtime for WebOS (Multi-App + Multi-Brand) Deterministic Tenant resolution (Domain / Header / Path / Session) Tenant isolation…
- [WebOS.Test](#webos-test) — Enterprise-grade Test Operating System for WebOS Multi-App + Multi-Brand + Multi-Tenant test runner (matrix mode) Engine/Router compliance tests (Main()…
- [WebOS.Theme](#webos-theme) — OS-level Theme Engine for WebOS Multi-App, Multi-Brand, Multi-Tenant theme resolution Design Tokens, Assets, Templates, Variants Ultra-fast cached resolution…
- [WebOS.Timeout](#webos-timeout) — OS-grade Timeout / Deadline enforcement for: API, Cron, Webhook, Worker, Queue, Task, Pipeline (Unit + Step) Multi-App + Multi-Tenant safe budgets Nested…
- [WebOS.Timezone](#webos-timezone) — Enterprise Grade Timezone Engine for Multi-App + Multi-Tenant WebOS Deterministic timezone resolution (Payload → User → Tenant → Brand → App → Ecosystem → UTC)…
- [WebOS.Toggle](#webos-toggle) — Enterprise-grade Feature Toggle / Flag / Kill-Switch Engine Multi-App, Multi-Brand, Multi-Tenant, User-aware resolution Ultra-fast read path using snapshot +…
- [WebOS.Trace](#webos-trace) — Distributed tracing for WebOS (API, Cron, Queue, Worker, Pipeline) Ultra-low overhead, async-safe, multi-tenant aware Correlates EngineRouter → Engine → DB →…
- [WebOS.Transaction](#webos-transaction) — Universal Transaction Engine for WebOS Supports any backend: Wallet, E-Commerce, SaaS, MLM, Banking-style systems Multi-Tenant, Multi-App, Enterprise-grade…
- [WebOS.TwoFactor](#webos-twofactor) — Provide enterprise‑grade Two‑Factor Authentication (2FA) Support TOTP, Email OTP, SMS OTP, Backup Codes (extensible) Policy‑driven, risk‑aware, step‑up…
- [WebOS.Update](#webos-update) — Enterprise-grade Update Orchestration Engine Core / SDK / App / Brand / Tenant updates Zero-downtime (Blue-Green / Canary / Rolling) Safe rollback + audit…
- [WebOS.UpgradePlanner](#webos-upgradeplanner) — Deterministic upgrade planning for WebOS ecosystem Multi-App, Multi-Brand, Multi-Tenant safe rollout planning Zero-downtime upgrade strategy generation Risk…
- [WebOS.Upload](#webos-upload) — Enterprise-grade Upload Engine for WebOS Multi-App, Multi-Tenant, High-Performance Upload OS Layer Supports chunked, resumable, secure uploads
- [WebOS.User](#webos-user) — Enterprise Grade User Engine for WebOS (Multi-App + Multi-Tenant) High-performance read/write with cache-first + strict scope enforcement Centralized user…
- [WebOS.Vector](#webos-vector) — Enterprise-grade Vector & Embedding Management Engine Multi-App + Multi-Tenant + AI-ready Vector Store Semantic Search, Similarity, Hybrid Search foundation
- [WebOS.Version](#webos-version) — Central Version Intelligence Engine for WebOS Multi-App, Multi-Tenant, Multi-Layer version resolution Enterprise-grade rollout, canary, pinning & compatibility…
- [WebOS.Wallet](#webos-wallet) — Enterprise-grade Wallet Engine for any backend (Website/WebApp/Mobile/SaaS) Multi-App + Multi-Tenant safe by design High-performance read paths + transaction-…
- [WebOS.Webhook](#webos-webhook) — Enterprise-grade Webhook Operating Layer (Inbound + Outbound) Multi-App + Multi-Tenant compatible by Context + Unit resolver Signature verify (HMAC), replay…
- [WebOS.Wellness](#webos-wellness) — Universal Wellness Domain Engine for WebOS (Multi-App + Multi-Tenant) Standardize Wellness Profile, Events, Habits, Goals, Aggregates High-performance, audit-…
- [WebOS.Withdraw](#webos-withdraw) — Enterprise Grade Withdraw Engine for Multi-App + Multi-Tenant WebOS.
- [WebOS.Worker](#webos-worker) — OS-grade background job execution engine Multi-App + Multi-Tenant + Fair Scheduling Safe async execution with retries, locks, metrics
- [WebOS.Workflow](#webos-workflow) — Workflow-as-OS-Resource for WebOS backend Multi-App + Multi-Tenant workflow definition + execution State/DAG/Saga-ready (v1 focuses on deterministic sequential…

## Engine Details
### <span id="webos-abtest"></span>WebOS.ABTest
- **Purpose**: Enterprise-grade A/B Testing & Experiment Decision Engine Multi-Tenant + Multi-App + Multi-Brand compatible Deterministic, sticky, cache-first variant assignment Deeply integrated with WebOS core engines
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.ABTest.php](WebOS/PHP.26.00.00/Engine/WebOS.ABTest.php)
- **Class**: `WebOS_ABTest`
- **Facade Entry Points**:
  - `WebOS.ABTest()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.ABTest()` |
### <span id="webos-abuse"></span>WebOS.Abuse
- **Purpose**: Behaviour-based abuse detection & prevention engine Adaptive risk scoring (not fixed rate limit) Multi-App + Multi-Tenant safe High-performance (cache-first, O(1) hot path)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Abuse.php](WebOS/PHP.26.00.00/Engine/WebOS.Abuse.php)
- **Class**: `WebOS_Abuse`
- **Facade Entry Points**:
  - `WebOS.Abuse()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AbuseDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `WebOS.AbuseFail()` → `public static function Fail(array $Payload, array $Context)`
  - `WebOS.AbuseSuccess()` → `public static function Success(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Abuse()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `WebOS.AbuseDecide()` |
| Fail | `public static function Fail(array $Payload, array $Context)` | `WebOS.AbuseFail()` |
| Success | `public static function Success(array $Payload, array $Context)` | `WebOS.AbuseSuccess()` |
### <span id="webos-account"></span>WebOS.Account
- **Purpose**: Central Account Engine for WebOS Backend Operating System Multi-App + Multi-Tenant Account Management High-Performance, Cache-Aware, Audit-Ready Acts as Identity Container for all apps and tenants
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Account.php](WebOS/PHP.26.00.00/Engine/WebOS.Account.php)
- **Class**: `WebOS_Account`
- **Facade Entry Points**:
  - `WebOS.Account()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AccountNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.AccountGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.AccountStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Account()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.AccountNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.AccountGet()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.AccountStatus()` |
### <span id="webos-agent"></span>WebOS.Agent
- **Purpose**: Autonomous Agent Orchestration Engine for WebOS Acts as AI / Automation Workforce Layer Multi-App + Multi-Tenant safe Enterprise Governance + Policy aware
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Agent.php](WebOS/PHP.26.00.00/Engine/WebOS.Agent.php)
- **Class**: `WebOS_Agent`
- **Facade Entry Points**:
  - `WebOS.Agent()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AgentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.AgentRun()` → `public static function Run(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Agent()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.AgentNew()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.AgentRun()` |
### <span id="webos-ai"></span>WebOS.AI
- **Purpose**: Enterprise Grade AI Orchestration Engine for WebOS (Multi-App / Multi-Tenant) Unified AI Runtime: Chat / Run / Embed / RAG / AgentRun Safe-by-default: scope, limits, policy gates, audit, rate-limit, cache Provider abstraction: Azure/OpenAI/Local via HTTP Engine + Secrets Engine
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.AI.php](WebOS/PHP.26.00.00/Engine/WebOS.AI.php)
- **Class**: `WebOS_AI`
- **Facade Entry Points**:
  - `WebOS.AI()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AIChat()` → `public static function Chat(array $Payload, array $Context)`
  - `WebOS.AIRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.AIEmbed()` → `public static function Embed(array $Payload, array $Context)`
  - `WebOS.AIRagQuery()` → `public static function RagQuery(array $Payload, array $Context)`
  - `WebOS.AIAgentRun()` → `public static function AgentRun(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.AI()` |
| Chat | `public static function Chat(array $Payload, array $Context)` | `WebOS.AIChat()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.AIRun()` |
| Embed | `public static function Embed(array $Payload, array $Context)` | `WebOS.AIEmbed()` |
| RagQuery | `public static function RagQuery(array $Payload, array $Context)` | `WebOS.AIRagQuery()` |
| AgentRun | `public static function AgentRun(array $Payload, array $Context)` | `WebOS.AIAgentRun()` |
### <span id="webos-alert"></span>WebOS.Alert
- **Purpose**: WebOS Nervous System: Alert Event → Dedup → Incident → Dispatch → Delivery Multi-App + Multi-Tenant safe routing and isolation High-Performance, non-blocking delivery via Queue/Worker ---------------------------------------------------------------------------- DESIGN (AliensGrade): Boot files = orchestration only Engine = intelligence + policies No direct requires/includes here (EngineRouter loads this file) All dependencies called via WebOS::EngineAction() facade ---------------------------------------------------------------------------- DEPENDENCIES (Expected Engines): WebOS.Cache   : CacheGet, CacheSet (or Cache Main wrapper) WebOS.Lock    : LockAcquire, LockRelease WebOS.Queue   : QueuePush WebOS.Database: DatabaseNew, DatabaseUpdate, DatabaseRow, DatabaseRows WebOS.Security: SecurityHash, SecurityNow, SecurityValidate WebOS.Log     : Log (or LOG) WebOS.Metric  : MetricInc, MetricTiming WebOS.Trace   : TraceStart, TraceEnd (optional) ---------------------------------------------------------------------------- TABLES (Recommended): webos_alert_event webos_alert_incident webos_alert_delivery webos_alert_ack webos_alert_rule webos_alert_policy ---------------------------------------------------------------------------- PUBLIC API (Loader Calls): WebOS::AlertNew([...])        → create event + incident + dispatch jobs WebOS::AlertDispatch([...])   → dispatch deliveries (usually Worker) WebOS::AlertAck([...])        → acknowledge incident WebOS::AlertResolve([...])    → resolve incident WebOS::AlertSnooze([...])     → snooze incident WebOS::AlertIncidentGet([...]) WebOS::AlertDeliveryList([...]) WebOS::ALERT([...])           → Main() ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Alert.php](WebOS/PHP.26.00.00/Engine/WebOS.Alert.php)
- **Class**: `WebOS_Alert`
- **Facade Entry Points**:
  - `WebOS.Alert()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.AlertNew()` → `public static function New($Payload = [], $Context = [])`
  - `WebOS.AlertDispatch()` → `public static function Dispatch($Payload = [], $Context = [])`
  - `WebOS.AlertAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `WebOS.AlertSnooze()` → `public static function Snooze($Payload = [], $Context = [])`
  - `WebOS.AlertResolve()` → `public static function Resolve($Payload = [], $Context = [])`
  - `WebOS.AlertIncidentGet()` → `public static function IncidentGet($Payload = [], $Context = [])`
  - `WebOS.AlertDeliveryList()` → `public static function DeliveryList($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Alert()` |
| New | `public static function New($Payload = [], $Context = [])` | `WebOS.AlertNew()` |
| Dispatch | `public static function Dispatch($Payload = [], $Context = [])` | `WebOS.AlertDispatch()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `WebOS.AlertAck()` |
| Snooze | `public static function Snooze($Payload = [], $Context = [])` | `WebOS.AlertSnooze()` |
| Resolve | `public static function Resolve($Payload = [], $Context = [])` | `WebOS.AlertResolve()` |
| IncidentGet | `public static function IncidentGet($Payload = [], $Context = [])` | `WebOS.AlertIncidentGet()` |
| DeliveryList | `public static function DeliveryList($Payload = [], $Context = [])` | `WebOS.AlertDeliveryList()` |
### <span id="webos-analytics"></span>WebOS.Analytics
- **Purpose**: OS-level analytics & telemetry engine for WebOS Multi-App, Multi-Tenant, High-Performance analytics core Unified tracking for API, Cron, Worker, Webhook, System units
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Analytics.php](WebOS/PHP.26.00.00/Engine/WebOS.Analytics.php)
- **Class**: `WebOS_Analytics`
- **Facade Entry Points**:
  - `WebOS.Analytics()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AnalyticsTrack()` → `public static function Track(array $Payload, array $Context)`
  - `WebOS.AnalyticsBatch()` → `public static function Batch(array $Payload, array $Context)`
  - `WebOS.AnalyticsQuery()` → `public static function Query(array $Payload, array $Context)`
  - `WebOS.AnalyticsRollup()` → `public static function Rollup(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Analytics()` |
| Track | `public static function Track(array $Payload, array $Context)` | `WebOS.AnalyticsTrack()` |
| Batch | `public static function Batch(array $Payload, array $Context)` | `WebOS.AnalyticsBatch()` |
| Query | `public static function Query(array $Payload, array $Context)` | `WebOS.AnalyticsQuery()` |
| Rollup | `public static function Rollup(array $Payload, array $Context)` | `WebOS.AnalyticsRollup()` |
### <span id="webos-api"></span>WebOS.API
- **Purpose**: Central API Gateway Engine for WebOS Enterprise-grade API orchestration Multi-App, Multi-Tenant, High-Performance backend core
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.API.php](WebOS/PHP.26.00.00/Engine/WebOS.API.php)
- **Class**: `WebOS_API`
- **Facade Entry Points**:
  - `WebOS.API()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.API()` |
### <span id="webos-appointment"></span>WebOS.Appointment
- **Purpose**: Universal, multi-tenant, multi-app Appointment & Scheduling Engine Supports clinics, services, meetings, demos, education, enterprise use Conflict-safe booking with Hold → Confirm flow Fully compatible with WebOS Loader & EngineRouter
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Appointment.php](WebOS/PHP.26.00.00/Engine/WebOS.Appointment.php)
- **Class**: `WebOS_Appointment`
- **Facade Entry Points**:
  - `WebOS.Appointment()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AppointmentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.AppointmentGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.AppointmentList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.AppointmentCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.AppointmentReschedule()` → `public static function Reschedule(array $Payload, array $Context)`
  - `WebOS.AppointmentSlotHold()` → `public static function SlotHold(array $Payload, array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Appointment()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.AppointmentNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.AppointmentGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.AppointmentList()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.AppointmentCancel()` |
| Reschedule | `public static function Reschedule(array $Payload, array $Context)` | `WebOS.AppointmentReschedule()` |
| SlotHold | `public static function SlotHold(array $Payload, array $Context = [])` | `WebOS.AppointmentSlotHold()` |
### <span id="webos-assetmgmt"></span>WebOS.AssetMgmt
- **Purpose**: Enterprise-grade Asset Management Engine Multi-App + Multi-Tenant compatible Physical, Digital, License, Subscription assets Full lifecycle: Create → Assign → Maintain → Audit → Retire
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.AssetMgmt.php](WebOS/PHP.26.00.00/Engine/WebOS.AssetMgmt.php)
- **Class**: `WebOS_AssetMgmt`
- **Facade Entry Points**:
  - `WebOS.AssetMgmt()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AssetMgmtAssetNew()` → `public static function AssetNew(array $Payload, array $Context)`
  - `WebOS.AssetMgmtAssetGet()` → `public static function AssetGet(array $Payload, array $Context)`
  - `WebOS.AssetMgmtAssetUpdate()` → `public static function AssetUpdate(array $Payload, array $Context)`
  - `WebOS.AssetMgmtAssign()` → `public static function Assign(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.AssetMgmt()` |
| AssetNew | `public static function AssetNew(array $Payload, array $Context)` | `WebOS.AssetMgmtAssetNew()` |
| AssetGet | `public static function AssetGet(array $Payload, array $Context)` | `WebOS.AssetMgmtAssetGet()` |
| AssetUpdate | `public static function AssetUpdate(array $Payload, array $Context)` | `WebOS.AssetMgmtAssetUpdate()` |
| Assign | `public static function Assign(array $Payload, array $Context)` | `WebOS.AssetMgmtAssign()` |
### <span id="webos-assignment"></span>WebOS.Assignment
- **Purpose**: Enterprise-Grade Assignment Management Engine Multi-App + Multi-Tenant + High-Performance Education, Training, Enterprise Tasks compatible
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Assignment.php](WebOS/PHP.26.00.00/Engine/WebOS.Assignment.php)
- **Class**: `WebOS_Assignment`
- **Facade Entry Points**:
  - `WebOS.Assignment()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AssignmentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.AssignmentList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.AssignmentGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.AssignmentSubmit()` → `public static function Submit(array $Payload, array $Context)`
  - `WebOS.AssignmentGrade()` → `public static function Grade(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Assignment()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.AssignmentNew()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.AssignmentList()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.AssignmentGet()` |
| Submit | `public static function Submit(array $Payload, array $Context)` | `WebOS.AssignmentSubmit()` |
| Grade | `public static function Grade(array $Payload, array $Context)` | `WebOS.AssignmentGrade()` |
### <span id="webos-attendance"></span>WebOS.Attendance
- **Purpose**: Enterprise-grade Attendance Management Engine Multi-App, Multi-Tenant, High-Performance OS-level backend component for WebOS
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Attendance.php](WebOS/PHP.26.00.00/Engine/WebOS.Attendance.php)
- **Class**: `WebOS_Attendance`
- **Facade Entry Points**:
  - `WebOS.Attendance()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AttendanceSessionNew()` → `public static function SessionNew(array $Payload, array $Context)`
  - `WebOS.AttendanceMark()` → `public static function Mark(array $Payload, array $Context)`
  - `WebOS.AttendanceSummary()` → `public static function Summary(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Attendance()` |
| SessionNew | `public static function SessionNew(array $Payload, array $Context)` | `WebOS.AttendanceSessionNew()` |
| Mark | `public static function Mark(array $Payload, array $Context)` | `WebOS.AttendanceMark()` |
| Summary | `public static function Summary(array $Payload, array $Context)` | `WebOS.AttendanceSummary()` |
### <span id="webos-audit"></span>WebOS.Audit
- **Purpose**: Centralized, tamper-evident audit engine for WebOS Append-only audit fabric (API / Cron / Webhook / System) Multi-App + Multi-Tenant isolation High-performance write path Privacy-safe, compliance-ready (SOC2 / ISO / GDPR)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Audit.php](WebOS/PHP.26.00.00/Engine/WebOS.Audit.php)
- **Class**: `WebOS_Audit`
- **Facade Entry Points**:
  - `WebOS.Audit()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AuditLog()` → `public static function Log(array $Payload, array $Context)`
  - `WebOS.AuditVerify()` → `public static function Verify(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Audit()` |
| Log | `public static function Log(array $Payload, array $Context)` | `WebOS.AuditLog()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.AuditVerify()` |
### <span id="webos-auth"></span>WebOS.Auth
- **Purpose**: Enterprise Auth Brain for WebOS (Multi-App + Multi-Tenant) Identity (User/App/Tenant) Context Session validation + Force logout hooks Token parsing (Bearer/API-Key) + lightweight signed token support Highlights  : Boot-friendly (Orchestration-only boots call this engine) Deny-by-default security mindset High-performance: minimal DB assumptions + cache-ready Dependencies : WebOS::Cache()   (optional but recommended) WebOS::Config()  (optional; used for secrets/TTLs/policies) WebOS::Log()     (optional; for security logging) WebOS::Security()(optional; for IP/user-agent checks) Globals/Consts   : App, i Notes : DB tables (sessions/tokens/attempts) are integration-ready via stubs. ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Auth.php](WebOS/PHP.26.00.00/Engine/WebOS.Auth.php)
- **Class**: `WebOS_Auth`
- **Facade Entry Points**: None documented
### <span id="webos-automation"></span>WebOS.Automation
- **Purpose**: System-level Automation Engine for WebOS. Provides enterprise-grade event → rule → pipeline → action orchestration across Multi-App, Multi-Brand, Multi-Tenant environments.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Automation.php](WebOS/PHP.26.00.00/Engine/WebOS.Automation.php)
- **Class**: `WebOS_Automation`
- **Facade Entry Points**:
  - `WebOS.Automation()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.AutomationNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.AutomationList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.AutomationTrigger()` → `public static function Trigger(array $Payload, array $Context)`
  - `WebOS.AutomationConsume()` → `public static function Consume(array $Payload, array $Context)`
  - `WebOS.AutomationRun()` → `public static function Run(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Automation()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.AutomationNew()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.AutomationList()` |
| Trigger | `public static function Trigger(array $Payload, array $Context)` | `WebOS.AutomationTrigger()` |
| Consume | `public static function Consume(array $Payload, array $Context)` | `WebOS.AutomationConsume()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.AutomationRun()` |
### <span id="webos-backup"></span>WebOS.Backup
- **Purpose**: OS-level Backup & Restore Engine for WebOS Multi-App, Multi-Tenant, Enterprise-grade Manifest-driven, Secure, Verifiable backups
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Backup.php](WebOS/PHP.26.00.00/Engine/WebOS.Backup.php)
- **Class**: `WebOS_Backup`
- **Facade Entry Points**:
  - `WebOS.Backup()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.BackupCreate()` → `public static function Create(array $Payload, array $Context)`
  - `WebOS.BackupRestore()` → `public static function Restore(array $Payload, array $Context)`
  - `WebOS.BackupVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.BackupList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.BackupDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.BackupPolicySet()` → `public static function PolicySet(array $Payload, array $Context)`
  - `WebOS.BackupScheduleSet()` → `public static function ScheduleSet(array $Payload, array $Context)`
  - `WebOS.BackupExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Backup()` |
| Create | `public static function Create(array $Payload, array $Context)` | `WebOS.BackupCreate()` |
| Restore | `public static function Restore(array $Payload, array $Context)` | `WebOS.BackupRestore()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.BackupVerify()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.BackupList()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.BackupDelete()` |
| PolicySet | `public static function PolicySet(array $Payload, array $Context)` | `WebOS.BackupPolicySet()` |
| ScheduleSet | `public static function ScheduleSet(array $Payload, array $Context)` | `WebOS.BackupScheduleSet()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.BackupExport()` |
### <span id="webos-balance"></span>WebOS.Balance
- **Purpose**: Balance state management (Available / Locked / Pending / Total) Ledger / Transaction driven balance updates Multi-Tenant + Multi-App compatible High-concurrency safe (lock + version aware)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Balance.php](WebOS/PHP.26.00.00/Engine/WebOS.Balance.php)
- **Class**: `WebOS_Balance`
- **Facade Entry Points**:
  - `WebOS.Balance()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.BalanceGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.BalanceApply()` → `public static function Apply(array $Payload, array $Context)`
  - `WebOS.BalanceHold()` → `public static function Hold(array $Payload, array $Context)`
  - `WebOS.BalanceRelease()` → `public static function Release(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Balance()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.BalanceGet()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `WebOS.BalanceApply()` |
| Hold | `public static function Hold(array $Payload, array $Context)` | `WebOS.BalanceHold()` |
| Release | `public static function Release(array $Payload, array $Context)` | `WebOS.BalanceRelease()` |
### <span id="webos-billing"></span>WebOS.Billing
- **Purpose**: Unified Billing Engine for WebOS Multi-App + Multi-Tenant + Multi-Brand billing brain Handles Products, Prices, Invoices, Subscriptions coordination
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Billing.php](WebOS/PHP.26.00.00/Engine/WebOS.Billing.php)
- **Class**: `WebOS_Billing`
- **Facade Entry Points**:
  - `WebOS.Billing()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.BillingProductUpsert()` → `public static function ProductUpsert(array $Payload, array $Context)`
  - `WebOS.BillingInvoiceDraft()` → `public static function InvoiceDraft(array $Payload, array $Context)`
  - `WebOS.BillingInvoiceAddItem()` → `public static function InvoiceAddItem(array $Payload, array $Context)`
  - `WebOS.BillingInvoiceFinalize()` → `public static function InvoiceFinalize(array $Payload, array $Context)`
  - `WebOS.BillingInvoicePay()` → `public static function InvoicePay(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Billing()` |
| ProductUpsert | `public static function ProductUpsert(array $Payload, array $Context)` | `WebOS.BillingProductUpsert()` |
| InvoiceDraft | `public static function InvoiceDraft(array $Payload, array $Context)` | `WebOS.BillingInvoiceDraft()` |
| InvoiceAddItem | `public static function InvoiceAddItem(array $Payload, array $Context)` | `WebOS.BillingInvoiceAddItem()` |
| InvoiceFinalize | `public static function InvoiceFinalize(array $Payload, array $Context)` | `WebOS.BillingInvoiceFinalize()` |
| InvoicePay | `public static function InvoicePay(array $Payload, array $Context)` | `WebOS.BillingInvoicePay()` |
### <span id="webos-blockchain"></span>WebOS.Blockchain
- **Purpose**: Blockchain abstraction engine for WebOS Chain-agnostic (EVM, Tron, Solana, Internal Ledger) Secure orchestration of wallets, balances, transfers & indexing High-performance, async-ready, auditable backend OS layer
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Blockchain.php](WebOS/PHP.26.00.00/Engine/WebOS.Blockchain.php)
- **Class**: `WebOS_Blockchain`
- **Facade Entry Points**:
  - `WebOS.Blockchain()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.BlockchainChains()` → `public static function Chains(array $Payload, array $Context)`
  - `WebOS.BlockchainBalance()` → `public static function Balance(array $Payload, array $Context)`
  - `WebOS.BlockchainTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `WebOS.BlockchainTxGet()` → `public static function TxGet(array $Payload, array $Context)`
  - `WebOS.BlockchainIndexRun()` → `public static function IndexRun(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Blockchain()` |
| Chains | `public static function Chains(array $Payload, array $Context)` | `WebOS.BlockchainChains()` |
| Balance | `public static function Balance(array $Payload, array $Context)` | `WebOS.BlockchainBalance()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `WebOS.BlockchainTransfer()` |
| TxGet | `public static function TxGet(array $Payload, array $Context)` | `WebOS.BlockchainTxGet()` |
| IndexRun | `public static function IndexRun(array $Payload, array $Context)` | `WebOS.BlockchainIndexRun()` |
### <span id="webos-boot"></span>WebOS.Boot
- **Purpose**: Initialize complete Aliens ecosystem for each request Highlights : Normalizes PHP environment (timezone, encoding, limits) Uses Aliens['WebOS']['version'] + ['path'] as single source of truth Prepares unified Aliens container for Paths / Request / System Loads Config + Manifest with merge strategy Detects Tenant + App + Brand (Multi-App + Multi-Tenant ready) Initializes core Engines (Log, Cache, Database, Session) Measures Boot time (ms) for performance monitoring Dependencies: Aliens['WebOS']['path'] / Aliens['WebOS']['version'] (pre-filled before Boot) d($Msg) / x($Msg,$Type,$Unit) for logging (optional but recommended) WebOS::Log(), WebOS::Cache(), WebOS::Database(), WebOS::Session() engines Style      : AliensStyle (PascalCase names, clean sections, echo-logging, no jugaad) Hinglish allowed in comments, no Hindi script ============================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Boot.php](WebOS/PHP.26.00.00/Engine/WebOS.Boot.php)
- **Class**: `WebOS_Boot`
- **Facade Entry Points**:
  - `WebOS.Boot()` → `public static function Main(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Boot()` |
### <span id="webos-botdetect"></span>WebOS.BotDetect
- **Purpose**: Enterprise-grade bot detection + abuse scoring for all WebOS entry points. Works in Multi-App + Multi-Tenant environment using Context isolation. Returns deterministic Decision object: Allow + Action + RiskScore + Reasons + TTL + Key + Meta Designed for High-Performance: cache-first counters, minimal parsing.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.BotDetect.php](WebOS/PHP.26.00.00/Engine/WebOS.BotDetect.php)
- **Class**: `WebOS_BotDetect`
- **Facade Entry Points**:
  - `WebOS.BotDetect()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.BotDetectAssess()` → `public static function Assess(array $Payload = [], array $Context = [])`
  - `WebOS.BotDetectSignal()` → `public static function Signal(array $Payload = [], array $Context = [])`
  - `WebOS.BotDetectBlock()` → `public static function Block(array $Payload = [], array $Context = [])`
  - `WebOS.BotDetectAllowList()` → `public static function AllowList(array $Payload = [], array $Context = [])`
  - `WebOS.BotDetectStats()` → `public static function Stats(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.BotDetect()` |
| Assess | `public static function Assess(array $Payload = [], array $Context = [])` | `WebOS.BotDetectAssess()` |
| Signal | `public static function Signal(array $Payload = [], array $Context = [])` | `WebOS.BotDetectSignal()` |
| Block | `public static function Block(array $Payload = [], array $Context = [])` | `WebOS.BotDetectBlock()` |
| AllowList | `public static function AllowList(array $Payload = [], array $Context = [])` | `WebOS.BotDetectAllowList()` |
| Stats | `public static function Stats(array $Payload = [], array $Context = [])` | `WebOS.BotDetectStats()` |
### <span id="webos-brand"></span>WebOS.Brand
- **Purpose**: Brand resolution layer for WebOS Multi-App + Multi-Tenant + Multi-Domain support White-label / Enterprise branding engine
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Brand.php](WebOS/PHP.26.00.00/Engine/WebOS.Brand.php)
- **Class**: `WebOS_Brand`
- **Facade Entry Points**:
  - `WebOS.Brand()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.BrandResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.BrandResolveFromDB()` → `public static function ResolveFromDB(array $Args)`
  - `WebOS.BrandGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.BrandGetFromDB()` → `public static function GetFromDB(array $Args)`
  - `WebOS.BrandConfig()` → `public static function Config(array $Payload, array $Context)`
  - `WebOS.BrandBuildConfig()` → `public static function BuildConfig(array $Args)`
  - `WebOS.BrandInvalidateCache()` → `public static function InvalidateCache(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Brand()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.BrandResolve()` |
| ResolveFromDB | `public static function ResolveFromDB(array $Args)` | `WebOS.BrandResolveFromDB()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.BrandGet()` |
| GetFromDB | `public static function GetFromDB(array $Args)` | `WebOS.BrandGetFromDB()` |
| Config | `public static function Config(array $Payload, array $Context)` | `WebOS.BrandConfig()` |
| BuildConfig | `public static function BuildConfig(array $Args)` | `WebOS.BrandBuildConfig()` |
| InvalidateCache | `public static function InvalidateCache(array $Payload, array $Context)` | `WebOS.BrandInvalidateCache()` |
### <span id="webos-cache"></span>WebOS.Cache
- **Purpose**: Enterprise-grade universal caching engine for WebOS 26.x DESIGN      : Mode-Detection, Auto-UID, TTL, Logging, Force-Fresh.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Cache.php](WebOS/PHP.26.00.00/Engine/WebOS.Cache.php)
- **Class**: `WebOS_Cache`
- **Facade Entry Points**:
  - `WebOS.Cache()` → `public static function Main($Payload, $Context)`
  - `WebOS.CacheGet()` → `public static function Get($Payload, $Context)`
  - `WebOS.CacheDelete()` → `public static function Delete($Payload, $Context)`
  - `WebOS.CacheCacheFlushAll()` → `public static function CacheFlushAll()`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.Cache()` |
| Get | `public static function Get($Payload, $Context)` | `WebOS.CacheGet()` |
| Delete | `public static function Delete($Payload, $Context)` | `WebOS.CacheDelete()` |
| CacheFlushAll | `public static function CacheFlushAll()` | `WebOS.CacheCacheFlushAll()` |
### <span id="webos-cart"></span>WebOS.Cart
- **Purpose**: Universal Cart Engine for WebOS Works for ANY type of backend: E-commerce Food / Booking Subscription Services Marketplace Multi-Tenant + Multi-App safe High-Performance OS-level Cart
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Cart.php](WebOS/PHP.26.00.00/Engine/WebOS.Cart.php)
- **Class**: `WebOS_Cart`
- **Facade Entry Points**:
  - `WebOS.Cart()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.CartGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.CartAdd()` → `public static function Add(array $Payload, array $Context)`
  - `WebOS.CartUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.CartRemove()` → `public static function Remove(array $Payload, array $Context)`
  - `WebOS.CartClear()` → `public static function Clear(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Cart()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.CartGet()` |
| Add | `public static function Add(array $Payload, array $Context)` | `WebOS.CartAdd()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.CartUpdate()` |
| Remove | `public static function Remove(array $Payload, array $Context)` | `WebOS.CartRemove()` |
| Clear | `public static function Clear(array $Payload, array $Context)` | `WebOS.CartClear()` |
### <span id="webos-category"></span>WebOS.Category
- **Purpose**: Universal Category / Taxonomy Engine for WebOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph extensions High-performance read patterns with cache + selective invalidation Enterprise governance: validation, policy checks, audit hooks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Category.php](WebOS/PHP.26.00.00/Engine/WebOS.Category.php)
- **Class**: `WebOS_Category`
- **Facade Entry Points**:
  - `WebOS.Category()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.CategoryGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.CategoryList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.CategorySearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.CategoryNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.CategoryUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.CategoryStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.CategoryDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.CategoryTree()` → `public static function Tree(array $Payload, array $Context)`
  - `WebOS.CategoryChildren()` → `public static function Children(array $Payload, array $Context)`
  - `WebOS.CategoryBreadcrumb()` → `public static function Breadcrumb(array $Payload, array $Context)`
  - `WebOS.CategoryMove()` → `public static function Move(array $Payload, array $Context)`
  - `WebOS.CategoryRebuild()` → `public static function Rebuild(array $Payload, array $Context)`
  - `WebOS.CategoryMapAdd()` → `public static function MapAdd(array $Payload, array $Context)`
  - `WebOS.CategoryMapRemove()` → `public static function MapRemove(array $Payload, array $Context)`
  - `WebOS.CategoryMapList()` → `public static function MapList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Category()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.CategoryGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.CategoryList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.CategorySearch()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.CategoryNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.CategoryUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.CategoryStatus()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.CategoryDelete()` |
| Tree | `public static function Tree(array $Payload, array $Context)` | `WebOS.CategoryTree()` |
| Children | `public static function Children(array $Payload, array $Context)` | `WebOS.CategoryChildren()` |
| Breadcrumb | `public static function Breadcrumb(array $Payload, array $Context)` | `WebOS.CategoryBreadcrumb()` |
| Move | `public static function Move(array $Payload, array $Context)` | `WebOS.CategoryMove()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `WebOS.CategoryRebuild()` |
| MapAdd | `public static function MapAdd(array $Payload, array $Context)` | `WebOS.CategoryMapAdd()` |
| MapRemove | `public static function MapRemove(array $Payload, array $Context)` | `WebOS.CategoryMapRemove()` |
| MapList | `public static function MapList(array $Payload, array $Context)` | `WebOS.CategoryMapList()` |
### <span id="webos-certificate"></span>WebOS.Certificate
- **Purpose**: Centralized Certificate Engine for WebOS Issue, Validate, Revoke, Renew digital certificates Multi-App + Multi-Tenant compatible Secure signing, verification and audit tracking
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Certificate.php](WebOS/PHP.26.00.00/Engine/WebOS.Certificate.php)
- **Class**: `WebOS_Certificate`
- **Facade Entry Points**:
  - `WebOS.Certificate()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.CertificateIssue()` → `public static function Issue(array $Payload, array $Context)`
  - `WebOS.CertificateValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.CertificateRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `WebOS.CertificateRenew()` → `public static function Renew(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Certificate()` |
| Issue | `public static function Issue(array $Payload, array $Context)` | `WebOS.CertificateIssue()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.CertificateValidate()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `WebOS.CertificateRevoke()` |
| Renew | `public static function Renew(array $Payload, array $Context)` | `WebOS.CertificateRenew()` |
### <span id="webos-chat"></span>WebOS.Chat
- **Purpose**: Enterprise-grade Chat backbone for any WebOS App (Multi-App + Multi-Tenant) DM / Group / Channel chat spaces Messaging, membership, receipts scaffolding High-performance read paths (cache-ready), write paths (lock-ready) Strict isolation: TenantId + AppId on every query
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Chat.php](WebOS/PHP.26.00.00/Engine/WebOS.Chat.php)
- **Class**: `WebOS_Chat`
- **Facade Entry Points**:
  - `WebOS.Chat()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ChatNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ChatList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.ChatSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.ChatMessages()` → `public static function Messages(array $Payload, array $Context)`
  - `WebOS.ChatMemberAdd()` → `public static function MemberAdd(array $Payload, array $Context)`
  - `WebOS.ChatMemberRemove()` → `public static function MemberRemove(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Chat()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ChatNew()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.ChatList()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.ChatSend()` |
| Messages | `public static function Messages(array $Payload, array $Context)` | `WebOS.ChatMessages()` |
| MemberAdd | `public static function MemberAdd(array $Payload, array $Context)` | `WebOS.ChatMemberAdd()` |
| MemberRemove | `public static function MemberRemove(array $Payload, array $Context)` | `WebOS.ChatMemberRemove()` |
### <span id="webos-class"></span>WebOS.Class
- **Purpose**: Universal Class / Module loader for WebOS Multi-App + Multi-Tenant safe instantiation High-performance registry + reflection cache Dependency-aware lightweight DI Strict AliensGrade security & isolation
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Class.php](WebOS/PHP.26.00.00/Engine/WebOS.Class.php)
- **Class**: `across`
- **Facade Entry Points**:
  - `WebOS.Class()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ClassResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.ClassLoad()` → `public static function Load(array $Payload, array $Context)`
  - `WebOS.ClassGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ClassNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ClassHas()` → `public static function Has(array $Payload, array $Context)`
  - `WebOS.ClassForget()` → `public static function Forget(array $Payload, array $Context)`
  - `WebOS.ClassFlush()` → `public static function Flush(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Class()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.ClassResolve()` |
| Load | `public static function Load(array $Payload, array $Context)` | `WebOS.ClassLoad()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ClassGet()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ClassNew()` |
| Has | `public static function Has(array $Payload, array $Context)` | `WebOS.ClassHas()` |
| Forget | `public static function Forget(array $Payload, array $Context)` | `WebOS.ClassForget()` |
| Flush | `public static function Flush(array $Payload, array $Context)` | `WebOS.ClassFlush()` |
### <span id="webos-cluster"></span>WebOS.Cluster
- **Purpose**: Provide cluster-level intelligence to WebOS Enable multi-node, multi-region, multi-tenant backend execution Act as the backbone for high-availability, failover, and routing decisions
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Cluster.php](WebOS/PHP.26.00.00/Engine/WebOS.Cluster.php)
- **Class**: `WebOS_Cluster`
- **Facade Entry Points**:
  - `WebOS.Cluster()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ClusterTopologyGet()` → `public static function TopologyGet(array $Payload, array $Context)`
  - `WebOS.ClusterNodeHeartbeat()` → `public static function NodeHeartbeat(array $Payload, array $Context)`
  - `WebOS.ClusterRouteResolve()` → `public static function RouteResolve(array $Payload, array $Context)`
  - `WebOS.ClusterFailover()` → `public static function Failover(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Cluster()` |
| TopologyGet | `public static function TopologyGet(array $Payload, array $Context)` | `WebOS.ClusterTopologyGet()` |
| NodeHeartbeat | `public static function NodeHeartbeat(array $Payload, array $Context)` | `WebOS.ClusterNodeHeartbeat()` |
| RouteResolve | `public static function RouteResolve(array $Payload, array $Context)` | `WebOS.ClusterRouteResolve()` |
| Failover | `public static function Failover(array $Payload, array $Context)` | `WebOS.ClusterFailover()` |
### <span id="webos-comment"></span>WebOS.Comment
- **Purpose**: Universal Comment Engine for WebOS Multi-App + Multi-Tenant + Enterprise Grade Threaded comments with moderation, reactions, audit
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Comment.php](WebOS/PHP.26.00.00/Engine/WebOS.Comment.php)
- **Class**: `WebOS_Comment`
- **Facade Entry Points**:
  - `WebOS.Comment()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.CommentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.CommentList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.CommentDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Comment()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.CommentNew()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.CommentList()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.CommentDelete()` |
### <span id="webos-compliance"></span>WebOS.Compliance
- **Purpose**: Central Compliance & Governance Engine for WebOS Enforces data policies, consent, retention, audit Multi-App + Multi-Tenant + Region aware
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Compliance.php](WebOS/PHP.26.00.00/Engine/WebOS.Compliance.php)
- **Class**: `WebOS_Compliance`
- **Facade Entry Points**:
  - `WebOS.Compliance()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ComplianceCheck()` → `public static function Check(array $Payload, array $Context)`
  - `WebOS.ComplianceGate()` → `public static function Gate(array $Payload, array $Context)`
  - `WebOS.ComplianceConsentGet()` → `public static function ConsentGet(array $Payload, array $Context)`
  - `WebOS.ComplianceConsentSet()` → `public static function ConsentSet(array $Payload, array $Context)`
  - `WebOS.ComplianceAudit()` → `public static function Audit(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Compliance()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.ComplianceCheck()` |
| Gate | `public static function Gate(array $Payload, array $Context)` | `WebOS.ComplianceGate()` |
| ConsentGet | `public static function ConsentGet(array $Payload, array $Context)` | `WebOS.ComplianceConsentGet()` |
| ConsentSet | `public static function ConsentSet(array $Payload, array $Context)` | `WebOS.ComplianceConsentSet()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `WebOS.ComplianceAudit()` |
### <span id="webos-compliancehr"></span>WebOS.ComplianceHR
- **Purpose**: HR Compliance Operating Layer (Policy → Rules → Decision → Audit) Multi-App + Multi-Tenant compatible via Context (app/brand/ecosystem/user) High-Performance: Policy caching + rule indexing + fast evaluation path ---------------------------------------------------------------------------- Dependencies (Engines via WebOS.Loader): WebOS::Security*   (sanitize + validation) WebOS::Auth*       (actor/session validation) WebOS::Role*       (role checks) WebOS::Permission* (fine-grained permission checks) WebOS::Cache*      (policy cache) WebOS::Database*   (persistence) WebOS::Audit*      (tamper-evident audit trail) WebOS::Task*       (approval/remediation tasks) WebOS::EventBus*   (events publish) WebOS::Log*        (structured logs) ---------------------------------------------------------------------------- AliensGrade Guarantees: Deny-by-default (missing policy/rule/unit → safe decision + auditable reason) No business logic in Boot files; Engine is pure backend power Deterministic outputs: Decision Contract is stable across apps/tenants ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.ComplianceHR.php](WebOS/PHP.26.00.00/Engine/WebOS.ComplianceHR.php)
- **Class**: `WebOS_ComplianceHR`
- **Facade Entry Points**:
  - `WebOS.ComplianceHR()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.ComplianceHREvaluate()` → `public static function Evaluate($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRPolicyList()` → `public static function PolicyList($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRPolicyGet()` → `public static function PolicyGet($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRPolicyNew()` → `public static function PolicyNew($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRPolicyUpdate()` → `public static function PolicyUpdate($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRPolicyActivate()` → `public static function PolicyActivate($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRPolicyRollback()` → `public static function PolicyRollback($Payload = [], $Context = [])`
  - `WebOS.ComplianceHREvidenceNew()` → `public static function EvidenceNew($Payload = [], $Context = [])`
  - `WebOS.ComplianceHREvidenceVerify()` → `public static function EvidenceVerify($Payload = [], $Context = [])`
  - `WebOS.ComplianceHREvidenceLink()` → `public static function EvidenceLink($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRViolationNew()` → `public static function ViolationNew($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRViolationAssign()` → `public static function ViolationAssign($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRViolationClose()` → `public static function ViolationClose($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRViolationEscalate()` → `public static function ViolationEscalate($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRReportGenerate()` → `public static function ReportGenerate($Payload = [], $Context = [])`
  - `WebOS.ComplianceHRReportExport()` → `public static function ReportExport($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.ComplianceHR()` |
| Evaluate | `public static function Evaluate($Payload = [], $Context = [])` | `WebOS.ComplianceHREvaluate()` |
| PolicyList | `public static function PolicyList($Payload = [], $Context = [])` | `WebOS.ComplianceHRPolicyList()` |
| PolicyGet | `public static function PolicyGet($Payload = [], $Context = [])` | `WebOS.ComplianceHRPolicyGet()` |
| PolicyNew | `public static function PolicyNew($Payload = [], $Context = [])` | `WebOS.ComplianceHRPolicyNew()` |
| PolicyUpdate | `public static function PolicyUpdate($Payload = [], $Context = [])` | `WebOS.ComplianceHRPolicyUpdate()` |
| PolicyActivate | `public static function PolicyActivate($Payload = [], $Context = [])` | `WebOS.ComplianceHRPolicyActivate()` |
| PolicyRollback | `public static function PolicyRollback($Payload = [], $Context = [])` | `WebOS.ComplianceHRPolicyRollback()` |
| EvidenceNew | `public static function EvidenceNew($Payload = [], $Context = [])` | `WebOS.ComplianceHREvidenceNew()` |
| EvidenceVerify | `public static function EvidenceVerify($Payload = [], $Context = [])` | `WebOS.ComplianceHREvidenceVerify()` |
| EvidenceLink | `public static function EvidenceLink($Payload = [], $Context = [])` | `WebOS.ComplianceHREvidenceLink()` |
| ViolationNew | `public static function ViolationNew($Payload = [], $Context = [])` | `WebOS.ComplianceHRViolationNew()` |
| ViolationAssign | `public static function ViolationAssign($Payload = [], $Context = [])` | `WebOS.ComplianceHRViolationAssign()` |
| ViolationClose | `public static function ViolationClose($Payload = [], $Context = [])` | `WebOS.ComplianceHRViolationClose()` |
| ViolationEscalate | `public static function ViolationEscalate($Payload = [], $Context = [])` | `WebOS.ComplianceHRViolationEscalate()` |
| ReportGenerate | `public static function ReportGenerate($Payload = [], $Context = [])` | `WebOS.ComplianceHRReportGenerate()` |
| ReportExport | `public static function ReportExport($Payload = [], $Context = [])` | `WebOS.ComplianceHRReportExport()` |
### <span id="webos-config"></span>WebOS.Config
- **Purpose**: Centralized configuration manager for WebOS Multi-App, Multi-Tenant config storage In-Memory + File + Database hybrid config system Auto-load configs from: App → Brand → SDK → WebOS Core Supports override chain & fallback chain
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Config.php](WebOS/PHP.26.00.00/Engine/WebOS.Config.php)
- **Class**: `WebOS_Config`
- **Facade Entry Points**:
  - `WebOS.Config()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ConfigGet()` → `public static function Get(string $App = null, string $Key = null, $Default = null)`
  - `WebOS.ConfigSet()` → `public static function Set(string $App = null, string $Key = null, $Value = null)`
  - `WebOS.ConfigDelete()` → `public static function Delete(string $App = null, string $Key = null)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Config()` |
| Get | `public static function Get(string $App = null, string $Key = null, $Default = null)` | `WebOS.ConfigGet()` |
| Set | `public static function Set(string $App = null, string $Key = null, $Value = null)` | `WebOS.ConfigSet()` |
| Delete | `public static function Delete(string $App = null, string $Key = null)` | `WebOS.ConfigDelete()` |
### <span id="webos-constitution"></span>WebOS.Constitution
- **Purpose**: WebOS ka Supreme Governance Layer (Policy + Rules + Enforcement) Multi-App + Multi-Tenant + Enterprise Grade control plane Unit Execution (API/Cron/Webhook/Asset/Component/Template) ko rules se govern karna Security, Compliance, Data Governance, Cost/Performance Governor
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Constitution.php](WebOS/PHP.26.00.00/Engine/WebOS.Constitution.php)
- **Class**: `WebOS_Constitution`
- **Facade Entry Points**:
  - `WebOS.Constitution()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionLoad()` → `public static function Load(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionEvaluate()` → `public static function Evaluate(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionEnforce()` → `public static function Enforce(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionCacheWarm()` → `public static function CacheWarm(array $Payload = [], array $Context = [])`
  - `WebOS.ConstitutionAudit()` → `public static function Audit(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Constitution()` |
| Load | `public static function Load(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionLoad()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionResolve()` |
| Evaluate | `public static function Evaluate(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionEvaluate()` |
| Enforce | `public static function Enforce(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionEnforce()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionExplain()` |
| CacheWarm | `public static function CacheWarm(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionCacheWarm()` |
| Audit | `public static function Audit(array $Payload = [], array $Context = [])` | `WebOS.ConstitutionAudit()` |
### <span id="webos-constraint"></span>WebOS.Constraint
- **Purpose**: Universal Constraint Enforcement Engine for WebOS Enforce hard/soft/observe rules across API, Data, Security, Business layers Multi-App + Multi-Tenant + Override-safe Deterministic, Auditable, High-Performance
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Constraint.php](WebOS/PHP.26.00.00/Engine/WebOS.Constraint.php)
- **Class**: `WebOS_Constraint`
- **Facade Entry Points**:
  - `WebOS.Constraint()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ConstraintCheck()` → `public static function Check(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Constraint()` |
| Check | `public static function Check(array $Payload = [], array $Context = [])` | `WebOS.ConstraintCheck()` |
### <span id="webos-container"></span>WebOS.Container
- **Purpose**: Central runtime container for WebOS Context holder (Tenant, App, Brand, User, Env) Service registry with lazy-loading Multi-App + Multi-Tenant isolation Deterministic, secure, high-performance backend core
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Container.php](WebOS/PHP.26.00.00/Engine/WebOS.Container.php)
- **Class**: `WebOS_Container`
- **Facade Entry Points**:
  - `WebOS.Container()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerInit()` → `public static function Init(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerBind()` → `public static function Bind(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerHas()` → `public static function Has(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerSeal()` → `public static function Seal(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerContext()` → `public static function Context(array $Payload = [], array $Context = [])`
  - `WebOS.ContainerStats()` → `public static function Stats(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Container()` |
| Init | `public static function Init(array $Payload = [], array $Context = [])` | `WebOS.ContainerInit()` |
| Bind | `public static function Bind(array $Payload = [], array $Context = [])` | `WebOS.ContainerBind()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `WebOS.ContainerSet()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.ContainerGet()` |
| Has | `public static function Has(array $Payload = [], array $Context = [])` | `WebOS.ContainerHas()` |
| Seal | `public static function Seal(array $Payload = [], array $Context = [])` | `WebOS.ContainerSeal()` |
| Context | `public static function Context(array $Payload = [], array $Context = [])` | `WebOS.ContainerContext()` |
| Stats | `public static function Stats(array $Payload = [], array $Context = [])` | `WebOS.ContainerStats()` |
### <span id="webos-content"></span>WebOS.Content
- **Purpose**: Universal Content Operating Engine for WebOS Supports ANY website / webapp / SaaS / app backend Multi-Tenant + Multi-App + Multi-Brand aware Draft → Publish → Version → Cache → Search ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Content.php](WebOS/PHP.26.00.00/Engine/WebOS.Content.php)
- **Class**: `WebOS_Content`
- **Facade Entry Points**:
  - `WebOS.Content()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ContentGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ContentList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.ContentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ContentUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.ContentPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `WebOS.ContentDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Content()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ContentGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.ContentList()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ContentNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.ContentUpdate()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.ContentPublish()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.ContentDelete()` |
### <span id="webos-context"></span>WebOS.Context
- **Purpose**: Centralized Context Manager for entire WebOS lifecycle Replaces scattered globals ($i, $app, $brand, $ecosystem) Provides unified API: ContextGet, ContextSet, ContextHas, ContextPick Supports Multi-App + Multi-Tenant isolation Lazy-loaded nodes (user/app/brand/tenant/runtime) Snapshot + Restore for Worker / Queue Jobs Scoped Overrides for internal tasks & temporary impersonation ---------------------------------------------------------------------------- Dependencies: WebOS.EngineRouter (Engine Resolver Layer) WebOS.Auth (Lazy user loader) WebOS.App   (App resolver) WebOS.Brand (Brand resolver) WebOS.Tenant (Ecosystem / tenant resolver) WebOS.Request / WebOS.Response (Optional integrations) ---------------------------------------------------------------------------- AliensStyle Rules: PascalCase for everything CleanCode structure with Section Headers No Hindi script (Hinglish allowed) Engine must be lightweight, powerful, override-friendly ---------------------------------------------------------------------------- Highlights: Multi-layer context tree (request/user/app/brand/tenant/env/runtime/...) Dot-notation access: ContextGet("user.id") Full in-memory caching (per request) Lockable context (AliensGrade: production mode safety) Can be extended by App/Brand/SDK overrides ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Context.php](WebOS/PHP.26.00.00/Engine/WebOS.Context.php)
- **Class**: `WebOS_Context`
- **Facade Entry Points**:
  - `WebOS.Context()` → `public static function Main(array $Payload = [], array $Context = []): mixed`
  - `WebOS.ContextGet()` → `public static function Get(array $Payload = [], array $Context = []): mixed`
  - `WebOS.ContextSet()` → `public static function Set(array $Payload = [], array $Context = []): bool`
  - `WebOS.ContextHas()` → `public static function Has(array $Payload = [], array $Context = []): bool`
  - `WebOS.ContextPick()` → `public static function Pick(array $Payload = [], array $Context = []): array`
  - `WebOS.ContextScope()` → `public static function Scope(array $Payload = [], array $Context = []): mixed`
  - `WebOS.ContextSnapshot()` → `public static function Snapshot(array $Payload = [], array $Context = []): array`
  - `WebOS.ContextRestore()` → `public static function Restore(array $Payload = [], array $Context = []): bool`
  - `WebOS.ContextLock()` → `public static function Lock(): void`
  - `WebOS.ContextUnlock()` → `public static function Unlock(): void`
  - `WebOS.ContextContextGet()` → `public static function ContextGet(string $Key): mixed`
  - `WebOS.ContextContextSet()` → `public static function ContextSet(array $Data): bool`
  - `WebOS.ContextContextHas()` → `public static function ContextHas(string $Key): bool`
  - `WebOS.ContextContextPick()` → `public static function ContextPick(array $Keys): array`
  - `WebOS.ContextContextSnapshot()` → `public static function ContextSnapshot(): array`
  - `WebOS.ContextContextRestore()` → `public static function ContextRestore(array $Tree): bool`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = []): mixed` | `WebOS.Context()` |
| Get | `public static function Get(array $Payload = [], array $Context = []): mixed` | `WebOS.ContextGet()` |
| Set | `public static function Set(array $Payload = [], array $Context = []): bool` | `WebOS.ContextSet()` |
| Has | `public static function Has(array $Payload = [], array $Context = []): bool` | `WebOS.ContextHas()` |
| Pick | `public static function Pick(array $Payload = [], array $Context = []): array` | `WebOS.ContextPick()` |
| Scope | `public static function Scope(array $Payload = [], array $Context = []): mixed` | `WebOS.ContextScope()` |
| Snapshot | `public static function Snapshot(array $Payload = [], array $Context = []): array` | `WebOS.ContextSnapshot()` |
| Restore | `public static function Restore(array $Payload = [], array $Context = []): bool` | `WebOS.ContextRestore()` |
| Lock | `public static function Lock(): void` | `WebOS.ContextLock()` |
| Unlock | `public static function Unlock(): void` | `WebOS.ContextUnlock()` |
| ContextGet | `public static function ContextGet(string $Key): mixed` | `WebOS.ContextContextGet()` |
| ContextSet | `public static function ContextSet(array $Data): bool` | `WebOS.ContextContextSet()` |
| ContextHas | `public static function ContextHas(string $Key): bool` | `WebOS.ContextContextHas()` |
| ContextPick | `public static function ContextPick(array $Keys): array` | `WebOS.ContextContextPick()` |
| ContextSnapshot | `public static function ContextSnapshot(): array` | `WebOS.ContextContextSnapshot()` |
| ContextRestore | `public static function ContextRestore(array $Tree): bool` | `WebOS.ContextContextRestore()` |
### <span id="webos-core"></span>WebOS.Core
- **Purpose**: WebOS ka "Brain" / Central Kernel Multi-App + Multi-Tenant core registry Request Context (User, App, Brand, Tenant, Request) System Context (Version, Path, Env, Execution) Config Loader (WebOS + App + Brand + Env) Memory Store (super-fast per request) Engine Registry (Cache, Database, HTTP...) Hooks (Before/After/Custom events) Trace System (Aliens Insight)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Core.php](WebOS/PHP.26.00.00/Engine/WebOS.Core.php)
- **Class**: `WebOS_Core`
- **Facade Entry Points**:
  - `WebOS.CoreInitSystem()` → `public static function InitSystem(): void`
  - `WebOS.CoreInitGlobals()` → `public static function InitGlobals(): void`
  - `WebOS.CoreSet()` → `public static function Set(string $Key, mixed $Val): void`
  - `WebOS.CoreGet()` → `public static function Get(string $Key, mixed $Default = null): mixed`
  - `WebOS.CoreSetContext()` → `public static function SetContext(string $Key, mixed $Val): void`
  - `WebOS.CoreContext()` → `public static function Context(string $Key, mixed $Default = null): mixed`
  - `WebOS.CoreApp()` → `public static function App(string $Key=null, mixed $Default=null): mixed`
  - `WebOS.CoreBrand()` → `public static function Brand(string $Key=null, mixed $Default=null): mixed`
  - `WebOS.CoreUser()` → `public static function User(string $Key=null, mixed $Default=null): mixed`
  - `WebOS.CoreTenant()` → `public static function Tenant(string $Key=null, mixed $Default=null): mixed`
  - `WebOS.CoreRequest()` → `public static function Request(string $Key=null, mixed $Default=null): mixed`
  - `WebOS.CoreSystem()` → `public static function System(string $Key=null, mixed $Default=null): mixed`
  - `WebOS.CoreMemSet()` → `public static function MemSet(string $Key, mixed $Val): void`
  - `WebOS.CoreMemGet()` → `public static function MemGet(string $Key, mixed $Default=null): mixed`
  - `WebOS.CoreSetConfig()` → `public static function SetConfig(string $Key, mixed $Val): void`
  - `WebOS.CoreConfig()` → `public static function Config(string $Key, mixed $Default=null): mixed`
  - `WebOS.CoreEngine()` → `public static function Engine(string $Name): ?string`
  - `WebOS.CoreRegisterEngine()` → `public static function RegisterEngine(string $Name, string $Class): void`
  - `WebOS.CoreEngines()` → `public static function Engines(): array`
  - `WebOS.CoreTrace()` → `public static function Trace(string $Msg): void`
  - `WebOS.CoreGetTrace()` → `public static function GetTrace(): array`
  - `WebOS.CoreHookAdd()` → `public static function HookAdd(string $Event, callable $Fn): void`
  - `WebOS.CoreHookRun()` → `public static function HookRun(string $Event, mixed &$Payload=null): void`

| Method | Signature | Facade |
| --- | --- | --- |
| InitSystem | `public static function InitSystem(): void` | `WebOS.CoreInitSystem()` |
| InitGlobals | `public static function InitGlobals(): void` | `WebOS.CoreInitGlobals()` |
| Set | `public static function Set(string $Key, mixed $Val): void` | `WebOS.CoreSet()` |
| Get | `public static function Get(string $Key, mixed $Default = null): mixed` | `WebOS.CoreGet()` |
| SetContext | `public static function SetContext(string $Key, mixed $Val): void` | `WebOS.CoreSetContext()` |
| Context | `public static function Context(string $Key, mixed $Default = null): mixed` | `WebOS.CoreContext()` |
| App | `public static function App(string $Key=null, mixed $Default=null): mixed` | `WebOS.CoreApp()` |
| Brand | `public static function Brand(string $Key=null, mixed $Default=null): mixed` | `WebOS.CoreBrand()` |
| User | `public static function User(string $Key=null, mixed $Default=null): mixed` | `WebOS.CoreUser()` |
| Tenant | `public static function Tenant(string $Key=null, mixed $Default=null): mixed` | `WebOS.CoreTenant()` |
| Request | `public static function Request(string $Key=null, mixed $Default=null): mixed` | `WebOS.CoreRequest()` |
| System | `public static function System(string $Key=null, mixed $Default=null): mixed` | `WebOS.CoreSystem()` |
| MemSet | `public static function MemSet(string $Key, mixed $Val): void` | `WebOS.CoreMemSet()` |
| MemGet | `public static function MemGet(string $Key, mixed $Default=null): mixed` | `WebOS.CoreMemGet()` |
| SetConfig | `public static function SetConfig(string $Key, mixed $Val): void` | `WebOS.CoreSetConfig()` |
| Config | `public static function Config(string $Key, mixed $Default=null): mixed` | `WebOS.CoreConfig()` |
| Engine | `public static function Engine(string $Name): ?string` | `WebOS.CoreEngine()` |
| RegisterEngine | `public static function RegisterEngine(string $Name, string $Class): void` | `WebOS.CoreRegisterEngine()` |
| Engines | `public static function Engines(): array` | `WebOS.CoreEngines()` |
| Trace | `public static function Trace(string $Msg): void` | `WebOS.CoreTrace()` |
| GetTrace | `public static function GetTrace(): array` | `WebOS.CoreGetTrace()` |
| HookAdd | `public static function HookAdd(string $Event, callable $Fn): void` | `WebOS.CoreHookAdd()` |
| HookRun | `public static function HookRun(string $Event, mixed &$Payload=null): void` | `WebOS.CoreHookRun()` |
### <span id="webos-course"></span>WebOS.Course
- **Purpose**: Enterprise-grade Course backend engine (Multi-App + Multi-Tenant) Clean, scoped CRUD + enrollment + progress Loader-only usage via WebOS::CourseAction()
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Course.php](WebOS/PHP.26.00.00/Engine/WebOS.Course.php)
- **Class**: `WebOS_Course`
- **Facade Entry Points**:
  - `WebOS.Course()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.CourseNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `WebOS.CourseGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.CourseList()` → `public static function List(array $Payload = [], array $Context = [])`
  - `WebOS.CourseUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `WebOS.CourseStatus()` → `public static function Status(array $Payload = [], array $Context = [])`
  - `WebOS.CourseDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `WebOS.CourseEnroll()` → `public static function Enroll(array $Payload = [], array $Context = [])`
  - `WebOS.CourseProgressGet()` → `public static function ProgressGet(array $Payload = [], array $Context = [])`
  - `WebOS.CourseProgressSet()` → `public static function ProgressSet(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Course()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `WebOS.CourseNew()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.CourseGet()` |
| List | `public static function List(array $Payload = [], array $Context = [])` | `WebOS.CourseList()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `WebOS.CourseUpdate()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `WebOS.CourseStatus()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `WebOS.CourseDelete()` |
| Enroll | `public static function Enroll(array $Payload = [], array $Context = [])` | `WebOS.CourseEnroll()` |
| ProgressGet | `public static function ProgressGet(array $Payload = [], array $Context = [])` | `WebOS.CourseProgressGet()` |
| ProgressSet | `public static function ProgressSet(array $Payload = [], array $Context = [])` | `WebOS.CourseProgressSet()` |
### <span id="webos-cron"></span>WebOS.Cron
- **Purpose**: Enterprise-grade Cron / Scheduler Engine for WebOS Multi-App + Multi-Tenant + Cluster-Safe job orchestration Central brain for all scheduled/background jobs
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Cron.php](WebOS/PHP.26.00.00/Engine/WebOS.Cron.php)
- **Class**: `WebOS_Cron`
- **Facade Entry Points**:
  - `WebOS.Cron()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.CronTick()` → `public static function Tick(array $Payload = [], array $Context = [])`
  - `WebOS.CronEnqueue()` → `public static function Enqueue(array $Payload = [], array $Context = [])`
  - `WebOS.CronClaim()` → `public static function Claim(array $Payload = [], array $Context = [])`
  - `WebOS.CronRun()` → `public static function Run(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Cron()` |
| Tick | `public static function Tick(array $Payload = [], array $Context = [])` | `WebOS.CronTick()` |
| Enqueue | `public static function Enqueue(array $Payload = [], array $Context = [])` | `WebOS.CronEnqueue()` |
| Claim | `public static function Claim(array $Payload = [], array $Context = [])` | `WebOS.CronClaim()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `WebOS.CronRun()` |
### <span id="webos-crypto"></span>WebOS.Crypto
- **Purpose**: Enterprise-grade Crypto Engine for WebOS Multi-App, Multi-Tenant, Chain-Agnostic crypto layer Wallet, Balance, Transfer, Ledger (Phase-1)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Crypto.php](WebOS/PHP.26.00.00/Engine/WebOS.Crypto.php)
- **Class**: `WebOS_Crypto`
- **Facade Entry Points**:
  - `WebOS.Crypto()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.CryptoWalletNew()` → `public static function WalletNew(array $Payload, array $Context)`
  - `WebOS.CryptoBalanceGet()` → `public static function BalanceGet(array $Payload, array $Context)`
  - `WebOS.CryptoTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `WebOS.CryptoLedgerList()` → `public static function LedgerList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Crypto()` |
| WalletNew | `public static function WalletNew(array $Payload, array $Context)` | `WebOS.CryptoWalletNew()` |
| BalanceGet | `public static function BalanceGet(array $Payload, array $Context)` | `WebOS.CryptoBalanceGet()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `WebOS.CryptoTransfer()` |
| LedgerList | `public static function LedgerList(array $Payload, array $Context)` | `WebOS.CryptoLedgerList()` |
### <span id="webos-csrf"></span>WebOS.CSRF
- **Purpose**: Enterprise-grade CSRF protection for browser + session(cookie) requests Multi-App + Multi-Tenant isolation by scope (Tenant/App/Brand/User/Session) Supports Header + Body token modes (cookie mode optional) Optional Origin/Referer validation Optional Replay protection for high-risk routes (nonce store) ---------------------------------------------------------------------------- DESIGN RULES (WebOS.EngineRouter v7.0): Called only via WebOS::CSRF* static calls Main($Payload,$Context) is mandatory fallback No includes, no boot logic, no global
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.CSRF.php](WebOS/PHP.26.00.00/Engine/WebOS.CSRF.php)
- **Class**: `instantiation`
- **Facade Entry Points**:
  - `WebOS.CSRF()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.CSRFPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `WebOS.CSRFGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.CSRFNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `WebOS.CSRFRotate()` → `public static function Rotate(array $Payload = [], array $Context = [])`
  - `WebOS.CSRFRequire()` → `public static function Require(array $Payload = [], array $Context = [])`
  - `WebOS.CSRFValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.CSRF()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `WebOS.CSRFPolicy()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.CSRFGet()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `WebOS.CSRFNew()` |
| Rotate | `public static function Rotate(array $Payload = [], array $Context = [])` | `WebOS.CSRFRotate()` |
| Require | `public static function Require(array $Payload = [], array $Context = [])` | `WebOS.CSRFRequire()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `WebOS.CSRFValidate()` |
### <span id="webos-currency"></span>WebOS.Currency
- **Purpose**: Enterprise-grade Currency + Money + FX conversion engine Multi-App + Multi-Tenant defaults, policies, and allowed currencies High-precision decimal safety (no float math), rounding policies Fast catalog/config/rate caching with scoped keys Audit-ready outputs + deterministic error contracts
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Currency.php](WebOS/PHP.26.00.00/Engine/WebOS.Currency.php)
- **Class**: `WebOS_Currency`
- **Facade Entry Points**:
  - `WebOS.Currency()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.CurrencyList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.CurrencyGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.CurrencyValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.CurrencyNormalize()` → `public static function Normalize(array $Payload, array $Context)`
  - `WebOS.CurrencyFormat()` → `public static function Format(array $Payload, array $Context)`
  - `WebOS.CurrencyDefault()` → `public static function Default(array $Payload, array $Context)`
  - `WebOS.CurrencyAllowed()` → `public static function Allowed(array $Payload, array $Context)`
  - `WebOS.CurrencyRounding()` → `public static function Rounding(array $Payload, array $Context)`
  - `WebOS.CurrencySpread()` → `public static function Spread(array $Payload, array $Context)`
  - `WebOS.CurrencyRateGet()` → `public static function RateGet(array $Payload, array $Context)`
  - `WebOS.CurrencyConvert()` → `public static function Convert(array $Payload, array $Context)`
  - `WebOS.CurrencyRateSet()` → `public static function RateSet(array $Payload, array $Context)`
  - `WebOS.CurrencyRateRefresh()` → `public static function RateRefresh(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Currency()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.CurrencyList()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.CurrencyGet()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.CurrencyValidate()` |
| Normalize | `public static function Normalize(array $Payload, array $Context)` | `WebOS.CurrencyNormalize()` |
| Format | `public static function Format(array $Payload, array $Context)` | `WebOS.CurrencyFormat()` |
| Default | `public static function Default(array $Payload, array $Context)` | `WebOS.CurrencyDefault()` |
| Allowed | `public static function Allowed(array $Payload, array $Context)` | `WebOS.CurrencyAllowed()` |
| Rounding | `public static function Rounding(array $Payload, array $Context)` | `WebOS.CurrencyRounding()` |
| Spread | `public static function Spread(array $Payload, array $Context)` | `WebOS.CurrencySpread()` |
| RateGet | `public static function RateGet(array $Payload, array $Context)` | `WebOS.CurrencyRateGet()` |
| Convert | `public static function Convert(array $Payload, array $Context)` | `WebOS.CurrencyConvert()` |
| RateSet | `public static function RateSet(array $Payload, array $Context)` | `WebOS.CurrencyRateSet()` |
| RateRefresh | `public static function RateRefresh(array $Payload, array $Context)` | `WebOS.CurrencyRateRefresh()` |
### <span id="webos-database"></span>WebOS.Database
- **Purpose**: Central Database Operating Layer for WebOS Multi-App + Multi-Tenant + High-Performance DB Engine Read/Write split, Transactions, Safe Queries Powers AliensCRUD & all data engines
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Database.php](WebOS/PHP.26.00.00/Engine/WebOS.Database.php)
- **Class**: `WebOS_Database`
- **Facade Entry Points**:
  - `WebOS.Database()` → `public static function Main($Payload, $Context)`
  - `WebOS.DatabaseRow()` → `public static function Row($Payload, $Context)`
  - `WebOS.DatabaseRows()` → `public static function Rows($Payload, $Context)`
  - `WebOS.DatabaseExec()` → `public static function Exec($Payload, $Context)`
  - `WebOS.DatabaseBegin()` → `public static function Begin($Payload, $Context)`
  - `WebOS.DatabaseCommit()` → `public static function Commit($Payload, $Context)`
  - `WebOS.DatabaseRollback()` → `public static function Rollback($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.Database()` |
| Row | `public static function Row($Payload, $Context)` | `WebOS.DatabaseRow()` |
| Rows | `public static function Rows($Payload, $Context)` | `WebOS.DatabaseRows()` |
| Exec | `public static function Exec($Payload, $Context)` | `WebOS.DatabaseExec()` |
| Begin | `public static function Begin($Payload, $Context)` | `WebOS.DatabaseBegin()` |
| Commit | `public static function Commit($Payload, $Context)` | `WebOS.DatabaseCommit()` |
| Rollback | `public static function Rollback($Payload, $Context)` | `WebOS.DatabaseRollback()` |
### <span id="webos-datapolicy"></span>WebOS.DataPolicy
- **Purpose**: OS-level Data Governance & Lifecycle Engine Retention, Archival, Purge, Legal Hold, Masking, Export Control Enforced across ALL units (API, Cron, Webhook, SDK) Multi-App + Multi-Tenant + Override aware
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.DataPolicy.php](WebOS/PHP.26.00.00/Engine/WebOS.DataPolicy.php)
- **Class**: `WebOS_DataPolicy`
- **Facade Entry Points**:
  - `WebOS.DataPolicy()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.DataPolicyResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.DataPolicyAllow()` → `public static function Allow(array $Payload, array $Context)`
  - `WebOS.DataPolicyMask()` → `public static function Mask(array $Payload, array $Context)`
  - `WebOS.DataPolicyPurgePlan()` → `public static function PurgePlan(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.DataPolicy()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.DataPolicyResolve()` |
| Allow | `public static function Allow(array $Payload, array $Context)` | `WebOS.DataPolicyAllow()` |
| Mask | `public static function Mask(array $Payload, array $Context)` | `WebOS.DataPolicyMask()` |
| PurgePlan | `public static function PurgePlan(array $Payload, array $Context)` | `WebOS.DataPolicyPurgePlan()` |
### <span id="webos-debugger"></span>WebOS.Debugger
- **Purpose**: Enterprise-grade Debugging Engine for WebOS Safe production debugging (no data leaks) Multi-App + Multi-Tenant isolation Integrated with Log, Trace, Metric, Monitor Near-zero overhead when disabled
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Debugger.php](WebOS/PHP.26.00.00/Engine/WebOS.Debugger.php)
- **Class**: `WebOS_Debugger`
- **Facade Entry Points**:
  - `WebOS.Debugger()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DebuggerMode()` → `public static function Mode(array $Payload, array $Context)`
  - `WebOS.DebuggerOn()` → `public static function On(array $Payload, array $Context)`
  - `WebOS.DebuggerOff()` → `public static function Off(array $Payload, array $Context)`
  - `WebOS.DebuggerLog()` → `public static function Log(array $Payload, array $Context)`
  - `WebOS.DebuggerStart()` → `public static function Start(array $Payload, array $Context)`
  - `WebOS.DebuggerEnd()` → `public static function End(array $Payload, array $Context)`
  - `WebOS.DebuggerSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Debugger()` |
| Mode | `public static function Mode(array $Payload, array $Context)` | `WebOS.DebuggerMode()` |
| On | `public static function On(array $Payload, array $Context)` | `WebOS.DebuggerOn()` |
| Off | `public static function Off(array $Payload, array $Context)` | `WebOS.DebuggerOff()` |
| Log | `public static function Log(array $Payload, array $Context)` | `WebOS.DebuggerLog()` |
| Start | `public static function Start(array $Payload, array $Context)` | `WebOS.DebuggerStart()` |
| End | `public static function End(array $Payload, array $Context)` | `WebOS.DebuggerEnd()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `WebOS.DebuggerSnapshot()` |
### <span id="webos-debuggerai"></span>WebOS.DebuggerAI
- **Purpose**: System-level autonomous debugging & incident intelligence engine Central error, anomaly, trace and incident brain for WebOS Safe, scoped, auditable debugging for Multi-App & Multi-Tenant systems
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.DebuggerAI.php](WebOS/PHP.26.00.00/Engine/WebOS.DebuggerAI.php)
- **Class**: `WebOS_DebuggerAI`
- **Facade Entry Points**:
  - `WebOS.DebuggerAI()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAITraceStart()` → `public static function TraceStart(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAITraceEnd()` → `public static function TraceEnd(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAIErrorIngest()` → `public static function ErrorIngest(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAIAnomalyDetect()` → `public static function AnomalyDetect(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAIExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAIFixPlan()` → `public static function FixPlan(array $Payload = [], array $Context = [])`
  - `WebOS.DebuggerAIFixApply()` → `public static function FixApply(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAI()` |
| TraceStart | `public static function TraceStart(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAITraceStart()` |
| TraceEnd | `public static function TraceEnd(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAITraceEnd()` |
| ErrorIngest | `public static function ErrorIngest(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAIErrorIngest()` |
| AnomalyDetect | `public static function AnomalyDetect(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAIAnomalyDetect()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAIExplain()` |
| FixPlan | `public static function FixPlan(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAIFixPlan()` |
| FixApply | `public static function FixApply(array $Payload = [], array $Context = [])` | `WebOS.DebuggerAIFixApply()` |
### <span id="webos-decision"></span>WebOS.Decision
- **Purpose**: Central deterministic decision engine for WebOS Access / Policy / Routing / Scoring decisions Multi-App, Multi-Brand, Multi-Tenant compatible Explainable, auditable, cache-aware decisions
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Decision.php](WebOS/PHP.26.00.00/Engine/WebOS.Decision.php)
- **Class**: `WebOS_Decision`
- **Facade Entry Points**:
  - `WebOS.Decision()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DecisionAllow()` → `public static function Allow(array $Payload, array $Context)`
  - `WebOS.DecisionRoute()` → `public static function Route(array $Payload, array $Context)`
  - `WebOS.DecisionScore()` → `public static function Score(array $Payload, array $Context)`
  - `WebOS.DecisionPolicy()` → `public static function Policy(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Decision()` |
| Allow | `public static function Allow(array $Payload, array $Context)` | `WebOS.DecisionAllow()` |
| Route | `public static function Route(array $Payload, array $Context)` | `WebOS.DecisionRoute()` |
| Score | `public static function Score(array $Payload, array $Context)` | `WebOS.DecisionScore()` |
| Policy | `public static function Policy(array $Payload, array $Context)` | `WebOS.DecisionPolicy()` |
### <span id="webos-department"></span>WebOS.Department
- **Purpose**: Enterprise-grade Department engine (Multi-Tenant + Multi-App) Department hierarchy (Parent/Path/Depth), move, tree Members mapping, role-ready, policy-ready Soft delete (status=9), deny-by-default
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Department.php](WebOS/PHP.26.00.00/Engine/WebOS.Department.php)
- **Class**: `WebOS_Department`
- **Facade Entry Points**:
  - `WebOS.Department()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DepartmentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.DepartmentGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.DepartmentList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.DepartmentUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.DepartmentStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.DepartmentTree()` → `public static function Tree(array $Payload, array $Context)`
  - `WebOS.DepartmentMove()` → `public static function Move(array $Payload, array $Context)`
  - `WebOS.DepartmentMemberAdd()` → `public static function MemberAdd(array $Payload, array $Context)`
  - `WebOS.DepartmentMemberRemove()` → `public static function MemberRemove(array $Payload, array $Context)`
  - `WebOS.DepartmentMemberList()` → `public static function MemberList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Department()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.DepartmentNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.DepartmentGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.DepartmentList()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.DepartmentUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.DepartmentStatus()` |
| Tree | `public static function Tree(array $Payload, array $Context)` | `WebOS.DepartmentTree()` |
| Move | `public static function Move(array $Payload, array $Context)` | `WebOS.DepartmentMove()` |
| MemberAdd | `public static function MemberAdd(array $Payload, array $Context)` | `WebOS.DepartmentMemberAdd()` |
| MemberRemove | `public static function MemberRemove(array $Payload, array $Context)` | `WebOS.DepartmentMemberRemove()` |
| MemberList | `public static function MemberList(array $Payload, array $Context)` | `WebOS.DepartmentMemberList()` |
### <span id="webos-deployment"></span>WebOS.Deployment
- **Purpose**: Enterprise-grade deployment orchestration engine Multi-App + Multi-Brand + Multi-Tenant deployments Zero-downtime, rollback-first philosophy OS-style deployment (not framework-style)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Deployment.php](WebOS/PHP.26.00.00/Engine/WebOS.Deployment.php)
- **Class**: `WebOS_Deployment`
- **Facade Entry Points**:
  - `WebOS.Deployment()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DeploymentPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.DeploymentRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.DeploymentRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `WebOS.DeploymentStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Deployment()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.DeploymentPlan()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.DeploymentRun()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.DeploymentRollback()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.DeploymentStatus()` |
### <span id="webos-deposit"></span>WebOS.Deposit
- **Purpose**: Enterprise-grade Deposit engine (Multi-App + Multi-Tenant) Deposit Intent → Provider Init → Webhook/Confirm → Ledger Posting Idempotency + Locking + Audit + Trace friendly Works for any backend: ecommerce, SaaS, wallet, fintech, crypto, education
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Deposit.php](WebOS/PHP.26.00.00/Engine/WebOS.Deposit.php)
- **Class**: `WebOS_Deposit`
- **Facade Entry Points**:
  - `WebOS.Deposit()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DepositNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.DepositInit()` → `public static function Init(array $Payload, array $Context)`
  - `WebOS.DepositGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.DepositList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.DepositCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.DepositWebhook()` → `public static function Webhook(array $Payload, array $Context)`
  - `WebOS.DepositConfirm()` → `public static function Confirm(array $Payload, array $Context)`
  - `WebOS.DepositReconcile()` → `public static function Reconcile(array $Payload, array $Context)`
  - `WebOS.DepositAdminAdjust()` → `public static function AdminAdjust(array $Payload, array $Context)`
  - `WebOS.DepositExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Deposit()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.DepositNew()` |
| Init | `public static function Init(array $Payload, array $Context)` | `WebOS.DepositInit()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.DepositGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.DepositList()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.DepositCancel()` |
| Webhook | `public static function Webhook(array $Payload, array $Context)` | `WebOS.DepositWebhook()` |
| Confirm | `public static function Confirm(array $Payload, array $Context)` | `WebOS.DepositConfirm()` |
| Reconcile | `public static function Reconcile(array $Payload, array $Context)` | `WebOS.DepositReconcile()` |
| AdminAdjust | `public static function AdminAdjust(array $Payload, array $Context)` | `WebOS.DepositAdminAdjust()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.DepositExport()` |
### <span id="webos-device"></span>WebOS.Device
- **Purpose**: Enterprise-grade Device Identity + Trust + Risk Layer for WebOS Multi-App + Multi-Tenant device registry Session binding support (anti-hijack baseline) Blocklist support (device/fingerprint) High-performance request-level cache + optional persistence
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Device.php](WebOS/PHP.26.00.00/Engine/WebOS.Device.php)
- **Class**: `WebOS_Device`
- **Facade Entry Points**:
  - `WebOS.Device()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DeviceIdentify()` → `public static function Identify(array $Payload, array $Context)`
  - `WebOS.DeviceGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.DeviceGetByFingerprint()` → `public static function GetByFingerprint(array $Payload, array $Context)`
  - `WebOS.DeviceTrust()` → `public static function Trust(array $Payload, array $Context)`
  - `WebOS.DeviceBindSession()` → `public static function BindSession(array $Payload, array $Context)`
  - `WebOS.DeviceValidateSession()` → `public static function ValidateSession(array $Payload, array $Context)`
  - `WebOS.DeviceEvent()` → `public static function Event(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Device()` |
| Identify | `public static function Identify(array $Payload, array $Context)` | `WebOS.DeviceIdentify()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.DeviceGet()` |
| GetByFingerprint | `public static function GetByFingerprint(array $Payload, array $Context)` | `WebOS.DeviceGetByFingerprint()` |
| Trust | `public static function Trust(array $Payload, array $Context)` | `WebOS.DeviceTrust()` |
| BindSession | `public static function BindSession(array $Payload, array $Context)` | `WebOS.DeviceBindSession()` |
| ValidateSession | `public static function ValidateSession(array $Payload, array $Context)` | `WebOS.DeviceValidateSession()` |
| Event | `public static function Event(array $Payload, array $Context)` | `WebOS.DeviceEvent()` |
### <span id="webos-digitaltwin"></span>WebOS.DigitalTwin
- **Purpose**: Runtime Digital Twin for WebOS (System / App / Tenant) Snapshot, Diff, Replay, Simulation, Forecast Safe experimentation + predictive intelligence
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.DigitalTwin.php](WebOS/PHP.26.00.00/Engine/WebOS.DigitalTwin.php)
- **Class**: `WebOS_DigitalTwin`
- **Facade Entry Points**:
  - `WebOS.DigitalTwin()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DigitalTwinSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`
  - `WebOS.DigitalTwinGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.DigitalTwinDiff()` → `public static function Diff(array $Payload, array $Context)`
  - `WebOS.DigitalTwinReplay()` → `public static function Replay(array $Payload, array $Context)`
  - `WebOS.DigitalTwinSimulate()` → `public static function Simulate(array $Payload, array $Context)`
  - `WebOS.DigitalTwinForecast()` → `public static function Forecast(array $Payload, array $Context)`
  - `WebOS.DigitalTwinExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.DigitalTwin()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `WebOS.DigitalTwinSnapshot()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.DigitalTwinGet()` |
| Diff | `public static function Diff(array $Payload, array $Context)` | `WebOS.DigitalTwinDiff()` |
| Replay | `public static function Replay(array $Payload, array $Context)` | `WebOS.DigitalTwinReplay()` |
| Simulate | `public static function Simulate(array $Payload, array $Context)` | `WebOS.DigitalTwinSimulate()` |
| Forecast | `public static function Forecast(array $Payload, array $Context)` | `WebOS.DigitalTwinForecast()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.DigitalTwinExport()` |
### <span id="webos-doc"></span>WebOS.Doc
- **Purpose**: Make WebOS a self-documenting backend Operating System Auto-generate documentation for Engines, Units, Modules Multi-App + Multi-Brand + Multi-Tenant aware AI-readable + Human-readable documentation Enterprise-grade compliance, validation & versioning
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Doc.php](WebOS/PHP.26.00.00/Engine/WebOS.Doc.php)
- **Class**: `WebOS_Doc`
- **Facade Entry Points**:
  - `WebOS.Doc()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.DocBuild()` → `public static function Build(array $Payload, array $Context)`
  - `WebOS.DocServe()` → `public static function Serve(array $Payload, array $Context)`
  - `WebOS.DocSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.DocPage()` → `public static function Page(array $Payload, array $Context)`
  - `WebOS.DocValidate()` → `public static function Validate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Doc()` |
| Build | `public static function Build(array $Payload, array $Context)` | `WebOS.DocBuild()` |
| Serve | `public static function Serve(array $Payload, array $Context)` | `WebOS.DocServe()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.DocSearch()` |
| Page | `public static function Page(array $Payload, array $Context)` | `WebOS.DocPage()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.DocValidate()` |
### <span id="webos-doctor"></span>WebOS.Doctor
- **Purpose**: Enterprise Grade Doctor Domain Engine (Multi-App + Multi-Tenant) Doctor Identity, Verification, Clinic Linking, Schedule, Availability High-Performance Read Paths + Safe Cache Scoping
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Doctor.php](WebOS/PHP.26.00.00/Engine/WebOS.Doctor.php)
- **Class**: `WebOS_Doctor`
- **Facade Entry Points**:
  - `WebOS.Doctor()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DoctorNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.DoctorGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.DoctorUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.DoctorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.DoctorList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.DoctorSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.DoctorVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.DoctorScheduleSet()` → `public static function ScheduleSet(array $Payload, array $Context)`
  - `WebOS.DoctorScheduleGet()` → `public static function ScheduleGet(array $Payload, array $Context)`
  - `WebOS.DoctorAvailability()` → `public static function Availability(array $Payload, array $Context)`
  - `WebOS.DoctorStats()` → `public static function Stats(array $Payload, array $Context)`
  - `WebOS.DoctorExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Doctor()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.DoctorNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.DoctorGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.DoctorUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.DoctorStatus()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.DoctorList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.DoctorSearch()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.DoctorVerify()` |
| ScheduleSet | `public static function ScheduleSet(array $Payload, array $Context)` | `WebOS.DoctorScheduleSet()` |
| ScheduleGet | `public static function ScheduleGet(array $Payload, array $Context)` | `WebOS.DoctorScheduleGet()` |
| Availability | `public static function Availability(array $Payload, array $Context)` | `WebOS.DoctorAvailability()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `WebOS.DoctorStats()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.DoctorExport()` |
### <span id="webos-download"></span>WebOS.Download
- **Purpose**: Secure, high-performance, enterprise-grade download engine Multi-App + Multi-Tenant safe delivery Supports private/public/signed downloads
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Download.php](WebOS/PHP.26.00.00/Engine/WebOS.Download.php)
- **Class**: `WebOS_Download`
- **Facade Entry Points**:
  - `WebOS.Download()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.DownloadGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.DownloadLink()` → `public static function Link(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Download()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.DownloadGet()` |
| Link | `public static function Link(array $Payload, array $Context)` | `WebOS.DownloadLink()` |
### <span id="webos-educationai"></span>WebOS.EducationAI
- **Purpose**: Enterprise Grade AI Engine for Education Systems Powering LMS, Tutors, Exams, Content Factory Multi-App + Multi-Tenant + High Performance
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.EducationAI.php](WebOS/PHP.26.00.00/Engine/WebOS.EducationAI.php)
- **Class**: `WebOS_EducationAI`
- **Facade Entry Points**:
  - `WebOS.EducationAI()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EducationAIConfigGet()` → `public static function ConfigGet(array $Payload, array $Context)`
  - `WebOS.EducationAIPolicyCheck()` → `public static function PolicyCheck(array $Payload, array $Context)`
  - `WebOS.EducationAITutorStart()` → `public static function TutorStart(array $Payload, array $Context)`
  - `WebOS.EducationAITutorAsk()` → `public static function TutorAsk(array $Payload, array $Context)`
  - `WebOS.EducationAILessonGenerate()` → `public static function LessonGenerate(array $Payload, array $Context)`
  - `WebOS.EducationAILessonGenerateWorker()` → `public static function LessonGenerateWorker(array $Payload, array $Context)`
  - `WebOS.EducationAIEvaluateQuiz()` → `public static function EvaluateQuiz(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.EducationAI()` |
| ConfigGet | `public static function ConfigGet(array $Payload, array $Context)` | `WebOS.EducationAIConfigGet()` |
| PolicyCheck | `public static function PolicyCheck(array $Payload, array $Context)` | `WebOS.EducationAIPolicyCheck()` |
| TutorStart | `public static function TutorStart(array $Payload, array $Context)` | `WebOS.EducationAITutorStart()` |
| TutorAsk | `public static function TutorAsk(array $Payload, array $Context)` | `WebOS.EducationAITutorAsk()` |
| LessonGenerate | `public static function LessonGenerate(array $Payload, array $Context)` | `WebOS.EducationAILessonGenerate()` |
| LessonGenerateWorker | `public static function LessonGenerateWorker(array $Payload, array $Context)` | `WebOS.EducationAILessonGenerateWorker()` |
| EvaluateQuiz | `public static function EvaluateQuiz(array $Payload, array $Context)` | `WebOS.EducationAIEvaluateQuiz()` |
### <span id="webos-email"></span>WebOS.Email
- **Purpose**: Enterprise-grade Email Engine for WebOS Multi-App, Multi-Brand, Multi-Tenant High-performance async email orchestration Template + Provider abstraction Queue-ready, audit-ready, secure by default
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Email.php](WebOS/PHP.26.00.00/Engine/WebOS.Email.php)
- **Class**: `WebOS_Email`
- **Facade Entry Points**:
  - `WebOS.Email()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EmailSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.EmailBatch()` → `public static function Batch(array $Payload, array $Context)`
  - `WebOS.EmailTemplateRender()` → `public static function TemplateRender(array $Payload, array $Context)`
  - `WebOS.EmailStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Email()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.EmailSend()` |
| Batch | `public static function Batch(array $Payload, array $Context)` | `WebOS.EmailBatch()` |
| TemplateRender | `public static function TemplateRender(array $Payload, array $Context)` | `WebOS.EmailTemplateRender()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.EmailStatus()` |
### <span id="webos-embedding"></span>WebOS.Embedding
- **Purpose**: Enterprise-grade Embedding orchestration engine Multi-App, Multi-Tenant, High-Performance Provider-agnostic (Azure/OpenAI/Local/Future) Works as Semantic Intelligence Kernel for WebOS
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Embedding.php](WebOS/PHP.26.00.00/Engine/WebOS.Embedding.php)
- **Class**: `WebOS_Embedding`
- **Facade Entry Points**:
  - `WebOS.Embedding()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EmbeddingUpsert()` → `public static function Upsert(array $Payload, array $Context)`
  - `WebOS.EmbeddingSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.EmbeddingDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.EmbeddingHealth()` → `public static function Health(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Embedding()` |
| Upsert | `public static function Upsert(array $Payload, array $Context)` | `WebOS.EmbeddingUpsert()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.EmbeddingSearch()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.EmbeddingDelete()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.EmbeddingHealth()` |
### <span id="webos-emergency"></span>WebOS.Emergency
- **Purpose**: Enterprise-grade Incident / Emergency OS Layer for WebOS Multi-App + Multi-Tenant safe incident lifecycle: New → Get → Update → Escalate → Action → Close → Audit SafeMode orchestration (Normal/SafeL1/SafeL2/SafeL3/Lockdown) Deterministic, deny-by-default, audit-first, hook-compatible
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Emergency.php](WebOS/PHP.26.00.00/Engine/WebOS.Emergency.php)
- **Class**: `WebOS_Emergency`
- **Facade Entry Points**:
  - `WebOS.Emergency()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyEscalate()` → `public static function Escalate(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyClose()` → `public static function Close(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyList()` → `public static function List(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyAction()` → `public static function Action(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyStatus()` → `public static function Status(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencySafeModeEnable()` → `public static function SafeModeEnable(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencySafeModeDisable()` → `public static function SafeModeDisable(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyKillSwitchSet()` → `public static function KillSwitchSet(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyTrafficShed()` → `public static function TrafficShed(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyFailover()` → `public static function Failover(array $Payload = [], array $Context = [])`
  - `WebOS.EmergencyNotify()` → `public static function Notify(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Emergency()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `WebOS.EmergencyNew()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.EmergencyGet()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `WebOS.EmergencyUpdate()` |
| Escalate | `public static function Escalate(array $Payload = [], array $Context = [])` | `WebOS.EmergencyEscalate()` |
| Close | `public static function Close(array $Payload = [], array $Context = [])` | `WebOS.EmergencyClose()` |
| List | `public static function List(array $Payload = [], array $Context = [])` | `WebOS.EmergencyList()` |
| Action | `public static function Action(array $Payload = [], array $Context = [])` | `WebOS.EmergencyAction()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `WebOS.EmergencyStatus()` |
| SafeModeEnable | `public static function SafeModeEnable(array $Payload = [], array $Context = [])` | `WebOS.EmergencySafeModeEnable()` |
| SafeModeDisable | `public static function SafeModeDisable(array $Payload = [], array $Context = [])` | `WebOS.EmergencySafeModeDisable()` |
| KillSwitchSet | `public static function KillSwitchSet(array $Payload = [], array $Context = [])` | `WebOS.EmergencyKillSwitchSet()` |
| TrafficShed | `public static function TrafficShed(array $Payload = [], array $Context = [])` | `WebOS.EmergencyTrafficShed()` |
| Failover | `public static function Failover(array $Payload = [], array $Context = [])` | `WebOS.EmergencyFailover()` |
| Notify | `public static function Notify(array $Payload = [], array $Context = [])` | `WebOS.EmergencyNotify()` |
### <span id="webos-employee"></span>WebOS.Employee
- **Purpose**: Enterprise-grade Employee Engine Multi-Tenant + Multi-App Workforce Core Central employee abstraction for HR, Payroll, Attendance, Projects
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Employee.php](WebOS/PHP.26.00.00/Engine/WebOS.Employee.php)
- **Class**: `WebOS_Employee`
- **Facade Entry Points**:
  - `WebOS.Employee()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EmployeeNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.EmployeeGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.EmployeeUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.EmployeeStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.EmployeeList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.EmployeeTransfer()` → `public static function Transfer(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Employee()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.EmployeeNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.EmployeeGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.EmployeeUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.EmployeeStatus()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.EmployeeList()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `WebOS.EmployeeTransfer()` |
### <span id="webos-emulator"></span>WebOS.Emulator
- **Purpose**: Provide a virtualized execution layer for WebOS Units Emulate API / Cron / Webhook / Component execution safely Enable Sandbox, Replay, Shadow, Chaos and Perf modes Support Multi-App, Multi-Brand, Multi-Tenant isolation Act as WebOS "Virtual Backend Machine"
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Emulator.php](WebOS/PHP.26.00.00/Engine/WebOS.Emulator.php)
- **Class**: `WebOS_Emulator`
- **Facade Entry Points**:
  - `WebOS.Emulator()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EmulatorRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.EmulatorApi()` → `public static function Api(array $Payload, array $Context)`
  - `WebOS.EmulatorCron()` → `public static function Cron(array $Payload, array $Context)`
  - `WebOS.EmulatorWebhook()` → `public static function Webhook(array $Payload, array $Context)`
  - `WebOS.EmulatorReplay()` → `public static function Replay(array $Payload, array $Context)`
  - `WebOS.EmulatorReport()` → `public static function Report(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Emulator()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.EmulatorRun()` |
| Api | `public static function Api(array $Payload, array $Context)` | `WebOS.EmulatorApi()` |
| Cron | `public static function Cron(array $Payload, array $Context)` | `WebOS.EmulatorCron()` |
| Webhook | `public static function Webhook(array $Payload, array $Context)` | `WebOS.EmulatorWebhook()` |
| Replay | `public static function Replay(array $Payload, array $Context)` | `WebOS.EmulatorReplay()` |
| Report | `public static function Report(array $Payload, array $Context)` | `WebOS.EmulatorReport()` |
### <span id="webos-encryption"></span>WebOS.Encryption
- **Purpose**: Central cryptography engine for WebOS Encryption / Decryption (AEAD) Hashing & Password Hashing HMAC Sign / Verify Random / Nonce generation
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Encryption.php](WebOS/PHP.26.00.00/Engine/WebOS.Encryption.php)
- **Class**: `WebOS_Encryption`
- **Facade Entry Points**:
  - `WebOS.Encryption()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EncryptionEncrypt()` → `public static function Encrypt(array $Payload, array $Context)`
  - `WebOS.EncryptionDecrypt()` → `public static function Decrypt(array $Payload, array $Context)`
  - `WebOS.EncryptionHash()` → `public static function Hash(array $Payload, array $Context)`
  - `WebOS.EncryptionPasswordHash()` → `public static function PasswordHash(array $Payload, array $Context)`
  - `WebOS.EncryptionPasswordVerify()` → `public static function PasswordVerify(array $Payload, array $Context)`
  - `WebOS.EncryptionSign()` → `public static function Sign(array $Payload, array $Context)`
  - `WebOS.EncryptionVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.EncryptionRandom()` → `public static function Random(array $Payload, array $Context)`
  - `WebOS.EncryptionNonce()` → `public static function Nonce(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Encryption()` |
| Encrypt | `public static function Encrypt(array $Payload, array $Context)` | `WebOS.EncryptionEncrypt()` |
| Decrypt | `public static function Decrypt(array $Payload, array $Context)` | `WebOS.EncryptionDecrypt()` |
| Hash | `public static function Hash(array $Payload, array $Context)` | `WebOS.EncryptionHash()` |
| PasswordHash | `public static function PasswordHash(array $Payload, array $Context)` | `WebOS.EncryptionPasswordHash()` |
| PasswordVerify | `public static function PasswordVerify(array $Payload, array $Context)` | `WebOS.EncryptionPasswordVerify()` |
| Sign | `public static function Sign(array $Payload, array $Context)` | `WebOS.EncryptionSign()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.EncryptionVerify()` |
| Random | `public static function Random(array $Payload, array $Context)` | `WebOS.EncryptionRandom()` |
| Nonce | `public static function Nonce(array $Payload, array $Context)` | `WebOS.EncryptionNonce()` |
### <span id="webos-env"></span>WebOS.Env
- **Purpose**: Purpose not documented in source.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Env.php](WebOS/PHP.26.00.00/Engine/WebOS.Env.php)
- **Class**: Not declared
- **Facade Entry Points**: None documented
### <span id="webos-environment"></span>WebOS.Environment
- **Purpose**: Enterprise-grade Environment Control Plane for WebOS (Multi-App + Multi-Tenant) Single source of truth: Env Profile (perf + security + ops + log + flags) Deterministic precedence + safe fallbacks (production-safe by default) Designed to be called ONLY via WebOS.Loader + WebOS.EngineRouter (Static Facade)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Environment.php](WebOS/PHP.26.00.00/Engine/WebOS.Environment.php)
- **Class**: `WebOS_Environment`
- **Facade Entry Points**:
  - `WebOS.Environment()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentName()` → `public static function Name(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentIs()` → `public static function Is(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentMode()` → `public static function Mode(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentApply()` → `public static function Apply(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentKey()` → `public static function Key(array $Payload = [], array $Context = [])`
  - `WebOS.EnvironmentHash()` → `public static function Hash(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Environment()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentGet()` |
| Name | `public static function Name(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentName()` |
| Is | `public static function Is(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentIs()` |
| Mode | `public static function Mode(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentMode()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentPolicy()` |
| Apply | `public static function Apply(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentApply()` |
| Key | `public static function Key(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentKey()` |
| Hash | `public static function Hash(array $Payload = [], array $Context = [])` | `WebOS.EnvironmentHash()` |
### <span id="webos-eventbus"></span>WebOS.EventBus
- **Purpose**: Central nervous system for WebOS Durable, multi-tenant, multi-app event orchestration High-performance publish / subscribe / consume
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.EventBus.php](WebOS/PHP.26.00.00/Engine/WebOS.EventBus.php)
- **Class**: `WebOS_EventBus`
- **Facade Entry Points**:
  - `WebOS.EventBus()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.EventBusPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `WebOS.EventBusPublishBatch()` → `public static function PublishBatch(array $Payload, array $Context)`
  - `WebOS.EventBusConsume()` → `public static function Consume(array $Payload, array $Context)`
  - `WebOS.EventBusAck()` → `public static function Ack(array $Payload, array $Context)`
  - `WebOS.EventBusNack()` → `public static function Nack(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.EventBus()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.EventBusPublish()` |
| PublishBatch | `public static function PublishBatch(array $Payload, array $Context)` | `WebOS.EventBusPublishBatch()` |
| Consume | `public static function Consume(array $Payload, array $Context)` | `WebOS.EventBusConsume()` |
| Ack | `public static function Ack(array $Payload, array $Context)` | `WebOS.EventBusAck()` |
| Nack | `public static function Nack(array $Payload, array $Context)` | `WebOS.EventBusNack()` |
### <span id="webos-exam"></span>WebOS.Exam
- **Purpose**: Central Exam Engine for WebOS Works for LMS, HR, Certification, Medical, Compliance exams OS-level exam lifecycle management Highlights     : Loader + EngineRouter compatible Multi-App + Multi-Tenant safe State-driven exam lifecycle Audit, Cache, Lock, Event enabled Dependencies   : WebOS.Security WebOS.Auth WebOS.Role WebOS.Permission WebOS.Database (AliensCRUD) WebOS.Cache WebOS.Lock WebOS.Audit WebOS.Event =============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Exam.php](WebOS/PHP.26.00.00/Engine/WebOS.Exam.php)
- **Class**: `WebOS_Exam`
- **Facade Entry Points**:
  - `WebOS.Exam()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ExamNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ExamPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `WebOS.ExamAttemptStart()` → `public static function AttemptStart(array $Payload, array $Context)`
  - `WebOS.ExamSubmit()` → `public static function Submit(array $Payload, array $Context)`
  - `WebOS.ExamResultGet()` → `public static function ResultGet(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Exam()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ExamNew()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.ExamPublish()` |
| AttemptStart | `public static function AttemptStart(array $Payload, array $Context)` | `WebOS.ExamAttemptStart()` |
| Submit | `public static function Submit(array $Payload, array $Context)` | `WebOS.ExamSubmit()` |
| ResultGet | `public static function ResultGet(array $Payload, array $Context)` | `WebOS.ExamResultGet()` |
### <span id="webos-executor"></span>WebOS.Executor
- **Purpose**: Central execution engine for all Units (API/Cron/Webhook/Asset/Component/Template) Boot files remain orchestration-only (no functions/
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Executor.php](WebOS/PHP.26.00.00/Engine/WebOS.Executor.php)
- **Class**: `WebOS_Executor`
- **Facade Entry Points**:
  - `WebOS.Executor()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorApi()` → `public static function Api(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorApis()` → `public static function Apis(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorCron()` → `public static function Cron(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorWebhook()` → `public static function Webhook(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorAsset()` → `public static function Asset(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorComponent()` → `public static function Component(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorTemplate()` → `public static function Template(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorUnit()` → `public static function Unit(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `WebOS.ExecutorWrap()` → `public static function Wrap(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Executor()` |
| Api | `public static function Api(array $Payload = [], array $Context = [])` | `WebOS.ExecutorApi()` |
| Apis | `public static function Apis(array $Payload = [], array $Context = [])` | `WebOS.ExecutorApis()` |
| Cron | `public static function Cron(array $Payload = [], array $Context = [])` | `WebOS.ExecutorCron()` |
| Webhook | `public static function Webhook(array $Payload = [], array $Context = [])` | `WebOS.ExecutorWebhook()` |
| Asset | `public static function Asset(array $Payload = [], array $Context = [])` | `WebOS.ExecutorAsset()` |
| Component | `public static function Component(array $Payload = [], array $Context = [])` | `WebOS.ExecutorComponent()` |
| Template | `public static function Template(array $Payload = [], array $Context = [])` | `WebOS.ExecutorTemplate()` |
| Unit | `public static function Unit(array $Payload = [], array $Context = [])` | `WebOS.ExecutorUnit()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `WebOS.ExecutorPolicy()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `WebOS.ExecutorRun()` |
| Wrap | `public static function Wrap(array $Payload = [], array $Context = [])` | `WebOS.ExecutorWrap()` |
### <span id="webos-experiment"></span>WebOS.Experiment
- **Purpose**: Enterprise-grade Experimentation Engine for WebOS A/B Tests, Canary Releases, Progressive Rollouts Multi-App + Multi-Tenant safe by design Cache-first, ultra-fast runtime decisions Audit-ready, rollback-capable
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Experiment.php](WebOS/PHP.26.00.00/Engine/WebOS.Experiment.php)
- **Class**: `WebOS_Experiment`
- **Facade Entry Points**:
  - `WebOS.Experiment()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ExperimentNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ExperimentGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ExperimentStart()` → `public static function Start(array $Payload, array $Context)`
  - `WebOS.ExperimentDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `WebOS.ExperimentTrack()` → `public static function Track(array $Payload, array $Context)`
  - `WebOS.ExperimentStop()` → `public static function Stop(array $Payload, array $Context)`
  - `WebOS.ExperimentKill()` → `public static function Kill(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Experiment()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ExperimentNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ExperimentGet()` |
| Start | `public static function Start(array $Payload, array $Context)` | `WebOS.ExperimentStart()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `WebOS.ExperimentDecide()` |
| Track | `public static function Track(array $Payload, array $Context)` | `WebOS.ExperimentTrack()` |
| Stop | `public static function Stop(array $Payload, array $Context)` | `WebOS.ExperimentStop()` |
| Kill | `public static function Kill(array $Payload, array $Context)` | `WebOS.ExperimentKill()` |
### <span id="webos-failover"></span>WebOS.Failover
- **Purpose**: Central failover decision & state engine for WebOS Policy-driven, multi-scope failover orchestration Supports Global / App / Tenant / Dependency scopes
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Failover.php](WebOS/PHP.26.00.00/Engine/WebOS.Failover.php)
- **Class**: `WebOS_Failover`
- **Facade Entry Points**:
  - `WebOS.Failover()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FailoverDetect()` → `public static function Detect(array $Payload, array $Context)`
  - `WebOS.FailoverDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `WebOS.FailoverSwitch()` → `public static function Switch(array $Payload, array $Context)`
  - `WebOS.FailoverRecover()` → `public static function Recover(array $Payload, array $Context)`
  - `WebOS.FailoverStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Failover()` |
| Detect | `public static function Detect(array $Payload, array $Context)` | `WebOS.FailoverDetect()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `WebOS.FailoverDecide()` |
| Switch | `public static function Switch(array $Payload, array $Context)` | `WebOS.FailoverSwitch()` |
| Recover | `public static function Recover(array $Payload, array $Context)` | `WebOS.FailoverRecover()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.FailoverStatus()` |
### <span id="webos-feature"></span>WebOS.Feature
- **Purpose**: Enterprise-grade Feature Flag & Toggle Engine Multi-App, Multi-Tenant, Role/User scoped Deterministic rollout, variant & kill-switch support
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Feature.php](WebOS/PHP.26.00.00/Engine/WebOS.Feature.php)
- **Class**: `WebOS_Feature`
- **Facade Entry Points**:
  - `WebOS.Feature()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FeatureIsEnabled()` → `public static function IsEnabled(array $Payload, array $Context)`
  - `WebOS.FeatureVariant()` → `public static function Variant(array $Payload, array $Context)`
  - `WebOS.FeatureConfig()` → `public static function Config(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Feature()` |
| IsEnabled | `public static function IsEnabled(array $Payload, array $Context)` | `WebOS.FeatureIsEnabled()` |
| Variant | `public static function Variant(array $Payload, array $Context)` | `WebOS.FeatureVariant()` |
| Config | `public static function Config(array $Payload, array $Context)` | `WebOS.FeatureConfig()` |
### <span id="webos-featureflag"></span>WebOS.FeatureFlag
- **Purpose**: OS-level Feature Flag system for WebOS Runtime feature enable/disable without deployment Multi-App + Multi-Tenant + Role/User aware Deterministic rollout (hash / percent / schedule) Ultra-fast evaluation with cache-first strategy
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.FeatureFlag.php](WebOS/PHP.26.00.00/Engine/WebOS.FeatureFlag.php)
- **Class**: `WebOS_FeatureFlag`
- **Facade Entry Points**:
  - `WebOS.FeatureFlag()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FeatureFlagIsEnabled()` → `public static function IsEnabled(array $Payload, array $Context)`
  - `WebOS.FeatureFlagEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.FeatureFlag()` |
| IsEnabled | `public static function IsEnabled(array $Payload, array $Context)` | `WebOS.FeatureFlagIsEnabled()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `WebOS.FeatureFlagEvaluate()` |
### <span id="webos-feed"></span>WebOS.Feed
- **Purpose**: Universal Feed / Activity / Timeline Engine Works for Social, Enterprise, LMS, E-Commerce, System Feeds Multi-App + Multi-Tenant + High-Performance Push / Pull / Hybrid ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Feed.php](WebOS/PHP.26.00.00/Engine/WebOS.Feed.php)
- **Class**: `WebOS_Feed`
- **Facade Entry Points**:
  - `WebOS.Feed()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FeedFeedPublish()` → `public static function FeedPublish(array $Payload, array $Context)`
  - `WebOS.FeedFeedGet()` → `public static function FeedGet(array $Payload, array $Context)`
  - `WebOS.FeedFeedCount()` → `public static function FeedCount(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Feed()` |
| FeedPublish | `public static function FeedPublish(array $Payload, array $Context)` | `WebOS.FeedFeedPublish()` |
| FeedGet | `public static function FeedGet(array $Payload, array $Context)` | `WebOS.FeedFeedGet()` |
| FeedCount | `public static function FeedCount(array $Payload, array $Context)` | `WebOS.FeedFeedCount()` |
### <span id="webos-filter"></span>WebOS.Filter
- **Purpose**: Universal, secure, high-performance filter engine One DSL → DB / Analytics / API ready Policy-driven, tenant-safe, cache-aware filtering
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Filter.php](WebOS/PHP.26.00.00/Engine/WebOS.Filter.php)
- **Class**: `WebOS_Filter`
- **Facade Entry Points**:
  - `WebOS.Filter()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FilterParse()` → `public static function Parse(array $Payload, array $Context)`
  - `WebOS.FilterApply()` → `public static function Apply(array $Plan, array $Context)`
  - `WebOS.FilterExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Filter()` |
| Parse | `public static function Parse(array $Payload, array $Context)` | `WebOS.FilterParse()` |
| Apply | `public static function Apply(array $Plan, array $Context)` | `WebOS.FilterApply()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.FilterExplain()` |
### <span id="webos-firewall"></span>WebOS.Firewall
- **Purpose**: Central request firewall for WebOS Entry-layer protection for API / Webhook / Asset / Cron / Component Multi-App + Multi-Tenant aware High-performance, cache-driven, deny-by-default
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Firewall.php](WebOS/PHP.26.00.00/Engine/WebOS.Firewall.php)
- **Class**: `WebOS_Firewall`
- **Facade Entry Points**:
  - `WebOS.Firewall()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FirewallCheck()` → `public static function Check(array $Payload, array $Context)`
  - `WebOS.FirewallEnforce()` → `public static function Enforce(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Firewall()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.FirewallCheck()` |
| Enforce | `public static function Enforce(array $Payload, array $Context)` | `WebOS.FirewallEnforce()` |
### <span id="webos-formula"></span>WebOS.Formula
- **Purpose**: Enterprise-grade Formula Engine for WebOS Backend OS Safe formula evaluation without eval() Multi-App + Multi-Tenant + Multi-Brand scoping via Context Compile → Cache → Eval (High-performance) Deterministic results, explain mode, dependency-ready design
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Formula.php](WebOS/PHP.26.00.00/Engine/WebOS.Formula.php)
- **Class**: `WebOS_Formula`
- **Facade Entry Points**:
  - `WebOS.Formula()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FormulaEval()` → `public static function Eval(array $Payload, array $Context)`
  - `WebOS.FormulaValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.FormulaCompile()` → `public static function Compile(array $Payload, array $Context)`
  - `WebOS.FormulaExplain()` → `public static function Explain(array $Payload, array $Context)`
  - `WebOS.FormulaRegister()` → `public static function Register(array $Payload, array $Context)`
  - `WebOS.FormulaActivate()` → `public static function Activate(array $Payload, array $Context)`
  - `WebOS.FormulaRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `WebOS.FormulaGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.FormulaList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.FormulaDependency()` → `public static function Dependency(array $Payload, array $Context)`
  - `WebOS.FormulaBenchmark()` → `public static function Benchmark(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Formula()` |
| Eval | `public static function Eval(array $Payload, array $Context)` | `WebOS.FormulaEval()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.FormulaValidate()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `WebOS.FormulaCompile()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.FormulaExplain()` |
| Register | `public static function Register(array $Payload, array $Context)` | `WebOS.FormulaRegister()` |
| Activate | `public static function Activate(array $Payload, array $Context)` | `WebOS.FormulaActivate()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.FormulaRollback()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.FormulaGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.FormulaList()` |
| Dependency | `public static function Dependency(array $Payload, array $Context)` | `WebOS.FormulaDependency()` |
| Benchmark | `public static function Benchmark(array $Payload, array $Context)` | `WebOS.FormulaBenchmark()` |
### <span id="webos-friend"></span>WebOS.Friend
- **Purpose**: Enterprise-grade Friend / Relationship Engine Multi-Tenant + Multi-App compatible Symmetric friendship graph (A↔B) Privacy, abuse-safe, audit-ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Friend.php](WebOS/PHP.26.00.00/Engine/WebOS.Friend.php)
- **Class**: `WebOS_Friend`
- **Facade Entry Points**:
  - `WebOS.Friend()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.FriendAdd()` → `public static function Add(array $Payload, array $Context)`
  - `WebOS.FriendAccept()` → `public static function Accept(array $Payload, array $Context)`
  - `WebOS.FriendRemove()` → `public static function Remove(array $Payload, array $Context)`
  - `WebOS.FriendStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Friend()` |
| Add | `public static function Add(array $Payload, array $Context)` | `WebOS.FriendAdd()` |
| Accept | `public static function Accept(array $Payload, array $Context)` | `WebOS.FriendAccept()` |
| Remove | `public static function Remove(array $Payload, array $Context)` | `WebOS.FriendRemove()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.FriendStatus()` |
### <span id="webos-generator"></span>WebOS.Generator
- **Purpose**: Enterprise-grade code, unit, app, module generator Backend factory for WebOS (OS-level capability) Deterministic, auditable, multi-layer aware generation
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Generator.php](WebOS/PHP.26.00.00/Engine/WebOS.Generator.php)
- **Class**: `WebOS_Generator`
- **Facade Entry Points**:
  - `WebOS.Generator()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.GeneratorPreview()` → `public static function Preview(array $Payload, array $Context)`
  - `WebOS.GeneratorEngine()` → `public static function Engine(array $Payload, array $Context)`
  - `WebOS.GeneratorUnit()` → `public static function Unit(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Generator()` |
| Preview | `public static function Preview(array $Payload, array $Context)` | `WebOS.GeneratorPreview()` |
| Engine | `public static function Engine(array $Payload, array $Context)` | `WebOS.GeneratorEngine()` |
| Unit | `public static function Unit(array $Payload, array $Context)` | `WebOS.GeneratorUnit()` |
### <span id="webos-governor"></span>WebOS.Governor
- **Purpose**: Global control & protection layer for WebOS. Acts as: Resource Governor Policy Enforcer Quota & Limit Controller Circuit Breaker Gate Multi-Tenant Safety Layer
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Governor.php](WebOS/PHP.26.00.00/Engine/WebOS.Governor.php)
- **Class**: `WebOS_Governor`
- **Facade Entry Points**:
  - `WebOS.Governor()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.GovernorAllow()` → `public static function Allow(array $Payload, array $Context)`
  - `WebOS.GovernorEnforce()` → `public static function Enforce(array $Payload, array $Context)`
  - `WebOS.GovernorSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Governor()` |
| Allow | `public static function Allow(array $Payload, array $Context)` | `WebOS.GovernorAllow()` |
| Enforce | `public static function Enforce(array $Payload, array $Context)` | `WebOS.GovernorEnforce()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `WebOS.GovernorSnapshot()` |
### <span id="webos-health"></span>WebOS.Health
- **Purpose**: Enterprise Health System for WebOS (Multi-App + Multi-Tenant) Liveness / Readiness probes for Load Balancers & Orchestrators Quick/Standard/Deep health reports with strict performance rules Dependency-aware probe execution with caching & safe fallbacks ---------------------------------------------------------------------------- AliensGrade Highlights: Loader/Router compatible (WebOS::HealthX routed by EngineRouter) Context-driven (no globals; uses $Context user/app/brand/ecosystem) Deny heavy work by default (Quick path is ultra-fast) Sanitized public output + admin-only deep details Caching with scope keys to prevent cross-tenant/app mixing ---------------------------------------------------------------------------- Dependencies (Soft): WebOS.Cache (CacheGet/CacheSet)  [Recommended] WebOS.Monitor (MonitorMain/Start/End)  [Optional] WebOS.Metric (MetricMain)  [Optional] WebOS.Trace  (TraceMain)   [Optional] WebOS.Log    (LogMain)     [Optional] WebOS.Database (DatabasePing/DatabaseMain) [Optional] WebOS.Queue / WebOS.Worker (QueueMain/WorkerMain) [Optional] WebOS.Security / WebOS.RateLimit (SecurityMain/RateLimitMain) [Optional] ---------------------------------------------------------------------------- Public API (via WebOS Loader): WebOS::Health()         -> Quick summary (safe for public) WebOS::HealthLive()     -> Liveness probe (process alive) WebOS::HealthReady()    -> Readiness probe (can accept traffic) WebOS::HealthScore()    -> Score only WebOS::HealthReport()   -> Standard report (sanitized) WebOS::HealthDeep()     -> Deep report (admin-only recommended) WebOS::HealthProbeList()-> Available probe names WebOS::HealthProbeRun() -> Run a single probe (admin-only recommended) ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Health.php](WebOS/PHP.26.00.00/Engine/WebOS.Health.php)
- **Class**: `WebOS_Health`
- **Facade Entry Points**:
  - `WebOS.Health()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.HealthLive()` → `public static function Live(array $Payload = [], array $Context = [])`
  - `WebOS.HealthReady()` → `public static function Ready(array $Payload = [], array $Context = [])`
  - `WebOS.HealthScore()` → `public static function Score(array $Payload = [], array $Context = [])`
  - `WebOS.HealthReport()` → `public static function Report(array $Payload = [], array $Context = [])`
  - `WebOS.HealthDeep()` → `public static function Deep(array $Payload = [], array $Context = [])`
  - `WebOS.HealthProbeList()` → `public static function ProbeList(array $Payload = [], array $Context = [])`
  - `WebOS.HealthProbeRun()` → `public static function ProbeRun(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Health()` |
| Live | `public static function Live(array $Payload = [], array $Context = [])` | `WebOS.HealthLive()` |
| Ready | `public static function Ready(array $Payload = [], array $Context = [])` | `WebOS.HealthReady()` |
| Score | `public static function Score(array $Payload = [], array $Context = [])` | `WebOS.HealthScore()` |
| Report | `public static function Report(array $Payload = [], array $Context = [])` | `WebOS.HealthReport()` |
| Deep | `public static function Deep(array $Payload = [], array $Context = [])` | `WebOS.HealthDeep()` |
| ProbeList | `public static function ProbeList(array $Payload = [], array $Context = [])` | `WebOS.HealthProbeList()` |
| ProbeRun | `public static function ProbeRun(array $Payload = [], array $Context = [])` | `WebOS.HealthProbeRun()` |
### <span id="webos-healthmonitor"></span>WebOS.HealthMonitor
- **Purpose**: System, App, Tenant & Unit health monitoring Liveness, Readiness & Deep Health checks Metric ingestion with ultra-low overhead SLO breach detection & degrade signalling Acts as "Self-Awareness Kernel" of WebOS
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.HealthMonitor.php](WebOS/PHP.26.00.00/Engine/WebOS.HealthMonitor.php)
- **Class**: `WebOS_HealthMonitor`
- **Facade Entry Points**:
  - `WebOS.HealthMonitor()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.HealthMonitorPing()` → `public static function Ping(array $Payload, array $Context)`
  - `WebOS.HealthMonitorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.HealthMonitorMetric()` → `public static function Metric(array $Payload, array $Context)`
  - `WebOS.HealthMonitorCheck()` → `public static function Check(array $Payload, array $Context)`
  - `WebOS.HealthMonitorSlo()` → `public static function Slo(array $Payload, array $Context)`
  - `WebOS.HealthMonitorDegrade()` → `public static function Degrade(array $Payload, array $Context)`
  - `WebOS.HealthMonitorReport()` → `public static function Report(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.HealthMonitor()` |
| Ping | `public static function Ping(array $Payload, array $Context)` | `WebOS.HealthMonitorPing()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.HealthMonitorStatus()` |
| Metric | `public static function Metric(array $Payload, array $Context)` | `WebOS.HealthMonitorMetric()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.HealthMonitorCheck()` |
| Slo | `public static function Slo(array $Payload, array $Context)` | `WebOS.HealthMonitorSlo()` |
| Degrade | `public static function Degrade(array $Payload, array $Context)` | `WebOS.HealthMonitorDegrade()` |
| Report | `public static function Report(array $Payload, array $Context)` | `WebOS.HealthMonitorReport()` |
### <span id="webos-http"></span>WebOS.HTTP
- **Purpose**: Enterprise-grade HTTP Client Engine for WebOS Secure, observable, multi-tenant outbound HTTP layer SSRF-safe by default Retry, timeout, cache, trace, log support
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.HTTP.php](WebOS/PHP.26.00.00/Engine/WebOS.HTTP.php)
- **Class**: `WebOS_HTTP`
- **Facade Entry Points**:
  - `WebOS.HTTP()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.HTTPGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.HTTPPost()` → `public static function Post(array $Payload, array $Context)`
  - `WebOS.HTTPPut()` → `public static function Put(array $Payload, array $Context)`
  - `WebOS.HTTPPatch()` → `public static function Patch(array $Payload, array $Context)`
  - `WebOS.HTTPDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.HTTPHead()` → `public static function Head(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.HTTP()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.HTTPGet()` |
| Post | `public static function Post(array $Payload, array $Context)` | `WebOS.HTTPPost()` |
| Put | `public static function Put(array $Payload, array $Context)` | `WebOS.HTTPPut()` |
| Patch | `public static function Patch(array $Payload, array $Context)` | `WebOS.HTTPPatch()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.HTTPDelete()` |
| Head | `public static function Head(array $Payload, array $Context)` | `WebOS.HTTPHead()` |
### <span id="webos-identity"></span>WebOS.Identity
- **Purpose**: Enterprise-grade Identity Fabric for WebOS (Multi-App + Multi-Tenant) Login / Register / Session / Token / OTP orchestration via WebOS Engines Centralized policy: Auth, Role, Permission, Device Trust (future-ready) Safe caching + scoped keys to prevent data leak across users/roles/tenants
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Identity.php](WebOS/PHP.26.00.00/Engine/WebOS.Identity.php)
- **Class**: `WebOS_Identity`
- **Facade Entry Points**:
  - `WebOS.Identity()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityScope()` → `public static function Scope(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityRegister()` → `public static function Register(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityLogin()` → `public static function Login(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityLogout()` → `public static function Logout(array $Payload = [], array $Context = [])`
  - `WebOS.IdentitySessionGet()` → `public static function SessionGet(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityTokenIssue()` → `public static function TokenIssue(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityTokenVerify()` → `public static function TokenVerify(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityOtpSend()` → `public static function OtpSend(array $Payload = [], array $Context = [])`
  - `WebOS.IdentityOtpVerify()` → `public static function OtpVerify(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Identity()` |
| Scope | `public static function Scope(array $Payload = [], array $Context = [])` | `WebOS.IdentityScope()` |
| Register | `public static function Register(array $Payload = [], array $Context = [])` | `WebOS.IdentityRegister()` |
| Login | `public static function Login(array $Payload = [], array $Context = [])` | `WebOS.IdentityLogin()` |
| Logout | `public static function Logout(array $Payload = [], array $Context = [])` | `WebOS.IdentityLogout()` |
| SessionGet | `public static function SessionGet(array $Payload = [], array $Context = [])` | `WebOS.IdentitySessionGet()` |
| TokenIssue | `public static function TokenIssue(array $Payload = [], array $Context = [])` | `WebOS.IdentityTokenIssue()` |
| TokenVerify | `public static function TokenVerify(array $Payload = [], array $Context = [])` | `WebOS.IdentityTokenVerify()` |
| OtpSend | `public static function OtpSend(array $Payload = [], array $Context = [])` | `WebOS.IdentityOtpSend()` |
| OtpVerify | `public static function OtpVerify(array $Payload = [], array $Context = [])` | `WebOS.IdentityOtpVerify()` |
### <span id="webos-index"></span>WebOS.Index
- **Purpose**: Central Index Intelligence Layer for WebOS Fast Unit Resolution (Exact + Wildcard) Cache Index & Scope Safety Index Registry, Health, and Audit
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Index.php](WebOS/PHP.26.00.00/Engine/WebOS.Index.php)
- **Class**: `WebOS_Index`
- **Facade Entry Points**:
  - `WebOS.Index()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.IndexBuild()` → `public static function Build(array $Payload, array $Context)`
  - `WebOS.IndexRebuild()` → `public static function Rebuild(array $Payload, array $Context)`
  - `WebOS.IndexUnitResolveFast()` → `public static function UnitResolveFast(array $Payload, array $Context)`
  - `WebOS.IndexCachePurge()` → `public static function CachePurge(array $Payload, array $Context)`
  - `WebOS.IndexHealth()` → `public static function Health(array $Payload, array $Context)`
  - `WebOS.IndexStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Index()` |
| Build | `public static function Build(array $Payload, array $Context)` | `WebOS.IndexBuild()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `WebOS.IndexRebuild()` |
| UnitResolveFast | `public static function UnitResolveFast(array $Payload, array $Context)` | `WebOS.IndexUnitResolveFast()` |
| CachePurge | `public static function CachePurge(array $Payload, array $Context)` | `WebOS.IndexCachePurge()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.IndexHealth()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `WebOS.IndexStats()` |
### <span id="webos-indexer"></span>WebOS.Indexer
- **Purpose**: Universal indexing engine for all data types (Post, Page, Product, User…) Multi-App + Multi-Tenant safe indexing Incremental, idempotent, audit-ready indexing Search-backend agnostic (DB / External / Hybrid)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Indexer.php](WebOS/PHP.26.00.00/Engine/WebOS.Indexer.php)
- **Class**: `WebOS_Indexer`
- **Facade Entry Points**:
  - `WebOS.Indexer()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.IndexerIndex()` → `public static function Index(array $Payload, array $Context)`
  - `WebOS.IndexerDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.IndexerRebuild()` → `public static function Rebuild(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Indexer()` |
| Index | `public static function Index(array $Payload, array $Context)` | `WebOS.IndexerIndex()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.IndexerDelete()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `WebOS.IndexerRebuild()` |
### <span id="webos-insurance"></span>WebOS.Insurance
- **Purpose**: Universal Insurance Engine for Multi-App + Multi-Tenant WebOS Covers: Policies, Plans, Claims, Payouts, Audit, Documents, Integrations Designed as "Coverage Contract OS Layer" (not just insurance module)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Insurance.php](WebOS/PHP.26.00.00/Engine/WebOS.Insurance.php)
- **Class**: `WebOS_Insurance`
- **Facade Entry Points**:
  - `WebOS.Insurance()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.InsuranceProviderNew()` → `public static function ProviderNew(array $Payload, array $Context)`
  - `WebOS.InsuranceProductNew()` → `public static function ProductNew(array $Payload, array $Context)`
  - `WebOS.InsurancePlanNew()` → `public static function PlanNew(array $Payload, array $Context)`
  - `WebOS.InsurancePlanGet()` → `public static function PlanGet(array $Payload, array $Context)`
  - `WebOS.InsurancePolicyQuote()` → `public static function PolicyQuote(array $Payload, array $Context)`
  - `WebOS.InsurancePolicyIssue()` → `public static function PolicyIssue(array $Payload, array $Context)`
  - `WebOS.InsurancePolicyCancel()` → `public static function PolicyCancel(array $Payload, array $Context)`
  - `WebOS.InsuranceClaimNew()` → `public static function ClaimNew(array $Payload, array $Context)`
  - `WebOS.InsuranceClaimSubmit()` → `public static function ClaimSubmit(array $Payload, array $Context)`
  - `WebOS.InsuranceClaimApprove()` → `public static function ClaimApprove(array $Payload, array $Context)`
  - `WebOS.InsuranceClaimReject()` → `public static function ClaimReject(array $Payload, array $Context)`
  - `WebOS.InsuranceClaimPayout()` → `public static function ClaimPayout(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Insurance()` |
| ProviderNew | `public static function ProviderNew(array $Payload, array $Context)` | `WebOS.InsuranceProviderNew()` |
| ProductNew | `public static function ProductNew(array $Payload, array $Context)` | `WebOS.InsuranceProductNew()` |
| PlanNew | `public static function PlanNew(array $Payload, array $Context)` | `WebOS.InsurancePlanNew()` |
| PlanGet | `public static function PlanGet(array $Payload, array $Context)` | `WebOS.InsurancePlanGet()` |
| PolicyQuote | `public static function PolicyQuote(array $Payload, array $Context)` | `WebOS.InsurancePolicyQuote()` |
| PolicyIssue | `public static function PolicyIssue(array $Payload, array $Context)` | `WebOS.InsurancePolicyIssue()` |
| PolicyCancel | `public static function PolicyCancel(array $Payload, array $Context)` | `WebOS.InsurancePolicyCancel()` |
| ClaimNew | `public static function ClaimNew(array $Payload, array $Context)` | `WebOS.InsuranceClaimNew()` |
| ClaimSubmit | `public static function ClaimSubmit(array $Payload, array $Context)` | `WebOS.InsuranceClaimSubmit()` |
| ClaimApprove | `public static function ClaimApprove(array $Payload, array $Context)` | `WebOS.InsuranceClaimApprove()` |
| ClaimReject | `public static function ClaimReject(array $Payload, array $Context)` | `WebOS.InsuranceClaimReject()` |
| ClaimPayout | `public static function ClaimPayout(array $Payload, array $Context)` | `WebOS.InsuranceClaimPayout()` |
### <span id="webos-inventory"></span>WebOS.Inventory
- **Purpose**: Enterprise-grade Inventory Operating Engine (Multi-App, Multi-Tenant) Ledger-first design (truth = immutable ledger; balance = projection) Oversell protection via reserve/commit lifecycle Idempotent write operations (safe retries / webhooks) Fast reads via materialized balance + optional cache
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Inventory.php](WebOS/PHP.26.00.00/Engine/WebOS.Inventory.php)
- **Class**: `WebOS_Inventory`
- **Facade Entry Points**:
  - `WebOS.Inventory()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.InventoryGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.InventorySummary()` → `public static function Summary(array $Payload, array $Context)`
  - `WebOS.InventoryLedger()` → `public static function Ledger(array $Payload, array $Context)`
  - `WebOS.InventoryAdjust()` → `public static function Adjust(array $Payload, array $Context)`
  - `WebOS.InventoryReserve()` → `public static function Reserve(array $Payload, array $Context)`
  - `WebOS.InventoryRelease()` → `public static function Release(array $Payload, array $Context)`
  - `WebOS.InventoryCommit()` → `public static function Commit(array $Payload, array $Context)`
  - `WebOS.InventoryTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `WebOS.InventoryReceive()` → `public static function Receive(array $Payload, array $Context)`
  - `WebOS.InventoryReconcile()` → `public static function Reconcile(array $Payload, array $Context)`
  - `WebOS.InventoryHealth()` → `public static function Health(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Inventory()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.InventoryGet()` |
| Summary | `public static function Summary(array $Payload, array $Context)` | `WebOS.InventorySummary()` |
| Ledger | `public static function Ledger(array $Payload, array $Context)` | `WebOS.InventoryLedger()` |
| Adjust | `public static function Adjust(array $Payload, array $Context)` | `WebOS.InventoryAdjust()` |
| Reserve | `public static function Reserve(array $Payload, array $Context)` | `WebOS.InventoryReserve()` |
| Release | `public static function Release(array $Payload, array $Context)` | `WebOS.InventoryRelease()` |
| Commit | `public static function Commit(array $Payload, array $Context)` | `WebOS.InventoryCommit()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `WebOS.InventoryTransfer()` |
| Receive | `public static function Receive(array $Payload, array $Context)` | `WebOS.InventoryReceive()` |
| Reconcile | `public static function Reconcile(array $Payload, array $Context)` | `WebOS.InventoryReconcile()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.InventoryHealth()` |
### <span id="webos-invoice"></span>WebOS.Invoice
- **Purpose**: Enterprise-grade Invoicing Operating Layer for WebOS Multi-App + Multi-Tenant + High-Performance + Audit-Proof Strict Loader/Router compatibility (WebOS::InvoiceCreate(), etc.) ---------------------------------------------------------------------------- DESIGN RULES (AliensGrade): This is an Engine (no boot logic) All methods are static Main() is mandatory fallback Uses WebOS Engines for cross-system work (DB/Cache/Security/Lock/etc.) Deny-by-default, strict validation, idempotency, audit trail ----------------------------------------------------------------------------
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Invoice.php](WebOS/PHP.26.00.00/Engine/WebOS.Invoice.php)
- **Class**: `WebOS_Invoice`
- **Facade Entry Points**:
  - `WebOS.Invoice()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.InvoiceCreate()` → `public static function Create(array $Payload, array $Context)`
  - `WebOS.InvoiceUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.InvoiceIssue()` → `public static function Issue(array $Payload, array $Context)`
  - `WebOS.InvoiceSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.InvoicePay()` → `public static function Pay(array $Payload, array $Context)`
  - `WebOS.InvoiceVoid()` → `public static function Void(array $Payload, array $Context)`
  - `WebOS.InvoiceView()` → `public static function View(array $Payload, array $Context)`
  - `WebOS.InvoiceList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.InvoiceSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.InvoicePdf()` → `public static function Pdf(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Invoice()` |
| Create | `public static function Create(array $Payload, array $Context)` | `WebOS.InvoiceCreate()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.InvoiceUpdate()` |
| Issue | `public static function Issue(array $Payload, array $Context)` | `WebOS.InvoiceIssue()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.InvoiceSend()` |
| Pay | `public static function Pay(array $Payload, array $Context)` | `WebOS.InvoicePay()` |
| Void | `public static function Void(array $Payload, array $Context)` | `WebOS.InvoiceVoid()` |
| View | `public static function View(array $Payload, array $Context)` | `WebOS.InvoiceView()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.InvoiceList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.InvoiceSearch()` |
| Pdf | `public static function Pdf(array $Payload, array $Context)` | `WebOS.InvoicePdf()` |
### <span id="webos-knowledge"></span>WebOS.Knowledge
- **Purpose**: Central Knowledge Engine for WebOS Multi-App, Multi-Tenant, Enterprise Knowledge Layer Facts, Documents, Entities, Relations, Graph & AI-ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Knowledge.php](WebOS/PHP.26.00.00/Engine/WebOS.Knowledge.php)
- **Class**: `WebOS_Knowledge`
- **Facade Entry Points**:
  - `WebOS.Knowledge()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.KnowledgePut()` → `public static function Put(array $Payload, array $Context)`
  - `WebOS.KnowledgeGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.KnowledgeFind()` → `public static function Find(array $Payload, array $Context)`
  - `WebOS.KnowledgeRelate()` → `public static function Relate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Knowledge()` |
| Put | `public static function Put(array $Payload, array $Context)` | `WebOS.KnowledgePut()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.KnowledgeGet()` |
| Find | `public static function Find(array $Payload, array $Context)` | `WebOS.KnowledgeFind()` |
| Relate | `public static function Relate(array $Payload, array $Context)` | `WebOS.KnowledgeRelate()` |
### <span id="webos-knowledgegraph"></span>WebOS.KnowledgeGraph
- **Purpose**: Enterprise-grade Knowledge Graph Engine for WebOS Multi-App + Multi-Tenant semantic data layer Acts as intelligence backbone for Search, AI, Recommendation, Automation
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.KnowledgeGraph.php](WebOS/PHP.26.00.00/Engine/WebOS.KnowledgeGraph.php)
- **Class**: `WebOS_KnowledgeGraph`
- **Facade Entry Points**:
  - `WebOS.KnowledgeGraph()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.KnowledgeGraphNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.KnowledgeGraphUpsert()` → `public static function Upsert(array $Payload, array $Context)`
  - `WebOS.KnowledgeGraphLink()` → `public static function Link(array $Payload, array $Context)`
  - `WebOS.KnowledgeGraphNeighbors()` → `public static function Neighbors(array $Payload, array $Context)`
  - `WebOS.KnowledgeGraphExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.KnowledgeGraph()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.KnowledgeGraphNew()` |
| Upsert | `public static function Upsert(array $Payload, array $Context)` | `WebOS.KnowledgeGraphUpsert()` |
| Link | `public static function Link(array $Payload, array $Context)` | `WebOS.KnowledgeGraphLink()` |
| Neighbors | `public static function Neighbors(array $Payload, array $Context)` | `WebOS.KnowledgeGraphNeighbors()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.KnowledgeGraphExplain()` |
### <span id="webos-labreport"></span>WebOS.LabReport
- **Purpose**: Enterprise-grade Lab Report lifecycle engine Multi-App + Multi-Tenant + High-Security medical data handling Works as core backend module for Hospital / Lab / Health platforms
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.LabReport.php](WebOS/PHP.26.00.00/Engine/WebOS.LabReport.php)
- **Class**: `WebOS_LabReport`
- **Facade Entry Points**:
  - `WebOS.LabReport()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LabReportNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.LabReportGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.LabReportVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.LabReportPublish()` → `public static function Publish(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.LabReport()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.LabReportNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.LabReportGet()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.LabReportVerify()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.LabReportPublish()` |
### <span id="webos-learning"></span>WebOS.Learning
- **Purpose**: WebOS ka OS-level Learning Engine (Observe → Learn → Suggest/Act → Verify) Multi-App + Multi-Tenant compatible (Context based scoping) High-performance, async-first design (Queue/Worker friendly) Deterministic + Auditable learning (Audit + Model Registry + Versioning)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Learning.php](WebOS/PHP.26.00.00/Engine/WebOS.Learning.php)
- **Class**: `WebOS_Learning`
- **Facade Entry Points**:
  - `WebOS.Learning()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.LearningIngest()` → `public static function Ingest(array $Payload = [], array $Context = [])`
  - `WebOS.LearningTrain()` → `public static function Train(array $Payload = [], array $Context = [])`
  - `WebOS.LearningPredict()` → `public static function Predict(array $Payload = [], array $Context = [])`
  - `WebOS.LearningPredictCompute()` → `public static function PredictCompute(array $Payload = [], array $Context = [])`
  - `WebOS.LearningRecommend()` → `public static function Recommend(array $Payload = [], array $Context = [])`
  - `WebOS.LearningRecommendCompute()` → `public static function RecommendCompute(array $Payload = [], array $Context = [])`
  - `WebOS.LearningAnomaly()` → `public static function Anomaly(array $Payload = [], array $Context = [])`
  - `WebOS.LearningOptimize()` → `public static function Optimize(array $Payload = [], array $Context = [])`
  - `WebOS.LearningPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `WebOS.LearningStatus()` → `public static function Status(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Learning()` |
| Ingest | `public static function Ingest(array $Payload = [], array $Context = [])` | `WebOS.LearningIngest()` |
| Train | `public static function Train(array $Payload = [], array $Context = [])` | `WebOS.LearningTrain()` |
| Predict | `public static function Predict(array $Payload = [], array $Context = [])` | `WebOS.LearningPredict()` |
| PredictCompute | `public static function PredictCompute(array $Payload = [], array $Context = [])` | `WebOS.LearningPredictCompute()` |
| Recommend | `public static function Recommend(array $Payload = [], array $Context = [])` | `WebOS.LearningRecommend()` |
| RecommendCompute | `public static function RecommendCompute(array $Payload = [], array $Context = [])` | `WebOS.LearningRecommendCompute()` |
| Anomaly | `public static function Anomaly(array $Payload = [], array $Context = [])` | `WebOS.LearningAnomaly()` |
| Optimize | `public static function Optimize(array $Payload = [], array $Context = [])` | `WebOS.LearningOptimize()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `WebOS.LearningPolicy()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `WebOS.LearningStatus()` |
### <span id="webos-leave"></span>WebOS.Leave
- **Purpose**: Enterprise-grade Leave Management Engine Multi-App, Multi-Tenant, Policy-driven Fully compatible with WebOS.Loader & EngineRouter
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Leave.php](WebOS/PHP.26.00.00/Engine/WebOS.Leave.php)
- **Class**: `WebOS_Leave`
- **Facade Entry Points**:
  - `WebOS.Leave()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.LeaveApply()` → `public static function Apply(array $Payload = [], array $Context = [])`
  - `WebOS.LeaveApprove()` → `public static function Approve(array $Payload = [], array $Context = [])`
  - `WebOS.LeaveReject()` → `public static function Reject(array $Payload = [], array $Context = [])`
  - `WebOS.LeaveBalance()` → `public static function Balance(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Leave()` |
| Apply | `public static function Apply(array $Payload = [], array $Context = [])` | `WebOS.LeaveApply()` |
| Approve | `public static function Approve(array $Payload = [], array $Context = [])` | `WebOS.LeaveApprove()` |
| Reject | `public static function Reject(array $Payload = [], array $Context = [])` | `WebOS.LeaveReject()` |
| Balance | `public static function Balance(array $Payload = [], array $Context = [])` | `WebOS.LeaveBalance()` |
### <span id="webos-ledger"></span>WebOS.Ledger
- **Purpose**: Immutable, enterprise-grade ledger engine Multi-App + Multi-Tenant financial journal Append-only, idempotent, audit-ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Ledger.php](WebOS/PHP.26.00.00/Engine/WebOS.Ledger.php)
- **Class**: `WebOS_Ledger`
- **Facade Entry Points**:
  - `WebOS.Ledger()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LedgerPost()` → `public static function Post(array $Payload, array $Context)`
  - `WebOS.LedgerQuery()` → `public static function Query(array $Payload, array $Context)`
  - `WebOS.LedgerBalance()` → `public static function Balance(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Ledger()` |
| Post | `public static function Post(array $Payload, array $Context)` | `WebOS.LedgerPost()` |
| Query | `public static function Query(array $Payload, array $Context)` | `WebOS.LedgerQuery()` |
| Balance | `public static function Balance(array $Payload, array $Context)` | `WebOS.LedgerBalance()` |
### <span id="webos-lesson"></span>WebOS.Lesson
- **Purpose**: Universal Lesson Engine for WebOS OS-level Lesson primitive (not LMS-only) Multi-App + Multi-Tenant + Multi-Brand compatible
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Lesson.php](WebOS/PHP.26.00.00/Engine/WebOS.Lesson.php)
- **Class**: `WebOS_Lesson`
- **Facade Entry Points**:
  - `WebOS.Lesson()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LessonNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.LessonGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.LessonList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.LessonPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `WebOS.LessonProgressSet()` → `public static function ProgressSet(array $Payload, array $Context)`
  - `WebOS.LessonDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Lesson()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.LessonNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.LessonGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.LessonList()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.LessonPublish()` |
| ProgressSet | `public static function ProgressSet(array $Payload, array $Context)` | `WebOS.LessonProgressSet()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.LessonDelete()` |
### <span id="webos-like"></span>WebOS.Like
- **Purpose**: Universal Like / Unlike / Reaction engine Works with ANY entity (Post, Comment, Product, Video, Profile, etc.) Multi-App + Multi-Tenant + Enterprise Grade
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Like.php](WebOS/PHP.26.00.00/Engine/WebOS.Like.php)
- **Class**: `WebOS_Like`
- **Facade Entry Points**:
  - `WebOS.Like()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Like()` |
### <span id="webos-linter"></span>WebOS.Linter
- **Purpose**: Enterprise-grade static code analysis engine for WebOS Enforces AliensStyle + AliensGrade contracts Validates Engine, Boot, Unit, SDK, App, Brand code Acts as Quality Firewall before Release
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Linter.php](WebOS/PHP.26.00.00/Engine/WebOS.Linter.php)
- **Class**: `WebOS_Linter`
- **Facade Entry Points**:
  - `WebOS.Linter()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LinterScan()` → `public static function Scan(array $Payload, array $Context)`
  - `WebOS.LinterFile()` → `public static function File(array $Payload, array $Context)`
  - `WebOS.LinterEngine()` → `public static function Engine(array $Payload, array $Context)`
  - `WebOS.LinterReport()` → `public static function Report(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Linter()` |
| Scan | `public static function Scan(array $Payload, array $Context)` | `WebOS.LinterScan()` |
| File | `public static function File(array $Payload, array $Context)` | `WebOS.LinterFile()` |
| Engine | `public static function Engine(array $Payload, array $Context)` | `WebOS.LinterEngine()` |
| Report | `public static function Report(array $Payload, array $Context)` | `WebOS.LinterReport()` |
### <span id="webos-loadbalancer"></span>WebOS.LoadBalancer
- **Purpose**: Logical load balancing for WebOS backend OS Multi-App, Multi-Tenant, Multi-Cluster routing High-performance decision engine (app-level LB) Zero-downtime deployments (Canary, Blue/Green)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.LoadBalancer.php](WebOS/PHP.26.00.00/Engine/WebOS.LoadBalancer.php)
- **Class**: `WebOS_LoadBalancer`
- **Facade Entry Points**:
  - `WebOS.LoadBalancer()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.LoadBalancerDecide()` → `public static function Decide(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.LoadBalancer()` |
| Decide | `public static function Decide(array $Payload = [], array $Context = [])` | `WebOS.LoadBalancerDecide()` |
### <span id="webos-locale"></span>WebOS.Locale
- **Purpose**: Enterprise-grade Locale Operating System layer for WebOS Multi-App + Multi-Brand + Multi-Tenant locale resolution + formatting Safe, deterministic, cached locale context resolution Translation dictionaries (file-first) with strict fallback chain
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Locale.php](WebOS/PHP.26.00.00/Engine/WebOS.Locale.php)
- **Class**: `WebOS_Locale`
- **Facade Entry Points**:
  - `WebOS.Locale()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleTranslate()` → `public static function Translate(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleFormatDate()` → `public static function FormatDate(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleFormatNumber()` → `public static function FormatNumber(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleSupported()` → `public static function Supported(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleNormalize()` → `public static function Normalize(array $Payload = [], array $Context = [])`
  - `WebOS.LocaleFlush()` → `public static function Flush(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Locale()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.LocaleResolve()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.LocaleGet()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `WebOS.LocaleSet()` |
| Translate | `public static function Translate(array $Payload = [], array $Context = [])` | `WebOS.LocaleTranslate()` |
| FormatDate | `public static function FormatDate(array $Payload = [], array $Context = [])` | `WebOS.LocaleFormatDate()` |
| FormatNumber | `public static function FormatNumber(array $Payload = [], array $Context = [])` | `WebOS.LocaleFormatNumber()` |
| Supported | `public static function Supported(array $Payload = [], array $Context = [])` | `WebOS.LocaleSupported()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `WebOS.LocaleValidate()` |
| Normalize | `public static function Normalize(array $Payload = [], array $Context = [])` | `WebOS.LocaleNormalize()` |
| Flush | `public static function Flush(array $Payload = [], array $Context = [])` | `WebOS.LocaleFlush()` |
### <span id="webos-location"></span>WebOS.Location
- **Purpose**: Enterprise-grade Location & Geo-Intelligence Engine Unified location resolution for API, Web, Mobile, SaaS Multi-App + Multi-Tenant aware Security, Privacy & Performance first
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Location.php](WebOS/PHP.26.00.00/Engine/WebOS.Location.php)
- **Class**: `WebOS_Location`
- **Facade Entry Points**:
  - `WebOS.Location()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LocationGet()` → `public static function Get(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Location()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.LocationGet()` |
### <span id="webos-lock"></span>WebOS.Lock
- **Purpose**: Distributed Lock Engine for WebOS Kernel Prevent race conditions, double execution, data corruption Support API, Cron, Queue, Worker, Task, EventBus
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Lock.php](WebOS/PHP.26.00.00/Engine/WebOS.Lock.php)
- **Class**: `WebOS_Lock`
- **Facade Entry Points**:
  - `WebOS.Lock()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LockAcquire()` → `public static function Acquire(array $Payload, array $Context)`
  - `WebOS.LockTry()` → `public static function Try(array $Payload, array $Context)`
  - `WebOS.LockRelease()` → `public static function Release(array $Payload, array $Context)`
  - `WebOS.LockExtend()` → `public static function Extend(array $Payload, array $Context)`
  - `WebOS.LockStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.LockForceRelease()` → `public static function ForceRelease(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Lock()` |
| Acquire | `public static function Acquire(array $Payload, array $Context)` | `WebOS.LockAcquire()` |
| Try | `public static function Try(array $Payload, array $Context)` | `WebOS.LockTry()` |
| Release | `public static function Release(array $Payload, array $Context)` | `WebOS.LockRelease()` |
| Extend | `public static function Extend(array $Payload, array $Context)` | `WebOS.LockExtend()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.LockStatus()` |
| ForceRelease | `public static function ForceRelease(array $Payload, array $Context)` | `WebOS.LockForceRelease()` |
### <span id="webos-log"></span>WebOS.Log
- **Purpose**: Enterprise-grade structured logging backbone for WebOS Multi-App + Multi-Tenant safe (App/Brand/Ecosystem/User/RequestId) High-performance buffered writes with safe flush JSONL file sink by default (fast + index-friendly) PII/Secret redaction (tokens/password/cookie/etc.) Audit/Security channels, request start/end timing support
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Log.php](WebOS/PHP.26.00.00/Engine/WebOS.Log.php)
- **Class**: `WebOS_Log`
- **Facade Entry Points**:
  - `WebOS.Log()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LogWrite()` → `public static function Write(array $Payload, array $Context)`
  - `WebOS.LogDebug()` → `public static function Debug(array $Payload, array $Context)`
  - `WebOS.LogInfo()` → `public static function Info(array $Payload, array $Context)`
  - `WebOS.LogNotice()` → `public static function Notice(array $Payload, array $Context)`
  - `WebOS.LogWarning()` → `public static function Warning(array $Payload, array $Context)`
  - `WebOS.LogError()` → `public static function Error(array $Payload, array $Context)`
  - `WebOS.LogCritical()` → `public static function Critical(array $Payload, array $Context)`
  - `WebOS.LogAlert()` → `public static function Alert(array $Payload, array $Context)`
  - `WebOS.LogEmergency()` → `public static function Emergency(array $Payload, array $Context)`
  - `WebOS.LogSecurity()` → `public static function Security(array $Payload, array $Context)`
  - `WebOS.LogAudit()` → `public static function Audit(array $Payload, array $Context)`
  - `WebOS.LogRequestStart()` → `public static function RequestStart(array $Payload, array $Context)`
  - `WebOS.LogRequestEnd()` → `public static function RequestEnd(array $Payload, array $Context)`
  - `WebOS.LogFlush()` → `public static function Flush(array $Payload, array $Context)`
  - `WebOS.LogConfigGet()` → `public static function ConfigGet(array $Payload, array $Context)`
  - `WebOS.LogConfigSet()` → `public static function ConfigSet(array $Payload, array $Context)`
  - `WebOS.LogScope()` → `public static function Scope(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Log()` |
| Write | `public static function Write(array $Payload, array $Context)` | `WebOS.LogWrite()` |
| Debug | `public static function Debug(array $Payload, array $Context)` | `WebOS.LogDebug()` |
| Info | `public static function Info(array $Payload, array $Context)` | `WebOS.LogInfo()` |
| Notice | `public static function Notice(array $Payload, array $Context)` | `WebOS.LogNotice()` |
| Warning | `public static function Warning(array $Payload, array $Context)` | `WebOS.LogWarning()` |
| Error | `public static function Error(array $Payload, array $Context)` | `WebOS.LogError()` |
| Critical | `public static function Critical(array $Payload, array $Context)` | `WebOS.LogCritical()` |
| Alert | `public static function Alert(array $Payload, array $Context)` | `WebOS.LogAlert()` |
| Emergency | `public static function Emergency(array $Payload, array $Context)` | `WebOS.LogEmergency()` |
| Security | `public static function Security(array $Payload, array $Context)` | `WebOS.LogSecurity()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `WebOS.LogAudit()` |
| RequestStart | `public static function RequestStart(array $Payload, array $Context)` | `WebOS.LogRequestStart()` |
| RequestEnd | `public static function RequestEnd(array $Payload, array $Context)` | `WebOS.LogRequestEnd()` |
| Flush | `public static function Flush(array $Payload, array $Context)` | `WebOS.LogFlush()` |
| ConfigGet | `public static function ConfigGet(array $Payload, array $Context)` | `WebOS.LogConfigGet()` |
| ConfigSet | `public static function ConfigSet(array $Payload, array $Context)` | `WebOS.LogConfigSet()` |
| Scope | `public static function Scope(array $Payload, array $Context)` | `WebOS.LogScope()` |
### <span id="webos-login"></span>WebOS.Login
- **Purpose**: Enterprise-grade Login Engine for WebOS Multi-App, Multi-Tenant, Secure Authentication Gateway Acts as Identity Entry Point (Password-based v1)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Login.php](WebOS/PHP.26.00.00/Engine/WebOS.Login.php)
- **Class**: `WebOS_Login`
- **Facade Entry Points**:
  - `WebOS.Login()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.LoginStart()` → `public static function Start(array $Payload, array $Context)`
  - `WebOS.LoginPassword()` → `public static function Password(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Login()` |
| Start | `public static function Start(array $Payload, array $Context)` | `WebOS.LoginStart()` |
| Password | `public static function Password(array $Payload, array $Context)` | `WebOS.LoginPassword()` |
### <span id="webos-logout"></span>WebOS.Logout
- **Purpose**: Enterprise-grade Logout & Access Revocation Engine Multi-App, Multi-Tenant, Multi-Device compatible Token, Session, Cache & Permission invalidation Policy, Security & Audit integrated
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Logout.php](WebOS/PHP.26.00.00/Engine/WebOS.Logout.php)
- **Class**: `WebOS_Logout`
- **Facade Entry Points**:
  - `WebOS.Logout()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.LogoutNow()` → `public static function Now(array $Payload, array $Context)`
  - `WebOS.LogoutAll()` → `public static function All(array $Payload, array $Context)`
  - `WebOS.LogoutDevice()` → `public static function Device(array $Payload, array $Context)`
  - `WebOS.LogoutTenant()` → `public static function Tenant(array $Payload, array $Context)`
  - `WebOS.LogoutApp()` → `public static function App(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Logout()` |
| Now | `public static function Now(array $Payload, array $Context)` | `WebOS.LogoutNow()` |
| All | `public static function All(array $Payload, array $Context)` | `WebOS.LogoutAll()` |
| Device | `public static function Device(array $Payload, array $Context)` | `WebOS.LogoutDevice()` |
| Tenant | `public static function Tenant(array $Payload, array $Context)` | `WebOS.LogoutTenant()` |
| App | `public static function App(array $Payload, array $Context)` | `WebOS.LogoutApp()` |
### <span id="webos-manifest"></span>WebOS.Manifest
- **Purpose**: Single Source Of Truth (SSOT) for all configuration Enterprise-grade Manifest engine for: Multi-App Multi-Brand Multi-Tenant Multi-Version WebOS Load + Merge + Validate config layers: Global → WebOS → Brand → App → Module → Tenant → Override High-Performance with local + Redis cache support
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Manifest.php](WebOS/PHP.26.00.00/Engine/WebOS.Manifest.php)
- **Class**: `WebOS_Manifest`
- **Facade Entry Points**:
  - `WebOS.Manifest()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ManifestGet()` → `public static function Get(array $Payload = [], array $Context = []): array`
  - `WebOS.ManifestValue()` → `public static function Value(array $Payload = [], array $Context = [])`
  - `WebOS.ManifestReload()` → `public static function Reload(array $Payload = [], array $Context = []): bool`
  - `WebOS.ManifestDescribe()` → `public static function Describe(array $Payload = [], array $Context = []): array`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Manifest()` |
| Get | `public static function Get(array $Payload = [], array $Context = []): array` | `WebOS.ManifestGet()` |
| Value | `public static function Value(array $Payload = [], array $Context = [])` | `WebOS.ManifestValue()` |
| Reload | `public static function Reload(array $Payload = [], array $Context = []): bool` | `WebOS.ManifestReload()` |
| Describe | `public static function Describe(array $Payload = [], array $Context = []): array` | `WebOS.ManifestDescribe()` |
### <span id="webos-media"></span>WebOS.Media
- **Purpose**: Unified Media Engine for WebOS Backend OS Upload, Store, Secure, Deliver, Audit media assets Multi-App + Multi-Tenant safe by design
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Media.php](WebOS/PHP.26.00.00/Engine/WebOS.Media.php)
- **Class**: `WebOS_Media`
- **Facade Entry Points**:
  - `WebOS.Media()` → `public static function Main($Payload, $Context)`
  - `WebOS.MediaNew()` → `public static function New($Payload, $Context)`
  - `WebOS.MediaGet()` → `public static function Get($Payload, $Context)`
  - `WebOS.MediaDelete()` → `public static function Delete($Payload, $Context)`
  - `WebOS.MediaURL()` → `public static function URL($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.Media()` |
| New | `public static function New($Payload, $Context)` | `WebOS.MediaNew()` |
| Get | `public static function Get($Payload, $Context)` | `WebOS.MediaGet()` |
| Delete | `public static function Delete($Payload, $Context)` | `WebOS.MediaDelete()` |
| URL | `public static function URL($Payload, $Context)` | `WebOS.MediaURL()` |
### <span id="webos-medicalrecord"></span>WebOS.MedicalRecord
- **Purpose**: Universal Medical Record Engine for WebOS Multi-App + Multi-Tenant + Enterprise Grade Versioned, Audited, Secure Medical Records
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.MedicalRecord.php](WebOS/PHP.26.00.00/Engine/WebOS.MedicalRecord.php)
- **Class**: `WebOS_MedicalRecord`
- **Facade Entry Points**:
  - `WebOS.MedicalRecord()` → `public static function Main($Payload, $Context)`
  - `WebOS.MedicalRecordNew()` → `public static function New($Payload, $Context)`
  - `WebOS.MedicalRecordGet()` → `public static function Get($Payload, $Context)`
  - `WebOS.MedicalRecordUpdate()` → `public static function Update($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.MedicalRecord()` |
| New | `public static function New($Payload, $Context)` | `WebOS.MedicalRecordNew()` |
| Get | `public static function Get($Payload, $Context)` | `WebOS.MedicalRecordGet()` |
| Update | `public static function Update($Payload, $Context)` | `WebOS.MedicalRecordUpdate()` |
### <span id="webos-memory"></span>WebOS.Memory
- **Purpose**: Unified system-level memory engine for WebOS Tier-0 (Request Memory) + Tier-2 (Shared Cache) Strict Tenant + App + Scope isolation Safe, fast, auditable memory substrate for all engines
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Memory.php](WebOS/PHP.26.00.00/Engine/WebOS.Memory.php)
- **Class**: `WebOS_Memory`
- **Facade Entry Points**:
  - `WebOS.Memory()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.MemoryGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.MemorySet()` → `public static function Set(array $Payload, array $Context)`
  - `WebOS.MemoryHas()` → `public static function Has(array $Payload, array $Context)`
  - `WebOS.MemoryDel()` → `public static function Del(array $Payload, array $Context)`
  - `WebOS.MemoryInc()` → `public static function Inc(array $Payload, array $Context)`
  - `WebOS.MemoryDec()` → `public static function Dec(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Memory()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.MemoryGet()` |
| Set | `public static function Set(array $Payload, array $Context)` | `WebOS.MemorySet()` |
| Has | `public static function Has(array $Payload, array $Context)` | `WebOS.MemoryHas()` |
| Del | `public static function Del(array $Payload, array $Context)` | `WebOS.MemoryDel()` |
| Inc | `public static function Inc(array $Payload, array $Context)` | `WebOS.MemoryInc()` |
| Dec | `public static function Dec(array $Payload, array $Context)` | `WebOS.MemoryDec()` |
### <span id="webos-message"></span>WebOS.Message
- **Purpose**: Universal Messaging Engine for WebOS Supports chat, system messages, notifications, workflows Multi-Tenant + Multi-App safe High-performance, async-ready, audit-friendly
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Message.php](WebOS/PHP.26.00.00/Engine/WebOS.Message.php)
- **Class**: `WebOS_Message`
- **Facade Entry Points**:
  - `WebOS.Message()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.MessageThreadNew()` → `public static function ThreadNew(array $Payload, array $Context)`
  - `WebOS.MessageSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.MessageList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.MessageMarkRead()` → `public static function MarkRead(array $Payload, array $Context)`
  - `WebOS.MessageThreadList()` → `public static function ThreadList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Message()` |
| ThreadNew | `public static function ThreadNew(array $Payload, array $Context)` | `WebOS.MessageThreadNew()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.MessageSend()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.MessageList()` |
| MarkRead | `public static function MarkRead(array $Payload, array $Context)` | `WebOS.MessageMarkRead()` |
| ThreadList | `public static function ThreadList(array $Payload, array $Context)` | `WebOS.MessageThreadList()` |
### <span id="webos-metric"></span>WebOS.Metric
- **Purpose**: Enterprise-grade Metrics Engine for WebOS Ultra low-overhead counters, gauges, histograms Multi-App + Multi-Tenant safe instrumentation High-cardinality guard + sampling Async-first flush (Queue/Cache ready)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Metric.php](WebOS/PHP.26.00.00/Engine/WebOS.Metric.php)
- **Class**: `WebOS_Metric`
- **Facade Entry Points**:
  - `WebOS.Metric()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.MetricInc()` → `public static function Inc(array $Payload, array $Context)`
  - `WebOS.MetricGauge()` → `public static function Gauge(array $Payload, array $Context)`
  - `WebOS.MetricObs()` → `public static function Obs(array $Payload, array $Context)`
  - `WebOS.MetricEvent()` → `public static function Event(array $Payload, array $Context)`
  - `WebOS.MetricFlush()` → `public static function Flush(array $Payload, array $Context)`
  - `WebOS.MetricExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Metric()` |
| Inc | `public static function Inc(array $Payload, array $Context)` | `WebOS.MetricInc()` |
| Gauge | `public static function Gauge(array $Payload, array $Context)` | `WebOS.MetricGauge()` |
| Obs | `public static function Obs(array $Payload, array $Context)` | `WebOS.MetricObs()` |
| Event | `public static function Event(array $Payload, array $Context)` | `WebOS.MetricEvent()` |
| Flush | `public static function Flush(array $Payload, array $Context)` | `WebOS.MetricFlush()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.MetricExport()` |
### <span id="webos-migration"></span>WebOS.Migration
- **Purpose**: Enterprise-grade Migration Engine for WebOS Multi-App + Multi-Tenant schema migration system Deterministic, Idempotent, Audited migrations Loader + EngineRouter compatible
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Migration.php](WebOS/PHP.26.00.00/Engine/WebOS.Migration.php)
- **Class**: `WebOS_Migration`
- **Facade Entry Points**:
  - `WebOS.Migration()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.MigrationStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.MigrationPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.MigrationApply()` → `public static function Apply(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Migration()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.MigrationStatus()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.MigrationPlan()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `WebOS.MigrationApply()` |
### <span id="webos-mock"></span>WebOS.Mock
- **Purpose**: Enterprise-grade Mock Engine for WebOS API / HTTP / DB / Email / SMS / Webhook mocking Multi-App + Multi-Tenant safe mock resolution Deterministic + Chaos testing support
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Mock.php](WebOS/PHP.26.00.00/Engine/WebOS.Mock.php)
- **Class**: `WebOS_Mock`
- **Facade Entry Points**:
  - `WebOS.Mock()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.MockIsOn()` → `public static function IsOn(array $Payload, array $Context)`
  - `WebOS.MockProfileGet()` → `public static function ProfileGet(array $Payload, array $Context)`
  - `WebOS.MockProfileSet()` → `public static function ProfileSet(array $Payload, array $Context)`
  - `WebOS.MockScope()` → `public static function Scope(array $Payload, array $Context)`
  - `WebOS.MockIntercept()` → `public static function Intercept(array $Payload, array $Context)`
  - `WebOS.MockRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.MockSet()` → `public static function Set(array $Payload, array $Context)`
  - `WebOS.MockDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Mock()` |
| IsOn | `public static function IsOn(array $Payload, array $Context)` | `WebOS.MockIsOn()` |
| ProfileGet | `public static function ProfileGet(array $Payload, array $Context)` | `WebOS.MockProfileGet()` |
| ProfileSet | `public static function ProfileSet(array $Payload, array $Context)` | `WebOS.MockProfileSet()` |
| Scope | `public static function Scope(array $Payload, array $Context)` | `WebOS.MockScope()` |
| Intercept | `public static function Intercept(array $Payload, array $Context)` | `WebOS.MockIntercept()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.MockRun()` |
| Set | `public static function Set(array $Payload, array $Context)` | `WebOS.MockSet()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.MockDelete()` |
### <span id="webos-module"></span>WebOS.Module
- **Purpose**: [] WebOS ke liye OS-level Module Registry + ModuleMap builder Multi-App + Multi-Tenant module enable/disable, bind/unbind Ultra-fast Resolve + Units/Map cache (Boot + Executor ko turbo) Dependency-aware (Phase-2 ready), audit-ready, deterministic
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Module.php](WebOS/PHP.26.00.00/Engine/WebOS.Module.php)
- **Class**: `WebOS_Module`
- **Facade Entry Points**:
  - `WebOS.Module()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleList()` → `public static function List(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleEnable()` → `public static function Enable(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleDisable()` → `public static function Disable(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleMap()` → `public static function Map(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleUnits()` → `public static function Units(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleInvalidate()` → `public static function Invalidate(array $Payload = [], array $Context = [])`
  - `WebOS.ModuleWarm()` → `public static function Warm(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Module()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.ModuleGet()` |
| List | `public static function List(array $Payload = [], array $Context = [])` | `WebOS.ModuleList()` |
| Enable | `public static function Enable(array $Payload = [], array $Context = [])` | `WebOS.ModuleEnable()` |
| Disable | `public static function Disable(array $Payload = [], array $Context = [])` | `WebOS.ModuleDisable()` |
| Map | `public static function Map(array $Payload = [], array $Context = [])` | `WebOS.ModuleMap()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.ModuleResolve()` |
| Units | `public static function Units(array $Payload = [], array $Context = [])` | `WebOS.ModuleUnits()` |
| Invalidate | `public static function Invalidate(array $Payload = [], array $Context = [])` | `WebOS.ModuleInvalidate()` |
| Warm | `public static function Warm(array $Payload = [], array $Context = [])` | `WebOS.ModuleWarm()` |
### <span id="webos-monitor"></span>WebOS.Monitor
- **Purpose**: Enterprise-grade observability engine for WebOS Monitor API / Cron / Webhook / Asset / Component lifecycle Measure execution time, memory usage, result state Multi-App + Multi-Tenant aware Ultra-low overhead, fail-safe
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Monitor.php](WebOS/PHP.26.00.00/Engine/WebOS.Monitor.php)
- **Class**: `WebOS_Monitor`
- **Facade Entry Points**:
  - `WebOS.Monitor()` → `public static function Main(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Monitor()` |
### <span id="webos-node"></span>WebOS.Node
- **Purpose**: Distributed Node Management Engine for WebOS Represents a physical / virtual runtime instance (Node) Supports Multi-App + Multi-Tenant execution Provides Node Registration, Heartbeat, Health, Selection Acts as WebOS Runtime Control Plane
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Node.php](WebOS/PHP.26.00.00/Engine/WebOS.Node.php)
- **Class**: `WebOS_Node`
- **Facade Entry Points**:
  - `WebOS.Node()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.NodeRegister()` → `public static function Register(array $Payload, array $Context)`
  - `WebOS.NodeHeartbeat()` → `public static function Heartbeat(array $Payload, array $Context)`
  - `WebOS.NodeHealth()` → `public static function Health(array $Payload, array $Context)`
  - `WebOS.NodePick()` → `public static function Pick(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Node()` |
| Register | `public static function Register(array $Payload, array $Context)` | `WebOS.NodeRegister()` |
| Heartbeat | `public static function Heartbeat(array $Payload, array $Context)` | `WebOS.NodeHeartbeat()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.NodeHealth()` |
| Pick | `public static function Pick(array $Payload, array $Context)` | `WebOS.NodePick()` |
### <span id="webos-oauth"></span>WebOS.OAuth
- **Purpose**: OAuth2 Authorization Server + Client Engine for WebOS Central identity authorization fabric Supports Code Flow + PKCE + Refresh + Introspection Fully Loader + EngineRouter compatible
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.OAuth.php](WebOS/PHP.26.00.00/Engine/WebOS.OAuth.php)
- **Class**: `WebOS_OAuth`
- **Facade Entry Points**:
  - `WebOS.OAuth()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.OAuthAuthorize()` → `public static function Authorize(array $Payload, array $Context)`
  - `WebOS.OAuthToken()` → `public static function Token(array $Payload, array $Context)`
  - `WebOS.OAuthIntrospect()` → `public static function Introspect(array $Payload, array $Context)`
  - `WebOS.OAuthRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `WebOS.OAuthJwks()` → `public static function Jwks(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.OAuth()` |
| Authorize | `public static function Authorize(array $Payload, array $Context)` | `WebOS.OAuthAuthorize()` |
| Token | `public static function Token(array $Payload, array $Context)` | `WebOS.OAuthToken()` |
| Introspect | `public static function Introspect(array $Payload, array $Context)` | `WebOS.OAuthIntrospect()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `WebOS.OAuthRevoke()` |
| Jwks | `public static function Jwks(array $Payload, array $Context)` | `WebOS.OAuthJwks()` |
### <span id="webos-optimize"></span>WebOS.Optimize
- **Purpose**: Self-optimization engine for WebOS Performance, cache, DB, concurrency, degradation planning Multi-App + Multi-Tenant aware optimization
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Optimize.php](WebOS/PHP.26.00.00/Engine/WebOS.Optimize.php)
- **Class**: `WebOS_Optimize`
- **Facade Entry Points**:
  - `WebOS.Optimize()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.OptimizePlan()` → `public static function Plan(array $Payload = [], array $Context = [])`
  - `WebOS.OptimizeBudget()` → `public static function Budget(array $Plan = [], array $Context = [])`
  - `WebOS.OptimizeCachePolicy()` → `public static function CachePolicy(array $Payload = [], array $Context = [])`
  - `WebOS.OptimizeReport()` → `public static function Report(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Optimize()` |
| Plan | `public static function Plan(array $Payload = [], array $Context = [])` | `WebOS.OptimizePlan()` |
| Budget | `public static function Budget(array $Plan = [], array $Context = [])` | `WebOS.OptimizeBudget()` |
| CachePolicy | `public static function CachePolicy(array $Payload = [], array $Context = [])` | `WebOS.OptimizeCachePolicy()` |
| Report | `public static function Report(array $Payload = [], array $Context = [])` | `WebOS.OptimizeReport()` |
### <span id="webos-orchestrator"></span>WebOS.Orchestrator
- **Purpose**: Engine-of-Engines: universal execution orchestration for WebOS Sync + Async dispatch (Run / Enqueue / Dispatch) Pipeline / Workflow runner (step-based orchestration) Enterprise controls: idempotency, locking, retry, policy gates Multi-App + Multi-Tenant safe scoping (Context-driven)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Orchestrator.php](WebOS/PHP.26.00.00/Engine/WebOS.Orchestrator.php)
- **Class**: `WebOS_Orchestrator`
- **Facade Entry Points**:
  - `WebOS.Orchestrator()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.OrchestratorRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.OrchestratorDispatch()` → `public static function Dispatch(array $Payload, array $Context)`
  - `WebOS.OrchestratorEnqueue()` → `public static function Enqueue(array $Payload, array $Context)`
  - `WebOS.OrchestratorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.OrchestratorCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.OrchestratorRetry()` → `public static function Retry(array $Payload, array $Context)`
  - `WebOS.OrchestratorPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.OrchestratorPipeline()` → `public static function Pipeline(array $Payload, array $Context)`
  - `WebOS.OrchestratorWorkflow()` → `public static function Workflow(array $Payload, array $Context)`
  - `WebOS.OrchestratorMetrics()` → `public static function Metrics(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Orchestrator()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.OrchestratorRun()` |
| Dispatch | `public static function Dispatch(array $Payload, array $Context)` | `WebOS.OrchestratorDispatch()` |
| Enqueue | `public static function Enqueue(array $Payload, array $Context)` | `WebOS.OrchestratorEnqueue()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.OrchestratorStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.OrchestratorCancel()` |
| Retry | `public static function Retry(array $Payload, array $Context)` | `WebOS.OrchestratorRetry()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.OrchestratorPlan()` |
| Pipeline | `public static function Pipeline(array $Payload, array $Context)` | `WebOS.OrchestratorPipeline()` |
| Workflow | `public static function Workflow(array $Payload, array $Context)` | `WebOS.OrchestratorWorkflow()` |
| Metrics | `public static function Metrics(array $Payload, array $Context)` | `WebOS.OrchestratorMetrics()` |
### <span id="webos-order"></span>WebOS.Order
- **Purpose**: Enterprise Grade Order Operating Engine (Multi-App + Multi-Tenant) Full Order Lifecycle: New, Get, List, Update, Confirm, Cancel, Pay, Fulfill, Ship, Deliver, Refund, Timeline, Recalc, Export, Import Idempotency + Concurrency + Audit/Event timeline
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Order.php](WebOS/PHP.26.00.00/Engine/WebOS.Order.php)
- **Class**: `WebOS_Order`
- **Facade Entry Points**:
  - `WebOS.Order()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.OrderNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.OrderGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.OrderList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.OrderUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.OrderConfirm()` → `public static function Confirm(array $Payload, array $Context)`
  - `WebOS.OrderCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.OrderPay()` → `public static function Pay(array $Payload, array $Context)`
  - `WebOS.OrderPaymentCallback()` → `public static function PaymentCallback(array $Payload, array $Context)`
  - `WebOS.OrderFulfill()` → `public static function Fulfill(array $Payload, array $Context)`
  - `WebOS.OrderShip()` → `public static function Ship(array $Payload, array $Context)`
  - `WebOS.OrderDeliver()` → `public static function Deliver(array $Payload, array $Context)`
  - `WebOS.OrderRefund()` → `public static function Refund(array $Payload, array $Context)`
  - `WebOS.OrderTimeline()` → `public static function Timeline(array $Payload, array $Context)`
  - `WebOS.OrderRecalc()` → `public static function Recalc(array $Payload, array $Context)`
  - `WebOS.OrderLock()` → `public static function Lock(array $Payload, array $Context)`
  - `WebOS.OrderUnlock()` → `public static function Unlock(array $Payload, array $Context)`
  - `WebOS.OrderExport()` → `public static function Export(array $Payload, array $Context)`
  - `WebOS.OrderImport()` → `public static function Import(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Order()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.OrderNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.OrderGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.OrderList()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.OrderUpdate()` |
| Confirm | `public static function Confirm(array $Payload, array $Context)` | `WebOS.OrderConfirm()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.OrderCancel()` |
| Pay | `public static function Pay(array $Payload, array $Context)` | `WebOS.OrderPay()` |
| PaymentCallback | `public static function PaymentCallback(array $Payload, array $Context)` | `WebOS.OrderPaymentCallback()` |
| Fulfill | `public static function Fulfill(array $Payload, array $Context)` | `WebOS.OrderFulfill()` |
| Ship | `public static function Ship(array $Payload, array $Context)` | `WebOS.OrderShip()` |
| Deliver | `public static function Deliver(array $Payload, array $Context)` | `WebOS.OrderDeliver()` |
| Refund | `public static function Refund(array $Payload, array $Context)` | `WebOS.OrderRefund()` |
| Timeline | `public static function Timeline(array $Payload, array $Context)` | `WebOS.OrderTimeline()` |
| Recalc | `public static function Recalc(array $Payload, array $Context)` | `WebOS.OrderRecalc()` |
| Lock | `public static function Lock(array $Payload, array $Context)` | `WebOS.OrderLock()` |
| Unlock | `public static function Unlock(array $Payload, array $Context)` | `WebOS.OrderUnlock()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.OrderExport()` |
| Import | `public static function Import(array $Payload, array $Context)` | `WebOS.OrderImport()` |
### <span id="webos-organization"></span>WebOS.Organization
- **Purpose**: Enterprise-grade Organization (Tenant) Engine for WebOS Multi-App + Multi-Tenant organization lifecycle, membership, roles, units, settings, policies, audit trails
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Organization.php](WebOS/PHP.26.00.00/Engine/WebOS.Organization.php)
- **Class**: `WebOS_Organization`
- **Facade Entry Points**:
  - `WebOS.Organization()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.OrganizationNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.OrganizationGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.OrganizationUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.OrganizationDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.OrganizationList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.OrganizationSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.OrganizationSwitch()` → `public static function Switch(array $Payload, array $Context)`
  - `WebOS.OrganizationMembers()` → `public static function Members(array $Payload, array $Context)`
  - `WebOS.OrganizationInvite()` → `public static function Invite(array $Payload, array $Context)`
  - `WebOS.OrganizationAcceptInvite()` → `public static function AcceptInvite(array $Payload, array $Context)`
  - `WebOS.OrganizationRemoveMember()` → `public static function RemoveMember(array $Payload, array $Context)`
  - `WebOS.OrganizationChangeRole()` → `public static function ChangeRole(array $Payload, array $Context)`
  - `WebOS.OrganizationUnitNew()` → `public static function UnitNew(array $Payload, array $Context)`
  - `WebOS.OrganizationUnitList()` → `public static function UnitList(array $Payload, array $Context)`
  - `WebOS.OrganizationSettingSet()` → `public static function SettingSet(array $Payload, array $Context)`
  - `WebOS.OrganizationSettingGet()` → `public static function SettingGet(array $Payload, array $Context)`
  - `WebOS.OrganizationPolicySet()` → `public static function PolicySet(array $Payload, array $Context)`
  - `WebOS.OrganizationPolicyGet()` → `public static function PolicyGet(array $Payload, array $Context)`
  - `WebOS.OrganizationAudit()` → `public static function Audit(array $Payload, array $Context)`
  - `WebOS.Organization()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.OrganizationNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.OrganizationGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.OrganizationUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.OrganizationDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.OrganizationList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.OrganizationSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.OrganizationSwitch()` → `public static function Switch(array $Payload, array $Context)`
  - `WebOS.OrganizationMembers()` → `public static function Members(array $Payload, array $Context)`
  - `WebOS.OrganizationInvite()` → `public static function Invite(array $Payload, array $Context)`
  - `WebOS.OrganizationAcceptInvite()` → `public static function AcceptInvite(array $Payload, array $Context)`
  - `WebOS.OrganizationRemoveMember()` → `public static function RemoveMember(array $Payload, array $Context)`
  - `WebOS.OrganizationChangeRole()` → `public static function ChangeRole(array $Payload, array $Context)`
  - `WebOS.OrganizationUnitNew()` → `public static function UnitNew(array $Payload, array $Context)`
  - `WebOS.OrganizationUnitList()` → `public static function UnitList(array $Payload, array $Context)`
  - `WebOS.OrganizationSettingSet()` → `public static function SettingSet(array $Payload, array $Context)`
  - `WebOS.OrganizationSettingGet()` → `public static function SettingGet(array $Payload, array $Context)`
  - `WebOS.OrganizationPolicySet()` → `public static function PolicySet(array $Payload, array $Context)`
  - `WebOS.OrganizationPolicyGet()` → `public static function PolicyGet(array $Payload, array $Context)`
  - `WebOS.OrganizationAudit()` → `public static function Audit(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Organization()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.OrganizationNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.OrganizationGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.OrganizationUpdate()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.OrganizationDelete()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.OrganizationList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.OrganizationSearch()` |
| Switch | `public static function Switch(array $Payload, array $Context)` | `WebOS.OrganizationSwitch()` |
| Members | `public static function Members(array $Payload, array $Context)` | `WebOS.OrganizationMembers()` |
| Invite | `public static function Invite(array $Payload, array $Context)` | `WebOS.OrganizationInvite()` |
| AcceptInvite | `public static function AcceptInvite(array $Payload, array $Context)` | `WebOS.OrganizationAcceptInvite()` |
| RemoveMember | `public static function RemoveMember(array $Payload, array $Context)` | `WebOS.OrganizationRemoveMember()` |
| ChangeRole | `public static function ChangeRole(array $Payload, array $Context)` | `WebOS.OrganizationChangeRole()` |
| UnitNew | `public static function UnitNew(array $Payload, array $Context)` | `WebOS.OrganizationUnitNew()` |
| UnitList | `public static function UnitList(array $Payload, array $Context)` | `WebOS.OrganizationUnitList()` |
| SettingSet | `public static function SettingSet(array $Payload, array $Context)` | `WebOS.OrganizationSettingSet()` |
| SettingGet | `public static function SettingGet(array $Payload, array $Context)` | `WebOS.OrganizationSettingGet()` |
| PolicySet | `public static function PolicySet(array $Payload, array $Context)` | `WebOS.OrganizationPolicySet()` |
| PolicyGet | `public static function PolicyGet(array $Payload, array $Context)` | `WebOS.OrganizationPolicyGet()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `WebOS.OrganizationAudit()` |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Organization()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.OrganizationNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.OrganizationGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.OrganizationUpdate()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.OrganizationDelete()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.OrganizationList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.OrganizationSearch()` |
| Switch | `public static function Switch(array $Payload, array $Context)` | `WebOS.OrganizationSwitch()` |
| Members | `public static function Members(array $Payload, array $Context)` | `WebOS.OrganizationMembers()` |
| Invite | `public static function Invite(array $Payload, array $Context)` | `WebOS.OrganizationInvite()` |
| AcceptInvite | `public static function AcceptInvite(array $Payload, array $Context)` | `WebOS.OrganizationAcceptInvite()` |
| RemoveMember | `public static function RemoveMember(array $Payload, array $Context)` | `WebOS.OrganizationRemoveMember()` |
| ChangeRole | `public static function ChangeRole(array $Payload, array $Context)` | `WebOS.OrganizationChangeRole()` |
| UnitNew | `public static function UnitNew(array $Payload, array $Context)` | `WebOS.OrganizationUnitNew()` |
| UnitList | `public static function UnitList(array $Payload, array $Context)` | `WebOS.OrganizationUnitList()` |
| SettingSet | `public static function SettingSet(array $Payload, array $Context)` | `WebOS.OrganizationSettingSet()` |
| SettingGet | `public static function SettingGet(array $Payload, array $Context)` | `WebOS.OrganizationSettingGet()` |
| PolicySet | `public static function PolicySet(array $Payload, array $Context)` | `WebOS.OrganizationPolicySet()` |
| PolicyGet | `public static function PolicyGet(array $Payload, array $Context)` | `WebOS.OrganizationPolicyGet()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `WebOS.OrganizationAudit()` |
### <span id="webos-page"></span>WebOS.Page
- **Purpose**: Page Operating Engine for WebOS Multi-App + Multi-Brand + Multi-Tenant Page Resolution Security-aware, Cache-aware, AI-ready Page Backend
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Page.php](WebOS/PHP.26.00.00/Engine/WebOS.Page.php)
- **Class**: `WebOS_Page`
- **Facade Entry Points**:
  - `WebOS.Page()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Page()` |
### <span id="webos-partition"></span>WebOS.Partition
- **Purpose**: High-performance data partitioning OS layer Multi-App + Multi-Tenant aware Time / Tenant / Policy driven partition resolution Zero-downtime rotation & routing Read/Write partition routing brain for WebOS
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Partition.php](WebOS/PHP.26.00.00/Engine/WebOS.Partition.php)
- **Class**: `WebOS_Partition`
- **Facade Entry Points**:
  - `WebOS.Partition()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PartitionResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.PartitionRouteWrite()` → `public static function RouteWrite(array $Payload, array $Context)`
  - `WebOS.PartitionRouteRead()` → `public static function RouteRead(array $Payload, array $Context)`
  - `WebOS.PartitionEnsure()` → `public static function Ensure(array $Payload, array $Context)`
  - `WebOS.PartitionRotate()` → `public static function Rotate(array $Payload, array $Context)`
  - `WebOS.PartitionStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Partition()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.PartitionResolve()` |
| RouteWrite | `public static function RouteWrite(array $Payload, array $Context)` | `WebOS.PartitionRouteWrite()` |
| RouteRead | `public static function RouteRead(array $Payload, array $Context)` | `WebOS.PartitionRouteRead()` |
| Ensure | `public static function Ensure(array $Payload, array $Context)` | `WebOS.PartitionEnsure()` |
| Rotate | `public static function Rotate(array $Payload, array $Context)` | `WebOS.PartitionRotate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.PartitionStatus()` |
### <span id="webos-patient"></span>WebOS.Patient
- **Purpose**: Enterprise Grade Patient Engine (Multi-App + Multi-Tenant) OS-level entity management: Patient identity, lifecycle, privacy-ready High-performance patterns: strict scoping, safe outputs, audit-ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Patient.php](WebOS/PHP.26.00.00/Engine/WebOS.Patient.php)
- **Class**: `WebOS_Patient`
- **Facade Entry Points**:
  - `WebOS.Patient()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PatientNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.PatientGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.PatientUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.PatientStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.PatientSearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.PatientConsentGrant()` → `public static function ConsentGrant(array $Payload, array $Context)`
  - `WebOS.PatientConsentRevoke()` → `public static function ConsentRevoke(array $Payload, array $Context)`
  - `WebOS.PatientExternalIdSet()` → `public static function ExternalIdSet(array $Payload, array $Context)`
  - `WebOS.PatientExternalIdGet()` → `public static function ExternalIdGet(array $Payload, array $Context)`
  - `WebOS.PatientReindex()` → `public static function Reindex(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Patient()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.PatientNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.PatientGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.PatientUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.PatientStatus()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.PatientSearch()` |
| ConsentGrant | `public static function ConsentGrant(array $Payload, array $Context)` | `WebOS.PatientConsentGrant()` |
| ConsentRevoke | `public static function ConsentRevoke(array $Payload, array $Context)` | `WebOS.PatientConsentRevoke()` |
| ExternalIdSet | `public static function ExternalIdSet(array $Payload, array $Context)` | `WebOS.PatientExternalIdSet()` |
| ExternalIdGet | `public static function ExternalIdGet(array $Payload, array $Context)` | `WebOS.PatientExternalIdGet()` |
| Reindex | `public static function Reindex(array $Payload, array $Context)` | `WebOS.PatientReindex()` |
### <span id="webos-payment"></span>WebOS.Payment
- **Purpose**: Unified payment orchestration engine for WebOS Multi-App, Multi-Tenant, Multi-Provider payment backbone Gateway-agnostic normalized payment lifecycle
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Payment.php](WebOS/PHP.26.00.00/Engine/WebOS.Payment.php)
- **Class**: `WebOS_Payment`
- **Facade Entry Points**:
  - `WebOS.Payment()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.PaymentNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `WebOS.PaymentStatus()` → `public static function Status(array $Payload = [], array $Context = [])`
  - `WebOS.PaymentWebhook()` → `public static function Webhook(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Payment()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `WebOS.PaymentNew()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `WebOS.PaymentStatus()` |
| Webhook | `public static function Webhook(array $Payload = [], array $Context = [])` | `WebOS.PaymentWebhook()` |
### <span id="webos-payout"></span>WebOS.Payout
- **Purpose**: Unified outgoing funds engine (Payout / Withdraw / Settlement) Multi-App, Multi-Tenant, Multi-Gateway Async-first, Idempotent, Audit-proof Highlights     : Loader + EngineRouter v7.0 compatible No direct IO, DB, Gateway calls Uses OS Engines for Security, Wallet, Ledger, Queue Dependencies   : WebOS.Security WebOS.Auth WebOS.Role WebOS.Tenant WebOS.Wallet WebOS.Balance WebOS.Ledger WebOS.Transaction WebOS.Lock WebOS.Queue WebOS.Audit WebOS.Log WebOS.Monitor Usage: WebOS::PayoutRequest([ ... ]) WebOS::PayoutStatus([ 'PayoutId' => '...' ]) ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Payout.php](WebOS/PHP.26.00.00/Engine/WebOS.Payout.php)
- **Class**: `WebOS_Payout`
- **Facade Entry Points**:
  - `WebOS.Payout()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PayoutRequest()` → `public static function Request(array $Payload, array $Context)`
  - `WebOS.PayoutStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.PayoutCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.PayoutRetry()` → `public static function Retry(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Payout()` |
| Request | `public static function Request(array $Payload, array $Context)` | `WebOS.PayoutRequest()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.PayoutStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.PayoutCancel()` |
| Retry | `public static function Retry(array $Payload, array $Context)` | `WebOS.PayoutRetry()` |
### <span id="webos-payroll"></span>WebOS.Payroll
- **Purpose**: Enterprise-grade Payroll Operating Engine Multi-App, Multi-Tenant, Policy-Driven Payroll Deterministic, Auditable, High-Performance
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Payroll.php](WebOS/PHP.26.00.00/Engine/WebOS.Payroll.php)
- **Class**: `WebOS_Payroll`
- **Facade Entry Points**:
  - `WebOS.Payroll()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PayrollStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.PayrollSetup()` → `public static function Setup(array $Payload, array $Context)`
  - `WebOS.PayrollRunNew()` → `public static function RunNew(array $Payload, array $Context)`
  - `WebOS.PayrollRunCompute()` → `public static function RunCompute(array $Payload, array $Context)`
  - `WebOS.PayrollRunLock()` → `public static function RunLock(array $Payload, array $Context)`
  - `WebOS.PayrollPayslipGenerate()` → `public static function PayslipGenerate(array $Payload, array $Context)`
  - `WebOS.PayrollPayoutCreate()` → `public static function PayoutCreate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Payroll()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.PayrollStatus()` |
| Setup | `public static function Setup(array $Payload, array $Context)` | `WebOS.PayrollSetup()` |
| RunNew | `public static function RunNew(array $Payload, array $Context)` | `WebOS.PayrollRunNew()` |
| RunCompute | `public static function RunCompute(array $Payload, array $Context)` | `WebOS.PayrollRunCompute()` |
| RunLock | `public static function RunLock(array $Payload, array $Context)` | `WebOS.PayrollRunLock()` |
| PayslipGenerate | `public static function PayslipGenerate(array $Payload, array $Context)` | `WebOS.PayrollPayslipGenerate()` |
| PayoutCreate | `public static function PayoutCreate(array $Payload, array $Context)` | `WebOS.PayrollPayoutCreate()` |
### <span id="webos-performance"></span>WebOS.Performance
- **Purpose**: WebOS High-Performance Operating System Engine Measure, track, score and optimize runtime performance Request → Unit → Span → Metrics lifecycle
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Performance.php](WebOS/PHP.26.00.00/Engine/WebOS.Performance.php)
- **Class**: `WebOS_Performance`
- **Facade Entry Points**:
  - `WebOS.Performance()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PerformanceStart()` → `public static function Start(array $Payload, array $Context)`
  - `WebOS.PerformanceEnd()` → `public static function End(array $Payload, array $Context)`
  - `WebOS.PerformanceSpanStart()` → `public static function SpanStart(array $Payload, array $Context)`
  - `WebOS.PerformanceSpanEnd()` → `public static function SpanEnd(array $Payload, array $Context)`
  - `WebOS.PerformanceCounterInc()` → `public static function CounterInc(array $Payload, array $Context)`
  - `WebOS.PerformanceGaugeSet()` → `public static function GaugeSet(array $Payload, array $Context)`
  - `WebOS.PerformanceSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`
  - `WebOS.PerformanceScore()` → `public static function Score(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Performance()` |
| Start | `public static function Start(array $Payload, array $Context)` | `WebOS.PerformanceStart()` |
| End | `public static function End(array $Payload, array $Context)` | `WebOS.PerformanceEnd()` |
| SpanStart | `public static function SpanStart(array $Payload, array $Context)` | `WebOS.PerformanceSpanStart()` |
| SpanEnd | `public static function SpanEnd(array $Payload, array $Context)` | `WebOS.PerformanceSpanEnd()` |
| CounterInc | `public static function CounterInc(array $Payload, array $Context)` | `WebOS.PerformanceCounterInc()` |
| GaugeSet | `public static function GaugeSet(array $Payload, array $Context)` | `WebOS.PerformanceGaugeSet()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `WebOS.PerformanceSnapshot()` |
| Score | `public static function Score(array $Payload, array $Context)` | `WebOS.PerformanceScore()` |
### <span id="webos-permission"></span>WebOS.Permission
- **Purpose**: Enterprise Grade Permission Engine for WebOS (Multi-App + Multi-Tenant) Central permission gate for API/Cron/Webhook/Assets and business actions RBAC-first (Role Based) with ABAC/Policy hooks-ready architecture High-performance: cached permission matrix, deterministic decisions
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Permission.php](WebOS/PHP.26.00.00/Engine/WebOS.Permission.php)
- **Class**: `WebOS_Permission`
- **Facade Entry Points**:
  - `WebOS.Permission()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PermissionCheck()` → `public static function Check(array $Payload, array $Context)`
  - `WebOS.PermissionCan()` → `public static function Can(array $Payload, array $Context)`
  - `WebOS.PermissionAssert()` → `public static function Assert(array $Payload, array $Context)`
  - `WebOS.PermissionMatrix()` → `public static function Matrix(array $Payload, array $Context)`
  - `WebOS.PermissionWarmUp()` → `public static function WarmUp(array $Payload, array $Context)`
  - `WebOS.PermissionInvalidate()` → `public static function Invalidate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Permission()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.PermissionCheck()` |
| Can | `public static function Can(array $Payload, array $Context)` | `WebOS.PermissionCan()` |
| Assert | `public static function Assert(array $Payload, array $Context)` | `WebOS.PermissionAssert()` |
| Matrix | `public static function Matrix(array $Payload, array $Context)` | `WebOS.PermissionMatrix()` |
| WarmUp | `public static function WarmUp(array $Payload, array $Context)` | `WebOS.PermissionWarmUp()` |
| Invalidate | `public static function Invalidate(array $Payload, array $Context)` | `WebOS.PermissionInvalidate()` |
### <span id="webos-pipeline"></span>WebOS.Pipeline
- **Purpose**: OS-level Pipeline / Workflow Engine Deterministic, idempotent, resumable execution Multi-Tenant + Multi-App safe Sync + Async (Queue/Worker) execution Enterprise observability (Log, Audit, Trace, Metric)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Pipeline.php](WebOS/PHP.26.00.00/Engine/WebOS.Pipeline.php)
- **Class**: `WebOS_Pipeline`
- **Facade Entry Points**:
  - `WebOS.Pipeline()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PipelineDefine()` → `public static function Define(array $Payload, array $Context)`
  - `WebOS.PipelineRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.PipelineResume()` → `public static function Resume(array $Payload, array $Context)`
  - `WebOS.PipelineStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.PipelineCancel()` → `public static function Cancel(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Pipeline()` |
| Define | `public static function Define(array $Payload, array $Context)` | `WebOS.PipelineDefine()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.PipelineRun()` |
| Resume | `public static function Resume(array $Payload, array $Context)` | `WebOS.PipelineResume()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.PipelineStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.PipelineCancel()` |
### <span id="webos-planner"></span>WebOS.Planner
- **Purpose**: Convert Intent → Deterministic Plan (execution-ready blueprint) Act as OS-level orchestration brain for WebOS Support Multi-App, Multi-Brand, Multi-Tenant planning Enforce Security, Policy, Role, Audit at planning-time
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Planner.php](WebOS/PHP.26.00.00/Engine/WebOS.Planner.php)
- **Class**: `WebOS_Planner`
- **Facade Entry Points**:
  - `WebOS.Planner()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PlannerNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.PlannerBuild()` → `public static function Build(array $Payload, array $Context)`
  - `WebOS.PlannerValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.PlannerExport()` → `public static function Export(array $Payload, array $Context)`
  - `WebOS.PlannerStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Planner()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.PlannerNew()` |
| Build | `public static function Build(array $Payload, array $Context)` | `WebOS.PlannerBuild()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.PlannerValidate()` |
| Export | `public static function Export(array $Payload, array $Context)` | `WebOS.PlannerExport()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.PlannerStatus()` |
### <span id="webos-policy"></span>WebOS.Policy
- **Purpose**: Central Policy Engine for WebOS (System Constitution) Access, Security, Rate, Cache, Feature, Compliance policies Multi-Tenant + Multi-App + Multi-Brand aware Deterministic, Explainable, Auditable decisions
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Policy.php](WebOS/PHP.26.00.00/Engine/WebOS.Policy.php)
- **Class**: `WebOS_Policy`
- **Facade Entry Points**:
  - `WebOS.Policy()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PolicyEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `WebOS.PolicyEnforce()` → `public static function Enforce(array $Payload, array $Context)`
  - `WebOS.PolicyExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Policy()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `WebOS.PolicyEvaluate()` |
| Enforce | `public static function Enforce(array $Payload, array $Context)` | `WebOS.PolicyEnforce()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.PolicyExplain()` |
### <span id="webos-policyai"></span>WebOS.PolicyAI
- **Purpose**: Central Policy + Decision Engine for WebOS Deterministic + AI-assisted policy evaluation Multi-App, Multi-Brand, Multi-Tenant safe decisions
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.PolicyAI.php](WebOS/PHP.26.00.00/Engine/WebOS.PolicyAI.php)
- **Class**: `WebOS_PolicyAI`
- **Facade Entry Points**:
  - `WebOS.PolicyAI()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PolicyAIEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `WebOS.PolicyAICompile()` → `public static function Compile(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.PolicyAI()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `WebOS.PolicyAIEvaluate()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `WebOS.PolicyAICompile()` |
### <span id="webos-post"></span>WebOS.Post
- **Purpose**: Universal Content / Post Engine for WebOS Supports Blog, News, Docs, Articles, CMS, Headless APIs Enterprise Grade + Multi-App + Multi-Tenant safe
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Post.php](WebOS/PHP.26.00.00/Engine/WebOS.Post.php)
- **Class**: `WebOS_Post`
- **Facade Entry Points**:
  - `WebOS.Post()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PostNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.PostGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.PostList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.PostPublish()` → `public static function Publish(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Post()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.PostNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.PostGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.PostList()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.PostPublish()` |
### <span id="webos-predict"></span>WebOS.Predict
- **Purpose**: Enterprise-grade prediction engine for WebOS Works across all Apps, Tenants, Brands Deterministic, auditable, cache-safe predictions Designed for AI / ML / Statistical hybrid models
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Predict.php](WebOS/PHP.26.00.00/Engine/WebOS.Predict.php)
- **Class**: `WebOS_Predict`
- **Facade Entry Points**:
  - `WebOS.Predict()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.PredictRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `WebOS.PredictBatch()` → `public static function Batch(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Predict()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `WebOS.PredictRun()` |
| Batch | `public static function Batch(array $Payload = [], array $Context = [])` | `WebOS.PredictBatch()` |
### <span id="webos-prescription"></span>WebOS.Prescription
- **Purpose**: Enterprise-grade Prescription Management Engine Multi-App, Multi-Tenant, Multi-Role compatible Secure, auditable, high-performance medical workflow core
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Prescription.php](WebOS/PHP.26.00.00/Engine/WebOS.Prescription.php)
- **Class**: `WebOS_Prescription`
- **Facade Entry Points**:
  - `WebOS.Prescription()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PrescriptionNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.PrescriptionUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.PrescriptionGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.PrescriptionList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.PrescriptionIssue()` → `public static function Issue(array $Payload, array $Context)`
  - `WebOS.PrescriptionSign()` → `public static function Sign(array $Payload, array $Context)`
  - `WebOS.PrescriptionCancel()` → `public static function Cancel(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Prescription()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.PrescriptionNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.PrescriptionUpdate()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.PrescriptionGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.PrescriptionList()` |
| Issue | `public static function Issue(array $Payload, array $Context)` | `WebOS.PrescriptionIssue()` |
| Sign | `public static function Sign(array $Payload, array $Context)` | `WebOS.PrescriptionSign()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.PrescriptionCancel()` |
### <span id="webos-product"></span>WebOS.Product
- **Purpose**: Universal Product / Catalog Engine for WebOS Multi-App + Multi-Tenant + Enterprise-Grade Supports Products, Variants, Pricing, Inventory, Media, SEO Backend Operating System level abstraction (not CRUD)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Product.php](WebOS/PHP.26.00.00/Engine/WebOS.Product.php)
- **Class**: `WebOS_Product`
- **Facade Entry Points**:
  - `WebOS.Product()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ProductNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ProductGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ProductUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.ProductStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Product()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ProductNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ProductGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.ProductUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.ProductStatus()` |
### <span id="webos-profile"></span>WebOS.Profile
- **Purpose**: Universal Profile Engine for WebOS Multi-App + Multi-Tenant + Multi-Brand user identity layer Public / Private / Admin profile isolation Schema-driven, cache-safe, audit-ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Profile.php](WebOS/PHP.26.00.00/Engine/WebOS.Profile.php)
- **Class**: `WebOS_Profile`
- **Facade Entry Points**:
  - `WebOS.Profile()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ProfileGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ProfileSet()` → `public static function Set(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Profile()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ProfileGet()` |
| Set | `public static function Set(array $Payload, array $Context)` | `WebOS.ProfileSet()` |
### <span id="webos-profiler"></span>WebOS.Profiler
- **Purpose**: High-performance profiling engine for WebOS Measure execution time, memory, spans, violations Feed data to Monitor, Metric, Trace, Log engines Multi-Tenant + Multi-App safe Dependencies   : WebOS.Loader (static facade) WebOS.EngineRouter v7.0 Engines: Security, Cache, Monitor, Metric, Log, Queue Notes: Safe-mode always ON (never breaks production) Sampling + buffering to reduce overhead ============================================================================
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Profiler.php](WebOS/PHP.26.00.00/Engine/WebOS.Profiler.php)
- **Class**: `WebOS_Profiler`
- **Facade Entry Points**:
  - `WebOS.Profiler()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ProfilerStart()` → `public static function Start(array $Payload, array $Context)`
  - `WebOS.ProfilerStop()` → `public static function Stop(array $Payload, array $Context)`
  - `WebOS.ProfilerSpanStart()` → `public static function SpanStart(array $Payload, array $Context)`
  - `WebOS.ProfilerSpanStop()` → `public static function SpanStop(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Profiler()` |
| Start | `public static function Start(array $Payload, array $Context)` | `WebOS.ProfilerStart()` |
| Stop | `public static function Stop(array $Payload, array $Context)` | `WebOS.ProfilerStop()` |
| SpanStart | `public static function SpanStart(array $Payload, array $Context)` | `WebOS.ProfilerSpanStart()` |
| SpanStop | `public static function SpanStop(array $Payload, array $Context)` | `WebOS.ProfilerSpanStop()` |
### <span id="webos-push"></span>WebOS.Push
- **Purpose**: Unified Push Notification Engine for WebOS Supports Multi-App, Multi-Tenant architecture OS-grade abstraction over push delivery Queue-ready, Provider-agnostic, Secure by design
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Push.php](WebOS/PHP.26.00.00/Engine/WebOS.Push.php)
- **Class**: `WebOS_Push`
- **Facade Entry Points**:
  - `WebOS.Push()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.PushRegisterDevice()` → `public static function RegisterDevice(array $Payload, array $Context)`
  - `WebOS.PushUnregisterDevice()` → `public static function UnregisterDevice(array $Payload, array $Context)`
  - `WebOS.PushSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.PushStats()` → `public static function Stats(array $Payload, array $Context)`
  - `WebOS.PushHealth()` → `public static function Health(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Push()` |
| RegisterDevice | `public static function RegisterDevice(array $Payload, array $Context)` | `WebOS.PushRegisterDevice()` |
| UnregisterDevice | `public static function UnregisterDevice(array $Payload, array $Context)` | `WebOS.PushUnregisterDevice()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.PushSend()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `WebOS.PushStats()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.PushHealth()` |
### <span id="webos-quarantine"></span>WebOS.Quarantine
- **Purpose**: Central isolation engine for risky entities Multi-App + Multi-Tenant safe quarantine system Works as ACTION layer after detection engines
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Quarantine.php](WebOS/PHP.26.00.00/Engine/WebOS.Quarantine.php)
- **Class**: `WebOS_Quarantine`
- **Facade Entry Points**:
  - `WebOS.Quarantine()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.QuarantineCheck()` → `public static function Check(array $Payload, array $Context)`
  - `WebOS.QuarantinePut()` → `public static function Put(array $Payload, array $Context)`
  - `WebOS.QuarantineRelease()` → `public static function Release(array $Payload, array $Context)`
  - `WebOS.QuarantineHit()` → `public static function Hit(array $Entity, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Quarantine()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.QuarantineCheck()` |
| Put | `public static function Put(array $Payload, array $Context)` | `WebOS.QuarantinePut()` |
| Release | `public static function Release(array $Payload, array $Context)` | `WebOS.QuarantineRelease()` |
| Hit | `public static function Hit(array $Entity, array $Context)` | `WebOS.QuarantineHit()` |
### <span id="webos-query"></span>WebOS.Query
- **Purpose**: Secure, Multi-Tenant, Multi-App Query Operating System SQL abstraction with strict safety & performance rules Zero raw SQL by default Prepared statements only Automatic Tenant enforcement Read / Write discipline
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Query.php](WebOS/PHP.26.00.00/Engine/WebOS.Query.php)
- **Class**: `WebOS_Query`
- **Facade Entry Points**:
  - `WebOS.Query()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.QuerySelect()` → `public static function Select(array $Payload, array $Context)`
  - `WebOS.QueryInsert()` → `public static function Insert(array $Payload, array $Context)`
  - `WebOS.QueryUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.QueryDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Query()` |
| Select | `public static function Select(array $Payload, array $Context)` | `WebOS.QuerySelect()` |
| Insert | `public static function Insert(array $Payload, array $Context)` | `WebOS.QueryInsert()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.QueryUpdate()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.QueryDelete()` |
### <span id="webos-queryai"></span>WebOS.QueryAI
- **Purpose**: Intelligent Query Engine for WebOS Natural Language → Secure Query Plan → Retrieval → Answer Multi-App + Multi-Tenant + Permission-Aware by default
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.QueryAI.php](WebOS/PHP.26.00.00/Engine/WebOS.QueryAI.php)
- **Class**: `WebOS_QueryAI`
- **Facade Entry Points**:
  - `WebOS.QueryAI()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.QueryAIPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.QueryAIAsk()` → `public static function Ask(array $Payload, array $Context)`
  - `WebOS.QueryAIExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.QueryAI()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.QueryAIPlan()` |
| Ask | `public static function Ask(array $Payload, array $Context)` | `WebOS.QueryAIAsk()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.QueryAIExplain()` |
### <span id="webos-queue"></span>WebOS.Queue
- **Purpose**: Enterprise-grade Queue Engine for WebOS (Multi-App + Multi-Tenant) High-performance job enqueue, reserve/lease, ack, fail, retry, DLQ OS-level async backbone for APIs, Cron, Webhooks, Pipelines, AI tasks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Queue.php](WebOS/PHP.26.00.00/Engine/WebOS.Queue.php)
- **Class**: `WebOS_Queue`
- **Facade Entry Points**:
  - `WebOS.Queue()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.QueuePush()` → `public static function Push($Payload = [], $Context = [])`
  - `WebOS.QueueLater()` → `public static function Later($Payload = [], $Context = [])`
  - `WebOS.QueueSchedule()` → `public static function Schedule($Payload = [], $Context = [])`
  - `WebOS.QueueBatch()` → `public static function Batch($Payload = [], $Context = [])`
  - `WebOS.QueueReserve()` → `public static function Reserve($Payload = [], $Context = [])`
  - `WebOS.QueueAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `WebOS.QueueFail()` → `public static function Fail($Payload = [], $Context = [])`
  - `WebOS.QueueRetry()` → `public static function Retry($Payload = [], $Context = [])`
  - `WebOS.QueueCancel()` → `public static function Cancel($Payload = [], $Context = [])`
  - `WebOS.QueueStatus()` → `public static function Status($Payload = [], $Context = [])`
  - `WebOS.QueueStats()` → `public static function Stats($Payload = [], $Context = [])`
  - `WebOS.QueueDLQ()` → `public static function DLQ($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Queue()` |
| Push | `public static function Push($Payload = [], $Context = [])` | `WebOS.QueuePush()` |
| Later | `public static function Later($Payload = [], $Context = [])` | `WebOS.QueueLater()` |
| Schedule | `public static function Schedule($Payload = [], $Context = [])` | `WebOS.QueueSchedule()` |
| Batch | `public static function Batch($Payload = [], $Context = [])` | `WebOS.QueueBatch()` |
| Reserve | `public static function Reserve($Payload = [], $Context = [])` | `WebOS.QueueReserve()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `WebOS.QueueAck()` |
| Fail | `public static function Fail($Payload = [], $Context = [])` | `WebOS.QueueFail()` |
| Retry | `public static function Retry($Payload = [], $Context = [])` | `WebOS.QueueRetry()` |
| Cancel | `public static function Cancel($Payload = [], $Context = [])` | `WebOS.QueueCancel()` |
| Status | `public static function Status($Payload = [], $Context = [])` | `WebOS.QueueStatus()` |
| Stats | `public static function Stats($Payload = [], $Context = [])` | `WebOS.QueueStats()` |
| DLQ | `public static function DLQ($Payload = [], $Context = [])` | `WebOS.QueueDLQ()` |
### <span id="webos-ratelimit"></span>WebOS.RateLimit
- **Purpose**: Enterprise-grade Rate Limiting for WebOS Units (API/Webhook/Cron/etc.) Multi-App + Multi-Tenant isolation via Context-aware keys High-performance counters with safe fallbacks Loader-only usage: callable exclusively via WebOS::RateLimit*()
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.RateLimit.php](WebOS/PHP.26.00.00/Engine/WebOS.RateLimit.php)
- **Class**: `WebOS_RateLimit`
- **Facade Entry Points**:
  - `WebOS.RateLimit()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.RateLimitAllow()` → `public static function Allow(array $Payload = [], array $Context = [])`
  - `WebOS.RateLimitHit()` → `public static function Hit(array $Payload = [], array $Context = [])`
  - `WebOS.RateLimitInfo()` → `public static function Info(array $Payload = [], array $Context = [])`
  - `WebOS.RateLimitReset()` → `public static function Reset(array $Payload = [], array $Context = [])`
  - `WebOS.RateLimitPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `WebOS.RateLimitKey()` → `public static function Key(array $Payload = [], array $Context = [], array $Policy = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.RateLimit()` |
| Allow | `public static function Allow(array $Payload = [], array $Context = [])` | `WebOS.RateLimitAllow()` |
| Hit | `public static function Hit(array $Payload = [], array $Context = [])` | `WebOS.RateLimitHit()` |
| Info | `public static function Info(array $Payload = [], array $Context = [])` | `WebOS.RateLimitInfo()` |
| Reset | `public static function Reset(array $Payload = [], array $Context = [])` | `WebOS.RateLimitReset()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `WebOS.RateLimitPolicy()` |
| Key | `public static function Key(array $Payload = [], array $Context = [], array $Policy = [])` | `WebOS.RateLimitKey()` |
### <span id="webos-recommend"></span>WebOS.Recommend
- **Purpose**: Universal Recommendation Engine for WebOS (Multi-App + Multi-Tenant) Provide "Suggest / Track / Profile / Explain" as a backend OS capability Safe, deterministic, high-performance defaults (cache-first, DB-optional)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Recommend.php](WebOS/PHP.26.00.00/Engine/WebOS.Recommend.php)
- **Class**: `WebOS_Recommend`
- **Facade Entry Points**:
  - `WebOS.Recommend()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.RecommendSuggest()` → `public static function Suggest($Payload = [], $Context = [])`
  - `WebOS.RecommendTrack()` → `public static function Track($Payload = [], $Context = [])`
  - `WebOS.RecommendProfile()` → `public static function Profile($Payload = [], $Context = [])`
  - `WebOS.RecommendExplain()` → `public static function Explain($Payload = [], $Context = [])`
  - `WebOS.RecommendWarm()` → `public static function Warm($Payload = [], $Context = [])`
  - `WebOS.RecommendInvalidate()` → `public static function Invalidate($Payload = [], $Context = [])`
  - `WebOS.RecommendModel()` → `public static function Model($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Recommend()` |
| Suggest | `public static function Suggest($Payload = [], $Context = [])` | `WebOS.RecommendSuggest()` |
| Track | `public static function Track($Payload = [], $Context = [])` | `WebOS.RecommendTrack()` |
| Profile | `public static function Profile($Payload = [], $Context = [])` | `WebOS.RecommendProfile()` |
| Explain | `public static function Explain($Payload = [], $Context = [])` | `WebOS.RecommendExplain()` |
| Warm | `public static function Warm($Payload = [], $Context = [])` | `WebOS.RecommendWarm()` |
| Invalidate | `public static function Invalidate($Payload = [], $Context = [])` | `WebOS.RecommendInvalidate()` |
| Model | `public static function Model($Payload = [], $Context = [])` | `WebOS.RecommendModel()` |
### <span id="webos-recovery"></span>WebOS.Recovery
- **Purpose**: Enterprise-grade Account Recovery Engine Password reset, access regain, emergency lockdown Multi-App + Multi-Tenant + High-Security recovery workflows
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Recovery.php](WebOS/PHP.26.00.00/Engine/WebOS.Recovery.php)
- **Class**: `WebOS_Recovery`
- **Facade Entry Points**:
  - `WebOS.Recovery()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RecoveryRequest()` → `public static function Request(array $Payload, array $Context)`
  - `WebOS.RecoveryVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.RecoveryConsume()` → `public static function Consume(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Recovery()` |
| Request | `public static function Request(array $Payload, array $Context)` | `WebOS.RecoveryRequest()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.RecoveryVerify()` |
| Consume | `public static function Consume(array $Payload, array $Context)` | `WebOS.RecoveryConsume()` |
### <span id="webos-recruitment"></span>WebOS.Recruitment
- **Purpose**: Enterprise-grade Recruitment / ATS backend engine Multi-Tenant + Multi-App + Multi-Org safe Works as a core OS-level backend service Fully compatible with WebOS.Loader + EngineRouter v7.0
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Recruitment.php](WebOS/PHP.26.00.00/Engine/WebOS.Recruitment.php)
- **Class**: `WebOS_Recruitment`
- **Facade Entry Points**:
  - `WebOS.Recruitment()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RecruitmentRequisitionNew()` → `public static function RequisitionNew(array $Payload, array $Context)`
  - `WebOS.RecruitmentJobPublish()` → `public static function JobPublish(array $Payload, array $Context)`
  - `WebOS.RecruitmentCandidateNew()` → `public static function CandidateNew(array $Payload, array $Context)`
  - `WebOS.RecruitmentApplicationStageSet()` → `public static function ApplicationStageSet(array $Payload, array $Context)`
  - `WebOS.RecruitmentDashboardGet()` → `public static function DashboardGet(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Recruitment()` |
| RequisitionNew | `public static function RequisitionNew(array $Payload, array $Context)` | `WebOS.RecruitmentRequisitionNew()` |
| JobPublish | `public static function JobPublish(array $Payload, array $Context)` | `WebOS.RecruitmentJobPublish()` |
| CandidateNew | `public static function CandidateNew(array $Payload, array $Context)` | `WebOS.RecruitmentCandidateNew()` |
| ApplicationStageSet | `public static function ApplicationStageSet(array $Payload, array $Context)` | `WebOS.RecruitmentApplicationStageSet()` |
| DashboardGet | `public static function DashboardGet(array $Payload, array $Context)` | `WebOS.RecruitmentDashboardGet()` |
### <span id="webos-refund"></span>WebOS.Refund
- **Purpose**: Enterprise Grade Refund Management Engine Multi-App + Multi-Tenant safe by Context High-performance state machine + audit timeline Idempotent request support (RefundLock) Provider-ready (Gateway/Wallet/StoreCredit routing)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Refund.php](WebOS/PHP.26.00.00/Engine/WebOS.Refund.php)
- **Class**: `WebOS_Refund`
- **Facade Entry Points**:
  - `WebOS.Refund()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RefundHelp()` → `public static function Help(array $Payload, array $Context)`
  - `WebOS.RefundNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.RefundGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.RefundList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.RefundUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.RefundApprove()` → `public static function Approve(array $Payload, array $Context)`
  - `WebOS.RefundReject()` → `public static function Reject(array $Payload, array $Context)`
  - `WebOS.RefundCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.RefundExecute()` → `public static function Execute(array $Payload, array $Context)`
  - `WebOS.RefundValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.RefundCalculate()` → `public static function Calculate(array $Payload, array $Context)`
  - `WebOS.RefundTimeline()` → `public static function Timeline(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Refund()` |
| Help | `public static function Help(array $Payload, array $Context)` | `WebOS.RefundHelp()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.RefundNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.RefundGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.RefundList()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.RefundUpdate()` |
| Approve | `public static function Approve(array $Payload, array $Context)` | `WebOS.RefundApprove()` |
| Reject | `public static function Reject(array $Payload, array $Context)` | `WebOS.RefundReject()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.RefundCancel()` |
| Execute | `public static function Execute(array $Payload, array $Context)` | `WebOS.RefundExecute()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.RefundValidate()` |
| Calculate | `public static function Calculate(array $Payload, array $Context)` | `WebOS.RefundCalculate()` |
| Timeline | `public static function Timeline(array $Payload, array $Context)` | `WebOS.RefundTimeline()` |
### <span id="webos-region"></span>WebOS.Region
- **Purpose**: Region / Geo Intelligence as OS-level capability (Multi-App + Multi-Tenant) Canonical region resolution (Country / Subdivision / City / ZIP) Policy overlay merge (App → Brand → Tenant → SDK → Core) Safe caching + compiled indexes for ultra performance
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Region.php](WebOS/PHP.26.00.00/Engine/WebOS.Region.php)
- **Class**: `WebOS_Region`
- **Facade Entry Points**:
  - `WebOS.Region()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.RegionGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.RegionResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.RegionDetect()` → `public static function Detect(array $Payload = [], array $Context = [])`
  - `WebOS.RegionPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `WebOS.RegionIsAllowed()` → `public static function IsAllowed(array $Payload = [], array $Context = [])`
  - `WebOS.RegionIndexBuild()` → `public static function IndexBuild(array $Payload = [], array $Context = [])`
  - `WebOS.RegionIndexClear()` → `public static function IndexClear(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Region()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.RegionGet()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.RegionResolve()` |
| Detect | `public static function Detect(array $Payload = [], array $Context = [])` | `WebOS.RegionDetect()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `WebOS.RegionPolicy()` |
| IsAllowed | `public static function IsAllowed(array $Payload = [], array $Context = [])` | `WebOS.RegionIsAllowed()` |
| IndexBuild | `public static function IndexBuild(array $Payload = [], array $Context = [])` | `WebOS.RegionIndexBuild()` |
| IndexClear | `public static function IndexClear(array $Payload = [], array $Context = [])` | `WebOS.RegionIndexClear()` |
### <span id="webos-release"></span>WebOS.Release
- **Purpose**: Enterprise-grade Release Lifecycle Engine for WebOS Multi-App + Multi-Tenant + Multi-Strategy Releases Deterministic, Auditable, Rollbackable OS-level Releases
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Release.php](WebOS/PHP.26.00.00/Engine/WebOS.Release.php)
- **Class**: `WebOS_Release`
- **Facade Entry Points**:
  - `WebOS.Release()` → `public static function Main($Payload, $Context)`
  - `WebOS.ReleaseNew()` → `public static function New($Payload, $Context)`
  - `WebOS.ReleasePlan()` → `public static function Plan($Payload, $Context)`
  - `WebOS.ReleaseApprove()` → `public static function Approve($Payload, $Context)`
  - `WebOS.ReleaseDeploy()` → `public static function Deploy($Payload, $Context)`
  - `WebOS.ReleaseVerify()` → `public static function Verify($Payload, $Context)`
  - `WebOS.ReleaseRollback()` → `public static function Rollback($Payload, $Context)`
  - `WebOS.ReleaseStatus()` → `public static function Status($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.Release()` |
| New | `public static function New($Payload, $Context)` | `WebOS.ReleaseNew()` |
| Plan | `public static function Plan($Payload, $Context)` | `WebOS.ReleasePlan()` |
| Approve | `public static function Approve($Payload, $Context)` | `WebOS.ReleaseApprove()` |
| Deploy | `public static function Deploy($Payload, $Context)` | `WebOS.ReleaseDeploy()` |
| Verify | `public static function Verify($Payload, $Context)` | `WebOS.ReleaseVerify()` |
| Rollback | `public static function Rollback($Payload, $Context)` | `WebOS.ReleaseRollback()` |
| Status | `public static function Status($Payload, $Context)` | `WebOS.ReleaseStatus()` |
### <span id="webos-relevance"></span>WebOS.Relevance
- **Purpose**: Enterprise-grade relevance scoring engine Deterministic + explainable relevance for any entity Multi-App, Multi-Tenant, Multi-Mode (search/feed/recommend) Works as core intelligence layer for Search, Discovery, Ranking
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Relevance.php](WebOS/PHP.26.00.00/Engine/WebOS.Relevance.php)
- **Class**: `WebOS_Relevance`
- **Facade Entry Points**:
  - `WebOS.Relevance()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.RelevanceSignals()` → `public static function Signals(array $Input)`
  - `WebOS.RelevanceWeights()` → `public static function Weights(array $Input)`
  - `WebOS.RelevanceScore()` → `public static function Score(array $Signals, array $Weights, array $Input)`
  - `WebOS.RelevanceCacheKey()` → `public static function CacheKey(array $Input)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Relevance()` |
| Signals | `public static function Signals(array $Input)` | `WebOS.RelevanceSignals()` |
| Weights | `public static function Weights(array $Input)` | `WebOS.RelevanceWeights()` |
| Score | `public static function Score(array $Signals, array $Weights, array $Input)` | `WebOS.RelevanceScore()` |
| CacheKey | `public static function CacheKey(array $Input)` | `WebOS.RelevanceCacheKey()` |
### <span id="webos-replica"></span>WebOS.Replica
- **Purpose**: Read / Write DB replica routing for WebOS Multi-App + Multi-Tenant aware Lag-aware, policy-driven replica selection Fail-safe primary fallback
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Replica.php](WebOS/PHP.26.00.00/Engine/WebOS.Replica.php)
- **Class**: `WebOS_Replica`
- **Facade Entry Points**:
  - `WebOS.Replica()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ReplicaSelect()` → `public static function Select(array $Payload, array $Context)`
  - `WebOS.ReplicaPrimary()` → `public static function Primary(array $Payload, array $Context)`
  - `WebOS.ReplicaReplica()` → `public static function Replica(array $Payload, array $Context)`
  - `WebOS.ReplicaAfterWrite()` → `public static function AfterWrite(array $Payload, array $Context)`
  - `WebOS.ReplicaHealthCheck()` → `public static function HealthCheck(array $Payload, array $Context)`
  - `WebOS.ReplicaStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Replica()` |
| Select | `public static function Select(array $Payload, array $Context)` | `WebOS.ReplicaSelect()` |
| Primary | `public static function Primary(array $Payload, array $Context)` | `WebOS.ReplicaPrimary()` |
| Replica | `public static function Replica(array $Payload, array $Context)` | `WebOS.ReplicaReplica()` |
| AfterWrite | `public static function AfterWrite(array $Payload, array $Context)` | `WebOS.ReplicaAfterWrite()` |
| HealthCheck | `public static function HealthCheck(array $Payload, array $Context)` | `WebOS.ReplicaHealthCheck()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `WebOS.ReplicaStats()` |
### <span id="webos-report"></span>WebOS.Report
- **Purpose**: Universal Reporting Engine for WebOS Multi-App, Multi-Tenant, Enterprise-Grade Reporting OS Layer Live + Async + Materialized report execution orchestration
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Report.php](WebOS/PHP.26.00.00/Engine/WebOS.Report.php)
- **Class**: `WebOS_Report`
- **Facade Entry Points**:
  - `WebOS.Report()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ReportReportSpecNew()` → `public static function ReportSpecNew(array $Payload, array $Context)`
  - `WebOS.ReportReportSpecGet()` → `public static function ReportSpecGet(array $Payload, array $Context)`
  - `WebOS.ReportReportRun()` → `public static function ReportRun(array $Payload, array $Context)`
  - `WebOS.ReportReportExport()` → `public static function ReportExport(array $Payload, array $Context)`
  - `WebOS.ReportReportScheduleNew()` → `public static function ReportScheduleNew(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Report()` |
| ReportSpecNew | `public static function ReportSpecNew(array $Payload, array $Context)` | `WebOS.ReportReportSpecNew()` |
| ReportSpecGet | `public static function ReportSpecGet(array $Payload, array $Context)` | `WebOS.ReportReportSpecGet()` |
| ReportRun | `public static function ReportRun(array $Payload, array $Context)` | `WebOS.ReportReportRun()` |
| ReportExport | `public static function ReportExport(array $Payload, array $Context)` | `WebOS.ReportReportExport()` |
| ReportScheduleNew | `public static function ReportScheduleNew(array $Payload, array $Context)` | `WebOS.ReportReportScheduleNew()` |
### <span id="webos-restore"></span>WebOS.Restore
- **Purpose**: Enterprise-grade Restore Orchestration Engine Multi-App, Multi-Tenant, Safe & Auditable Restore Snapshot / PITR / Partial Restore Support Zero-downtime capable via Quarantine + Cutover
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Restore.php](WebOS/PHP.26.00.00/Engine/WebOS.Restore.php)
- **Class**: `WebOS_Restore`
- **Facade Entry Points**:
  - `WebOS.Restore()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RestorePlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.RestoreRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.RestoreDryRun()` → `public static function DryRun(array $Payload, array $Context)`
  - `WebOS.RestoreVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.RestoreCutover()` → `public static function Cutover(array $Payload, array $Context)`
  - `WebOS.RestoreRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `WebOS.RestoreStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.RestoreCancel()` → `public static function Cancel(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Restore()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.RestorePlan()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.RestoreRun()` |
| DryRun | `public static function DryRun(array $Payload, array $Context)` | `WebOS.RestoreDryRun()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.RestoreVerify()` |
| Cutover | `public static function Cutover(array $Payload, array $Context)` | `WebOS.RestoreCutover()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.RestoreRollback()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.RestoreStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.RestoreCancel()` |
### <span id="webos-result"></span>WebOS.Result
- **Purpose**: Enterprise-grade Result Management Engine Multi-App + Multi-Tenant safe High-performance, audit-ready, reproducible result system
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Result.php](WebOS/PHP.26.00.00/Engine/WebOS.Result.php)
- **Class**: `WebOS_Result`
- **Facade Entry Points**:
  - `WebOS.Result()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ResultGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ResultNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ResultCalculate()` → `public static function Calculate(array $Payload, array $Context)`
  - `WebOS.ResultPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `WebOS.ResultLock()` → `public static function Lock(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Result()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ResultGet()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ResultNew()` |
| Calculate | `public static function Calculate(array $Payload, array $Context)` | `WebOS.ResultCalculate()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.ResultPublish()` |
| Lock | `public static function Lock(array $Payload, array $Context)` | `WebOS.ResultLock()` |
### <span id="webos-retry"></span>WebOS.Retry
- **Purpose**: Deterministic, policy-driven retry system Supports Inline, Deferred, Scheduled retry modes Enforces retry budget, idempotency, and safety Multi-App + Multi-Tenant safe
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Retry.php](WebOS/PHP.26.00.00/Engine/WebOS.Retry.php)
- **Class**: `WebOS_Retry`
- **Facade Entry Points**:
  - `WebOS.Retry()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.RetryDo()` → `public static function Do(array $Payload = [], array $Context = [])`
  - `WebOS.RetryLater()` → `public static function Later(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Retry()` |
| Do | `public static function Do(array $Payload = [], array $Context = [])` | `WebOS.RetryDo()` |
| Later | `public static function Later(array $Payload = [], array $Context = [])` | `WebOS.RetryLater()` |
### <span id="webos-role"></span>WebOS.Role
- **Purpose**: Enterprise-grade Authorization Engine (Role + Optional Permission Gate) Multi-App + Multi-Tenant safe role resolution and checks Strict, deterministic, auditable decisions (Deny-by-default) Backward compatible with CSV roles: "Admin,Manager,Accountant"
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Role.php](WebOS/PHP.26.00.00/Engine/WebOS.Role.php)
- **Class**: `WebOS_Role`
- **Facade Entry Points**:
  - `WebOS.Role()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.Role()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.RoleAllow()` → `public static function Allow(array $Payload = [], array $Context = [])`
  - `WebOS.RoleHas()` → `public static function Has(array $Payload = [], array $Context = [])`
  - `WebOS.RoleNormalize()` → `public static function Normalize(array $Payload = [], array $Context = [])`
  - `WebOS.RoleResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.RolePermission()` → `public static function Permission(array $Payload = [], array $Context = [])`
  - `WebOS.RoleExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`
  - `WebOS.RoleScope()` → `public static function Scope(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Role()` |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Role()` |
| Allow | `public static function Allow(array $Payload = [], array $Context = [])` | `WebOS.RoleAllow()` |
| Has | `public static function Has(array $Payload = [], array $Context = [])` | `WebOS.RoleHas()` |
| Normalize | `public static function Normalize(array $Payload = [], array $Context = [])` | `WebOS.RoleNormalize()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.RoleResolve()` |
| Permission | `public static function Permission(array $Payload = [], array $Context = [])` | `WebOS.RolePermission()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `WebOS.RoleExplain()` |
| Scope | `public static function Scope(array $Payload = [], array $Context = [])` | `WebOS.RoleScope()` |
### <span id="webos-rolehr"></span>WebOS.RoleHR
- **Purpose**: Enterprise-grade HR Role Management Engine Multi-Tenant + Multi-App + Multi-Scope role orchestration Authoritative HR role source for Permission & Auth engines
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.RoleHR.php](WebOS/PHP.26.00.00/Engine/WebOS.RoleHR.php)
- **Class**: `WebOS_RoleHR`
- **Facade Entry Points**:
  - `WebOS.RoleHR()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RoleHRAssign()` → `public static function Assign(array $Payload, array $Context)`
  - `WebOS.RoleHRRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `WebOS.RoleHRCheck()` → `public static function Check(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.RoleHR()` |
| Assign | `public static function Assign(array $Payload, array $Context)` | `WebOS.RoleHRAssign()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `WebOS.RoleHRRevoke()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.RoleHRCheck()` |
### <span id="webos-root"></span>WebOS.Root
- **Purpose**: Global Root Orchestrator for WebOS Single entry governance layer for all Units (API, Cron, Webhook, etc.) Builds RootContext (Tenant, App, User, Policy, Unit) Applies security, policy, limits, cache rules Delegates execution to Executor Engine
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Root.php](WebOS/PHP.26.00.00/Engine/WebOS.Root.php)
- **Class**: `WebOS_Root`
- **Facade Entry Points**:
  - `WebOS.Root()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RootDispatch()` → `public static function Dispatch(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Root()` |
| Dispatch | `public static function Dispatch(array $Payload, array $Context)` | `WebOS.RootDispatch()` |
### <span id="webos-row"></span>WebOS.Row
- **Purpose**: [] WebOS ka core CRUD engine. Enterprise-grade, multi-app, multi-tenant projects ke liye high-performance Row / Rows / Insert / Update / Status / Delete / Exists / Count / Sum / Transaction operations.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Row.php](WebOS/PHP.26.00.00/Engine/WebOS.Row.php)
- **Class**: `WebOS_Row`
- **Facade Entry Points**: None documented
### <span id="webos-ruleengine"></span>WebOS.RuleEngine
- **Purpose**: Central Decision & Rule Evaluation Engine for WebOS Guard, Compute, Route, Policy & Workflow Rules Multi-App, Multi-Tenant, Multi-Brand aware High-performance, cache-first execution
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.RuleEngine.php](WebOS/PHP.26.00.00/Engine/WebOS.RuleEngine.php)
- **Class**: `WebOS_RuleEngine`
- **Facade Entry Points**:
  - `WebOS.RuleEngine()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.RuleEngineEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `WebOS.RuleEngineGuard()` → `public static function Guard(array $Payload, array $Context)`
  - `WebOS.RuleEngineCompute()` → `public static function Compute(array $Payload, array $Context)`
  - `WebOS.RuleEngineExplain()` → `public static function Explain(array $Payload, array $Context)`
  - `WebOS.RuleEngineCompile()` → `public static function Compile(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.RuleEngine()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `WebOS.RuleEngineEvaluate()` |
| Guard | `public static function Guard(array $Payload, array $Context)` | `WebOS.RuleEngineGuard()` |
| Compute | `public static function Compute(array $Payload, array $Context)` | `WebOS.RuleEngineCompute()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.RuleEngineExplain()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `WebOS.RuleEngineCompile()` |
### <span id="webos-sandbox"></span>WebOS.Sandbox
- **Purpose**: Secure execution envelope for Units / APIs / Hooks / Plugins Enforce policy-based isolation (Multi-App + Multi-Tenant) Runtime guards: time, memory, output, scope Central sandbox for enterprise-grade backend OS
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Sandbox.php](WebOS/PHP.26.00.00/Engine/WebOS.Sandbox.php)
- **Class**: `WebOS_Sandbox`
- **Facade Entry Points**:
  - `WebOS.Sandbox()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.SandboxRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `WebOS.SandboxPolicy()` → `public static function Policy(array $Payload, array $Context, string $Scope)`
  - `WebOS.SandboxAllow()` → `public static function Allow(array $Payload, array $Context, array $Policy)`
  - `WebOS.SandboxGuard()` → `public static function Guard(array $Policy)`
  - `WebOS.SandboxScope()` → `public static function Scope(array $Payload, array $Context)`
  - `WebOS.SandboxReport()` → `public static function Report(array $Payload, array $Context, array $Policy, $Result, string $Scope)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Sandbox()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `WebOS.SandboxRun()` |
| Policy | `public static function Policy(array $Payload, array $Context, string $Scope)` | `WebOS.SandboxPolicy()` |
| Allow | `public static function Allow(array $Payload, array $Context, array $Policy)` | `WebOS.SandboxAllow()` |
| Guard | `public static function Guard(array $Policy)` | `WebOS.SandboxGuard()` |
| Scope | `public static function Scope(array $Payload, array $Context)` | `WebOS.SandboxScope()` |
| Report | `public static function Report(array $Payload, array $Context, array $Policy, $Result, string $Scope)` | `WebOS.SandboxReport()` |
### <span id="webos-scaling"></span>WebOS.Scaling
- **Purpose**: OS-level adaptive scaling brain for WebOS Multi-App, Multi-Tenant, Enterprise-grade performance control Policy-driven, metric-aware, safe auto-scaling
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Scaling.php](WebOS/PHP.26.00.00/Engine/WebOS.Scaling.php)
- **Class**: `WebOS_Scaling`
- **Facade Entry Points**:
  - `WebOS.Scaling()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ScalingObserve()` → `public static function Observe(array $Payload, array $Context)`
  - `WebOS.ScalingDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `WebOS.ScalingApply()` → `public static function Apply(array $Payload, array $Context)`
  - `WebOS.ScalingStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.ScalingSimulate()` → `public static function Simulate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Scaling()` |
| Observe | `public static function Observe(array $Payload, array $Context)` | `WebOS.ScalingObserve()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `WebOS.ScalingDecide()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `WebOS.ScalingApply()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.ScalingStatus()` |
| Simulate | `public static function Simulate(array $Payload, array $Context)` | `WebOS.ScalingSimulate()` |
### <span id="webos-schema"></span>WebOS.Schema
- **Purpose**: Treat Database Schema as OS-Level Asset Schema Registry (Source of Truth) Live DB Introspection Drift Detection (Registry vs Reality) Safe Migration Planning (Zero-Downtime Ready) Multi-App + Multi-Tenant Aware
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Schema.php](WebOS/PHP.26.00.00/Engine/WebOS.Schema.php)
- **Class**: `WebOS_Schema`
- **Facade Entry Points**:
  - `WebOS.Schema()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SchemaGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SchemaIntrospect()` → `public static function Introspect(array $Payload, array $Context)`
  - `WebOS.SchemaDiff()` → `public static function Diff(array $Payload, array $Context)`
  - `WebOS.SchemaValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.SchemaMigratePlan()` → `public static function MigratePlan(array $Payload, array $Context)`
  - `WebOS.SchemaMigrateRun()` → `public static function MigrateRun(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Schema()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SchemaGet()` |
| Introspect | `public static function Introspect(array $Payload, array $Context)` | `WebOS.SchemaIntrospect()` |
| Diff | `public static function Diff(array $Payload, array $Context)` | `WebOS.SchemaDiff()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.SchemaValidate()` |
| MigratePlan | `public static function MigratePlan(array $Payload, array $Context)` | `WebOS.SchemaMigratePlan()` |
| MigrateRun | `public static function MigrateRun(array $Payload, array $Context)` | `WebOS.SchemaMigrateRun()` |
### <span id="webos-sdk"></span>WebOS.SDK
- **Purpose**: Enterprise SDK Orchestrator for WebOS (Multi-App + Multi-Tenant) Deterministic SDK Module/Provider resolution with layered overrides Registry-driven SDK discovery (no runtime scanning in hot paths) Config layering: Tenant > App > Brand > Default Provider replaceability without code changes (future-proof contracts) ----------------------------------------------------------------------------
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SDK.php](WebOS/PHP.26.00.00/Engine/WebOS.SDK.php)
- **Class**: `WebOS_SDK`
- **Facade Entry Points**:
  - `WebOS.SDK()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SDKResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.SDKUse()` → `public static function Use(array $Payload, array $Context)`
  - `WebOS.SDKList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.SDKVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.SDKHealth()` → `public static function Health(array $Payload, array $Context)`
  - `WebOS.SDKRegister()` → `public static function Register(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SDK()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.SDKResolve()` |
| Use | `public static function Use(array $Payload, array $Context)` | `WebOS.SDKUse()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.SDKList()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.SDKVerify()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.SDKHealth()` |
| Register | `public static function Register(array $Payload, array $Context)` | `WebOS.SDKRegister()` |
### <span id="webos-search"></span>WebOS.Search
- **Purpose**: Enterprise-grade Search Engine for Multi-App + Multi-Tenant WebOS Query, Suggest, Index (hooks-ready), Health, Explain Permission-aware + Cache-safe + High-performance patterns
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Search.php](WebOS/PHP.26.00.00/Engine/WebOS.Search.php)
- **Class**: `WebOS_Search`
- **Facade Entry Points**:
  - `WebOS.Search()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.SearchQuery()` → `public static function Query($Payload = [], $Context = [])`
  - `WebOS.SearchSuggest()` → `public static function Suggest($Payload = [], $Context = [])`
  - `WebOS.SearchIndex()` → `public static function Index($Payload = [], $Context = [])`
  - `WebOS.SearchHealth()` → `public static function Health($Payload = [], $Context = [])`
  - `WebOS.SearchExplain()` → `public static function Explain($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Search()` |
| Query | `public static function Query($Payload = [], $Context = [])` | `WebOS.SearchQuery()` |
| Suggest | `public static function Suggest($Payload = [], $Context = [])` | `WebOS.SearchSuggest()` |
| Index | `public static function Index($Payload = [], $Context = [])` | `WebOS.SearchIndex()` |
| Health | `public static function Health($Payload = [], $Context = [])` | `WebOS.SearchHealth()` |
| Explain | `public static function Explain($Payload = [], $Context = [])` | `WebOS.SearchExplain()` |
### <span id="webos-secrets"></span>WebOS.Secrets
- **Purpose**: Centralized, secure, enterprise-grade Secrets Management Engine Multi-Tenant + Multi-App + Multi-Brand aware Supports versioning, rotation, policies, audit Zero secret leakage by design
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Secrets.php](WebOS/PHP.26.00.00/Engine/WebOS.Secrets.php)
- **Class**: `WebOS_Secrets`
- **Facade Entry Points**:
  - `WebOS.Secrets()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SecretsGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SecretsSet()` → `public static function Set(array $Payload, array $Context)`
  - `WebOS.SecretsRotate()` → `public static function Rotate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Secrets()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SecretsGet()` |
| Set | `public static function Set(array $Payload, array $Context)` | `WebOS.SecretsSet()` |
| Rotate | `public static function Rotate(array $Payload, array $Context)` | `WebOS.SecretsRotate()` |
### <span id="webos-secruleurity"></span>WebOS.SecRuleurity
- **Purpose**: WebOS OS-Level Rule Engine (Enterprise Grade) Multi-App + Multi-Tenant Policy / Decision Layer Data-driven rules (no hard-coded if-else across apps) Fast evaluation with compile + cache Explainability (audit-grade traces) ---------------------------------------------------------------------------- Core Guarantees (AliensGrade): Loader + EngineRouter v7.0 compatible (Main() mandatory) No functions/
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SecRuleurity.php](WebOS/PHP.26.00.00/Engine/WebOS.SecRuleurity.php)
- **Class**: `WebOS_Rule`
- **Facade Entry Points**:
  - `WebOS.SecRuleurity()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SecRuleurityEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `WebOS.SecRuleurityMatch()` → `public static function Match(array $Payload, array $Context)`
  - `WebOS.SecRuleurityExecute()` → `public static function Execute(array $Payload, array $Context)`
  - `WebOS.SecRuleurityExplain()` → `public static function Explain(array $Payload, array $Context)`
  - `WebOS.SecRuleurityValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.SecRuleurityCompile()` → `public static function Compile(array $Payload, array $Context)`
  - `WebOS.SecRuleurityCacheWarm()` → `public static function CacheWarm(array $Payload, array $Context)`
  - `WebOS.SecRuleurityGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SecRuleurityList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.SecRuleurityNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.SecRuleurityUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.SecRuleurityStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.SecRuleurityVersion()` → `public static function Version(array $Payload, array $Context)`
  - `WebOS.SecRuleurityRollback()` → `public static function Rollback(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SecRuleurity()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `WebOS.SecRuleurityEvaluate()` |
| Match | `public static function Match(array $Payload, array $Context)` | `WebOS.SecRuleurityMatch()` |
| Execute | `public static function Execute(array $Payload, array $Context)` | `WebOS.SecRuleurityExecute()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.SecRuleurityExplain()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.SecRuleurityValidate()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `WebOS.SecRuleurityCompile()` |
| CacheWarm | `public static function CacheWarm(array $Payload, array $Context)` | `WebOS.SecRuleurityCacheWarm()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SecRuleurityGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.SecRuleurityList()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.SecRuleurityNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.SecRuleurityUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.SecRuleurityStatus()` |
| Version | `public static function Version(array $Payload, array $Context)` | `WebOS.SecRuleurityVersion()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.SecRuleurityRollback()` |
### <span id="webos-security"></span>WebOS.Security
- **Purpose**: Universal Category / Taxonomy Engine for WebOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph extensions High-performance read patterns with cache + selective invalidation Enterprise governance: validation, policy checks, audit hooks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Security.php](WebOS/PHP.26.00.00/Engine/WebOS.Security.php)
- **Class**: `WebOS_Category`
- **Facade Entry Points**:
  - `WebOS.Security()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SecurityGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SecurityList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.SecuritySearch()` → `public static function Search(array $Payload, array $Context)`
  - `WebOS.SecurityNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.SecurityUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.SecurityStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.SecurityDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `WebOS.SecurityTree()` → `public static function Tree(array $Payload, array $Context)`
  - `WebOS.SecurityChildren()` → `public static function Children(array $Payload, array $Context)`
  - `WebOS.SecurityBreadcrumb()` → `public static function Breadcrumb(array $Payload, array $Context)`
  - `WebOS.SecurityMove()` → `public static function Move(array $Payload, array $Context)`
  - `WebOS.SecurityRebuild()` → `public static function Rebuild(array $Payload, array $Context)`
  - `WebOS.SecurityMapAdd()` → `public static function MapAdd(array $Payload, array $Context)`
  - `WebOS.SecurityMapRemove()` → `public static function MapRemove(array $Payload, array $Context)`
  - `WebOS.SecurityMapList()` → `public static function MapList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Security()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SecurityGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.SecurityList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `WebOS.SecuritySearch()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.SecurityNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.SecurityUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.SecurityStatus()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `WebOS.SecurityDelete()` |
| Tree | `public static function Tree(array $Payload, array $Context)` | `WebOS.SecurityTree()` |
| Children | `public static function Children(array $Payload, array $Context)` | `WebOS.SecurityChildren()` |
| Breadcrumb | `public static function Breadcrumb(array $Payload, array $Context)` | `WebOS.SecurityBreadcrumb()` |
| Move | `public static function Move(array $Payload, array $Context)` | `WebOS.SecurityMove()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `WebOS.SecurityRebuild()` |
| MapAdd | `public static function MapAdd(array $Payload, array $Context)` | `WebOS.SecurityMapAdd()` |
| MapRemove | `public static function MapRemove(array $Payload, array $Context)` | `WebOS.SecurityMapRemove()` |
| MapList | `public static function MapList(array $Payload, array $Context)` | `WebOS.SecurityMapList()` |
### <span id="webos-securityscan"></span>WebOS.SecurityScan
- **Purpose**: Enterprise-grade static & policy security scanner for WebOS Multi-App + Multi-Tenant aware Release gate + audit-ready security reports WebOS-specific rule enforcement (Boot, Engine, Unit, Cache safety) ----------------------------------------------------------------------------
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SecurityScan.php](WebOS/PHP.26.00.00/Engine/WebOS.SecurityScan.php)
- **Class**: `WebOS_SecurityScan`
- **Facade Entry Points**:
  - `WebOS.SecurityScan()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SecurityScanRun()` → `public static function Run(array $Payload, array $Context)`
  - `WebOS.SecurityScanGate()` → `public static function Gate(array $Payload, array $Context)`
  - `WebOS.SecurityScanSummary()` → `public static function Summary(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SecurityScan()` |
| Run | `public static function Run(array $Payload, array $Context)` | `WebOS.SecurityScanRun()` |
| Gate | `public static function Gate(array $Payload, array $Context)` | `WebOS.SecurityScanGate()` |
| Summary | `public static function Summary(array $Payload, array $Context)` | `WebOS.SecurityScanSummary()` |
### <span id="webos-selftest"></span>WebOS.SelfTest
- **Purpose**: Self-healing engine for WebOS Detect, plan, apply and rollback system repairs Multi-App + Multi-Tenant safe Policy driven, audit ready, zero-downtime friendly
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SelfTest.php](WebOS/PHP.26.00.00/Engine/WebOS.SelfTest.php)
- **Class**: `WebOS_SelfRepair`
- **Facade Entry Points**:
  - `WebOS.SelfTest()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SelfTestStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.SelfTestScan()` → `public static function Scan(array $Payload, array $Context)`
  - `WebOS.SelfTestPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.SelfTestApply()` → `public static function Apply(array $Payload, array $Context)`
  - `WebOS.SelfTestRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `WebOS.SelfTestHistory()` → `public static function History(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SelfTest()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.SelfTestStatus()` |
| Scan | `public static function Scan(array $Payload, array $Context)` | `WebOS.SelfTestScan()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.SelfTestPlan()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `WebOS.SelfTestApply()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.SelfTestRollback()` |
| History | `public static function History(array $Payload, array $Context)` | `WebOS.SelfTestHistory()` |
### <span id="webos-semantic"></span>WebOS.Semantic
- **Purpose**: Provide semantic intelligence layer for WebOS Meaning-based search, indexing, embeddings & ranking Multi-App, Multi-Tenant, Enterprise-grade isolation
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Semantic.php](WebOS/PHP.26.00.00/Engine/WebOS.Semantic.php)
- **Class**: `WebOS_Semantic`
- **Facade Entry Points**:
  - `WebOS.Semantic()` → `public static function Main(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Semantic()` |
### <span id="webos-server"></span>WebOS.Server
- **Purpose**: Server-level Operating System engine for WebOS Provides node identity, health, capacity, modes & cluster signals Works across Single Server → Multi-Server → Cluster Multi-App + Multi-Tenant aware
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Server.php](WebOS/PHP.26.00.00/Engine/WebOS.Server.php)
- **Class**: `WebOS_Server`
- **Facade Entry Points**:
  - `WebOS.Server()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ServerInfo()` → `public static function Info(array $Payload, array $Context)`
  - `WebOS.ServerServerId()` → `public static function ServerId(array $Payload, array $Context)`
  - `WebOS.ServerHealth()` → `public static function Health(array $Payload, array $Context)`
  - `WebOS.ServerHealthDeep()` → `public static function HealthDeep(array $Payload, array $Context)`
  - `WebOS.ServerCapacity()` → `public static function Capacity(array $Payload, array $Context)`
  - `WebOS.ServerMaintenanceOn()` → `public static function MaintenanceOn(array $Payload, array $Context)`
  - `WebOS.ServerMaintenanceOff()` → `public static function MaintenanceOff(array $Payload, array $Context)`
  - `WebOS.ServerRegister()` → `public static function Register(array $Payload, array $Context)`
  - `WebOS.ServerHeartbeat()` → `public static function Heartbeat(array $Payload, array $Context)`
  - `WebOS.ServerSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Server()` |
| Info | `public static function Info(array $Payload, array $Context)` | `WebOS.ServerInfo()` |
| ServerId | `public static function ServerId(array $Payload, array $Context)` | `WebOS.ServerServerId()` |
| Health | `public static function Health(array $Payload, array $Context)` | `WebOS.ServerHealth()` |
| HealthDeep | `public static function HealthDeep(array $Payload, array $Context)` | `WebOS.ServerHealthDeep()` |
| Capacity | `public static function Capacity(array $Payload, array $Context)` | `WebOS.ServerCapacity()` |
| MaintenanceOn | `public static function MaintenanceOn(array $Payload, array $Context)` | `WebOS.ServerMaintenanceOn()` |
| MaintenanceOff | `public static function MaintenanceOff(array $Payload, array $Context)` | `WebOS.ServerMaintenanceOff()` |
| Register | `public static function Register(array $Payload, array $Context)` | `WebOS.ServerRegister()` |
| Heartbeat | `public static function Heartbeat(array $Payload, array $Context)` | `WebOS.ServerHeartbeat()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `WebOS.ServerSnapshot()` |
### <span id="webos-session"></span>WebOS.Session
- **Purpose**: Enterprise-grade Session Operating Layer for WebOS (Multi-App, Multi-Tenant) Supports Browser Cookie Sessions + API Bearer Sessions + Refresh Tokens High-performance Validate() hot-path with Cache-first strategy Force revoke, revoke-all, rotation, touch throttling, device binding hooks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Session.php](WebOS/PHP.26.00.00/Engine/WebOS.Session.php)
- **Class**: `WebOS_Session`
- **Facade Entry Points**:
  - `WebOS.Session()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SessionStart()` → `public static function Start(array $Payload, array $Context)`
  - `WebOS.SessionValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.SessionGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SessionTouch()` → `public static function Touch(array $Payload, array $Context)`
  - `WebOS.SessionRefresh()` → `public static function Refresh(array $Payload, array $Context)`
  - `WebOS.SessionRotate()` → `public static function Rotate(array $Payload, array $Context)`
  - `WebOS.SessionRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `WebOS.SessionRevokeAll()` → `public static function RevokeAll(array $Payload, array $Context)`
  - `WebOS.SessionList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.SessionDestroy()` → `public static function Destroy(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Session()` |
| Start | `public static function Start(array $Payload, array $Context)` | `WebOS.SessionStart()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.SessionValidate()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SessionGet()` |
| Touch | `public static function Touch(array $Payload, array $Context)` | `WebOS.SessionTouch()` |
| Refresh | `public static function Refresh(array $Payload, array $Context)` | `WebOS.SessionRefresh()` |
| Rotate | `public static function Rotate(array $Payload, array $Context)` | `WebOS.SessionRotate()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `WebOS.SessionRevoke()` |
| RevokeAll | `public static function RevokeAll(array $Payload, array $Context)` | `WebOS.SessionRevokeAll()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.SessionList()` |
| Destroy | `public static function Destroy(array $Payload, array $Context)` | `WebOS.SessionDestroy()` |
### <span id="webos-shard"></span>WebOS.Shard
- **Purpose**: Enterprise-grade Sharding Engine for WebOS (Multi-App + Multi-Tenant) Deterministic shard routing (tenant/user/table/module) Directory-based shard map with cache-first hot-path Read/Write split awareness + policy hooks Admin controls: MapSet, NodeRegister, NodeStatus, KeyMove (RBAC-ready) Observability: Explain() + structured debug meta
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Shard.php](WebOS/PHP.26.00.00/Engine/WebOS.Shard.php)
- **Class**: `WebOS_Shard`
- **Facade Entry Points**:
  - `WebOS.Shard()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ShardResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.ShardResolveBatch()` → `public static function ResolveBatch(array $Payload, array $Context)`
  - `WebOS.ShardMapGet()` → `public static function MapGet(array $Payload, array $Context)`
  - `WebOS.ShardMapSet()` → `public static function MapSet(array $Payload, array $Context)`
  - `WebOS.ShardNodeRegister()` → `public static function NodeRegister(array $Payload, array $Context)`
  - `WebOS.ShardNodeStatus()` → `public static function NodeStatus(array $Payload, array $Context)`
  - `WebOS.ShardKeyMove()` → `public static function KeyMove(array $Payload, array $Context)`
  - `WebOS.ShardPlanRebalance()` → `public static function PlanRebalance(array $Payload, array $Context)`
  - `WebOS.ShardExecuteRebalance()` → `public static function ExecuteRebalance(array $Payload, array $Context)`
  - `WebOS.ShardExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Shard()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.ShardResolve()` |
| ResolveBatch | `public static function ResolveBatch(array $Payload, array $Context)` | `WebOS.ShardResolveBatch()` |
| MapGet | `public static function MapGet(array $Payload, array $Context)` | `WebOS.ShardMapGet()` |
| MapSet | `public static function MapSet(array $Payload, array $Context)` | `WebOS.ShardMapSet()` |
| NodeRegister | `public static function NodeRegister(array $Payload, array $Context)` | `WebOS.ShardNodeRegister()` |
| NodeStatus | `public static function NodeStatus(array $Payload, array $Context)` | `WebOS.ShardNodeStatus()` |
| KeyMove | `public static function KeyMove(array $Payload, array $Context)` | `WebOS.ShardKeyMove()` |
| PlanRebalance | `public static function PlanRebalance(array $Payload, array $Context)` | `WebOS.ShardPlanRebalance()` |
| ExecuteRebalance | `public static function ExecuteRebalance(array $Payload, array $Context)` | `WebOS.ShardExecuteRebalance()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `WebOS.ShardExplain()` |
### <span id="webos-share"></span>WebOS.Share
- **Purpose**: Enterprise Grade Share System (Multi-App + Multi-Tenant) Create secure share links (tokenized, scoped, revoke-able) Validate/Open share (auth/role/invite/expiry/max_uses) High-performance: cache-friendly, audit-ready, abuse-ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Share.php](WebOS/PHP.26.00.00/Engine/WebOS.Share.php)
- **Class**: `WebOS_Share`
- **Facade Entry Points**:
  - `WebOS.Share()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ShareNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.ShareGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ShareOpen()` → `public static function Open(array $Payload, array $Context)`
  - `WebOS.ShareRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `WebOS.ShareValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.ShareResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.ShareList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.ShareStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Share()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.ShareNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ShareGet()` |
| Open | `public static function Open(array $Payload, array $Context)` | `WebOS.ShareOpen()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `WebOS.ShareRevoke()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.ShareValidate()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.ShareResolve()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.ShareList()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `WebOS.ShareStats()` |
### <span id="webos-shipping"></span>WebOS.Shipping
- **Purpose**: Enterprise-grade Shipping & Logistics Engine Multi-App + Multi-Tenant + OS-Level abstraction Carrier-agnostic, Rule-driven, Auditable shipping system
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Shipping.php](WebOS/PHP.26.00.00/Engine/WebOS.Shipping.php)
- **Class**: `WebOS_Shipping`
- **Facade Entry Points**:
  - `WebOS.Shipping()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ShippingQuote()` → `public static function Quote(array $Payload, array $Context)`
  - `WebOS.ShippingCreate()` → `public static function Create(array $Payload, array $Context)`
  - `WebOS.ShippingLabelGenerate()` → `public static function LabelGenerate(array $Payload, array $Context)`
  - `WebOS.ShippingTrack()` → `public static function Track(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Shipping()` |
| Quote | `public static function Quote(array $Payload, array $Context)` | `WebOS.ShippingQuote()` |
| Create | `public static function Create(array $Payload, array $Context)` | `WebOS.ShippingCreate()` |
| LabelGenerate | `public static function LabelGenerate(array $Payload, array $Context)` | `WebOS.ShippingLabelGenerate()` |
| Track | `public static function Track(array $Payload, array $Context)` | `WebOS.ShippingTrack()` |
### <span id="webos-signal"></span>WebOS.Signal
- **Purpose**: WebOS Control-Plane Signaling Engine (OS-Level Signals) Multi-App + Multi-Tenant safe signaling (Scope enforced) High-performance signals using adapters (Cache default; DB/Redis ready) Supports Broadcast/Unicast/Multicast, TTL, Priority, Ack, Retry metadata Boot files call only: WebOS::SignalSend(), WebOS::SignalListen(), etc.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Signal.php](WebOS/PHP.26.00.00/Engine/WebOS.Signal.php)
- **Class**: `WebOS_Signal`
- **Facade Entry Points**:
  - `WebOS.Signal()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.SignalSend()` → `public static function Send($Payload = [], $Context = [])`
  - `WebOS.SignalEmit()` → `public static function Emit($Payload = [], $Context = [])`
  - `WebOS.SignalBroadcast()` → `public static function Broadcast($Payload = [], $Context = [])`
  - `WebOS.SignalSendMany()` → `public static function SendMany($Payload = [], $Context = [])`
  - `WebOS.SignalListen()` → `public static function Listen($Payload = [], $Context = [])`
  - `WebOS.SignalAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `WebOS.SignalAckMany()` → `public static function AckMany($Payload = [], $Context = [])`
  - `WebOS.SignalGet()` → `public static function Get($Payload = [], $Context = [])`
  - `WebOS.SignalPeek()` → `public static function Peek($Payload = [], $Context = [])`
  - `WebOS.SignalPurge()` → `public static function Purge($Payload = [], $Context = [])`
  - `WebOS.SignalStats()` → `public static function Stats($Payload = [], $Context = [])`
  - `WebOS.SignalConfig()` → `public static function Config($Payload = [], $Context = [])`
  - `WebOS.SignalLock()` → `public static function Lock($Payload = [], $Context = [])`
  - `WebOS.SignalUnlock()` → `public static function Unlock($Payload = [], $Context = [])`
  - `WebOS.SignalDeadLetter()` → `public static function DeadLetter($Payload = [], $Context = [])`
  - `WebOS.SignalReplay()` → `public static function Replay($Payload = [], $Context = [])`
  - `WebOS.SignalTrace()` → `public static function Trace($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Signal()` |
| Send | `public static function Send($Payload = [], $Context = [])` | `WebOS.SignalSend()` |
| Emit | `public static function Emit($Payload = [], $Context = [])` | `WebOS.SignalEmit()` |
| Broadcast | `public static function Broadcast($Payload = [], $Context = [])` | `WebOS.SignalBroadcast()` |
| SendMany | `public static function SendMany($Payload = [], $Context = [])` | `WebOS.SignalSendMany()` |
| Listen | `public static function Listen($Payload = [], $Context = [])` | `WebOS.SignalListen()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `WebOS.SignalAck()` |
| AckMany | `public static function AckMany($Payload = [], $Context = [])` | `WebOS.SignalAckMany()` |
| Get | `public static function Get($Payload = [], $Context = [])` | `WebOS.SignalGet()` |
| Peek | `public static function Peek($Payload = [], $Context = [])` | `WebOS.SignalPeek()` |
| Purge | `public static function Purge($Payload = [], $Context = [])` | `WebOS.SignalPurge()` |
| Stats | `public static function Stats($Payload = [], $Context = [])` | `WebOS.SignalStats()` |
| Config | `public static function Config($Payload = [], $Context = [])` | `WebOS.SignalConfig()` |
| Lock | `public static function Lock($Payload = [], $Context = [])` | `WebOS.SignalLock()` |
| Unlock | `public static function Unlock($Payload = [], $Context = [])` | `WebOS.SignalUnlock()` |
| DeadLetter | `public static function DeadLetter($Payload = [], $Context = [])` | `WebOS.SignalDeadLetter()` |
| Replay | `public static function Replay($Payload = [], $Context = [])` | `WebOS.SignalReplay()` |
| Trace | `public static function Trace($Payload = [], $Context = [])` | `WebOS.SignalTrace()` |
### <span id="webos-signature"></span>WebOS.Signature
- **Purpose**: Enterprise-grade request signing & verification engine Protect APIs, Webhooks, internal service calls Prevent tampering, replay attacks, cross-tenant abuse
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Signature.php](WebOS/PHP.26.00.00/Engine/WebOS.Signature.php)
- **Class**: `WebOS_Signature`
- **Facade Entry Points**:
  - `WebOS.Signature()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SignatureSign()` → `public static function Sign(array $Payload, array $Context)`
  - `WebOS.SignatureVerify()` → `public static function Verify(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Signature()` |
| Sign | `public static function Sign(array $Payload, array $Context)` | `WebOS.SignatureSign()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.SignatureVerify()` |
### <span id="webos-simulation"></span>WebOS.Simulation
- **Purpose**: Enterprise-grade Simulation Engine for WebOS (Multi-App + Multi-Tenant) Run realistic sandbox simulations of API/Cron/Webhook/Engine scenarios Deterministic runs (seed-based), policy-driven safety, audit-ready reports
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Simulation.php](WebOS/PHP.26.00.00/Engine/WebOS.Simulation.php)
- **Class**: `as`
- **Facade Entry Points**:
  - `WebOS.Simulation()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationScenarioNew()` → `public static function ScenarioNew(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationScenarioGet()` → `public static function ScenarioGet(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationCompile()` → `public static function Compile(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationScenarioRun()` → `public static function ScenarioRun(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationRunGet()` → `public static function RunGet(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationRunList()` → `public static function RunList(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationAssert()` → `public static function Assert(array $Payload = [], array $Context = [])`
  - `WebOS.SimulationReport()` → `public static function Report(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Simulation()` |
| ScenarioNew | `public static function ScenarioNew(array $Payload = [], array $Context = [])` | `WebOS.SimulationScenarioNew()` |
| ScenarioGet | `public static function ScenarioGet(array $Payload = [], array $Context = [])` | `WebOS.SimulationScenarioGet()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `WebOS.SimulationValidate()` |
| Compile | `public static function Compile(array $Payload = [], array $Context = [])` | `WebOS.SimulationCompile()` |
| ScenarioRun | `public static function ScenarioRun(array $Payload = [], array $Context = [])` | `WebOS.SimulationScenarioRun()` |
| RunGet | `public static function RunGet(array $Payload = [], array $Context = [])` | `WebOS.SimulationRunGet()` |
| RunList | `public static function RunList(array $Payload = [], array $Context = [])` | `WebOS.SimulationRunList()` |
| Assert | `public static function Assert(array $Payload = [], array $Context = [])` | `WebOS.SimulationAssert()` |
| Report | `public static function Report(array $Payload = [], array $Context = [])` | `WebOS.SimulationReport()` |
### <span id="webos-smartcontract"></span>WebOS.SmartContract
- **Purpose**: Smart Contract Operating System Layer (Registry + Deploy + Call + Send) Multi-App + Multi-Tenant compatible (Context enforced) High-Performance, Cache-first, Queue-first (write paths async) Policy + RBAC gates (via WebOS engines) ----------------------------------------------------------------------------
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SmartContract.php](WebOS/PHP.26.00.00/Engine/WebOS.SmartContract.php)
- **Class**: `WebOS_SmartContract`
- **Facade Entry Points**:
  - `WebOS.SmartContract()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SmartContractNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.SmartContractGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SmartContractList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.SmartContractDeploy()` → `public static function Deploy(array $Payload, array $Context)`
  - `WebOS.SmartContractSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.SmartContractCall()` → `public static function Call(array $Payload, array $Context)`
  - `WebOS.SmartContractVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.SmartContractAudit()` → `public static function Audit(array $Payload, array $Context)`
  - `WebOS.SmartContractEvents()` → `public static function Events(array $Payload, array $Context)`
  - `WebOS.SmartContractGetFresh()` → `public static function GetFresh($TenantId, $AppCode, $Name, $ChainId)`
  - `WebOS.SmartContractCallFresh()` → `public static function CallFresh(array $Scope, array $Context, array $Contract, string $Method, $Args)`
  - `WebOS.SmartContractResolveActiveAddressFresh()` → `public static function ResolveActiveAddressFresh($TenantId, $AppCode, $Name, $ChainId)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SmartContract()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.SmartContractNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SmartContractGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.SmartContractList()` |
| Deploy | `public static function Deploy(array $Payload, array $Context)` | `WebOS.SmartContractDeploy()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.SmartContractSend()` |
| Call | `public static function Call(array $Payload, array $Context)` | `WebOS.SmartContractCall()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.SmartContractVerify()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `WebOS.SmartContractAudit()` |
| Events | `public static function Events(array $Payload, array $Context)` | `WebOS.SmartContractEvents()` |
| GetFresh | `public static function GetFresh($TenantId, $AppCode, $Name, $ChainId)` | `WebOS.SmartContractGetFresh()` |
| CallFresh | `public static function CallFresh(array $Scope, array $Context, array $Contract, string $Method, $Args)` | `WebOS.SmartContractCallFresh()` |
| ResolveActiveAddressFresh | `public static function ResolveActiveAddressFresh($TenantId, $AppCode, $Name, $ChainId)` | `WebOS.SmartContractResolveActiveAddressFresh()` |
### <span id="webos-sms"></span>WebOS.SMS
- **Purpose**: Enterprise-grade SMS orchestration layer for WebOS Backend OS Multi-App + Multi-Tenant provider-agnostic SMS sending OTP suite (Send OTP + Verify) with anti-abuse + idempotency hooks Delivery tracking hooks + callback processing hooks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SMS.php](WebOS/PHP.26.00.00/Engine/WebOS.SMS.php)
- **Class**: `WebOS_SMS`
- **Facade Entry Points**:
  - `WebOS.SMS()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SMSSend()` → `public static function Send(array $Payload, array $Context)`
  - `WebOS.SMSOTP()` → `public static function OTP(array $Payload, array $Context)`
  - `WebOS.SMSVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `WebOS.SMSStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.SMSCallback()` → `public static function Callback(array $Payload, array $Context)`
  - `WebOS.SMSProviderSet()` → `public static function ProviderSet(array $Payload, array $Context)`
  - `WebOS.SMSProviderTest()` → `public static function ProviderTest(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SMS()` |
| Send | `public static function Send(array $Payload, array $Context)` | `WebOS.SMSSend()` |
| OTP | `public static function OTP(array $Payload, array $Context)` | `WebOS.SMSOTP()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `WebOS.SMSVerify()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.SMSStatus()` |
| Callback | `public static function Callback(array $Payload, array $Context)` | `WebOS.SMSCallback()` |
| ProviderSet | `public static function ProviderSet(array $Payload, array $Context)` | `WebOS.SMSProviderSet()` |
| ProviderTest | `public static function ProviderTest(array $Payload, array $Context)` | `WebOS.SMSProviderTest()` |
### <span id="webos-socialgraph"></span>WebOS.SocialGraph
- **Purpose**: Enterprise-grade Social Graph kernel for Multi-App + Multi-Tenant WebOS Generic Node↔Node relationships (FOLLOW, FRIEND, BLOCK, MUTE, MEMBER...) High-performance reads via counters + cache + pagination Deterministic scoping via Context (tenant/app/brand/ecosystem) Idempotent writes + audit events + cache invalidation hooks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.SocialGraph.php](WebOS/PHP.26.00.00/Engine/WebOS.SocialGraph.php)
- **Class**: `WebOS_SocialGraph`
- **Facade Entry Points**:
  - `WebOS.SocialGraph()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SocialGraphLink()` → `public static function Link(array $Payload, array $Context)`
  - `WebOS.SocialGraphUnlink()` → `public static function Unlink(array $Payload, array $Context)`
  - `WebOS.SocialGraphFollow()` → `public static function Follow(array $Payload, array $Context)`
  - `WebOS.SocialGraphUnfollow()` → `public static function Unfollow(array $Payload, array $Context)`
  - `WebOS.SocialGraphBlock()` → `public static function Block(array $Payload, array $Context)`
  - `WebOS.SocialGraphUnblock()` → `public static function Unblock(array $Payload, array $Context)`
  - `WebOS.SocialGraphIsLinked()` → `public static function IsLinked(array $Payload, array $Context)`
  - `WebOS.SocialGraphGetFollowers()` → `public static function GetFollowers(array $Payload, array $Context)`
  - `WebOS.SocialGraphGetFollowing()` → `public static function GetFollowing(array $Payload, array $Context)`
  - `WebOS.SocialGraphCount()` → `public static function Count(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.SocialGraph()` |
| Link | `public static function Link(array $Payload, array $Context)` | `WebOS.SocialGraphLink()` |
| Unlink | `public static function Unlink(array $Payload, array $Context)` | `WebOS.SocialGraphUnlink()` |
| Follow | `public static function Follow(array $Payload, array $Context)` | `WebOS.SocialGraphFollow()` |
| Unfollow | `public static function Unfollow(array $Payload, array $Context)` | `WebOS.SocialGraphUnfollow()` |
| Block | `public static function Block(array $Payload, array $Context)` | `WebOS.SocialGraphBlock()` |
| Unblock | `public static function Unblock(array $Payload, array $Context)` | `WebOS.SocialGraphUnblock()` |
| IsLinked | `public static function IsLinked(array $Payload, array $Context)` | `WebOS.SocialGraphIsLinked()` |
| GetFollowers | `public static function GetFollowers(array $Payload, array $Context)` | `WebOS.SocialGraphGetFollowers()` |
| GetFollowing | `public static function GetFollowing(array $Payload, array $Context)` | `WebOS.SocialGraphGetFollowing()` |
| Count | `public static function Count(array $Payload, array $Context)` | `WebOS.SocialGraphCount()` |
### <span id="webos-socket"></span>WebOS.Socket
- **Purpose**: WebOS real-time backend backbone (WS/SSE/Long-Poll orchestration) Gateway + Backplane driven architecture (PHP = policy + routing + publish) Multi-App + Multi-Tenant isolation (Topic scope) Auth + Role/Permission enforcement (deny-by-default) Presence management (Redis/Cache-first), with audit-friendly payloads
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Socket.php](WebOS/PHP.26.00.00/Engine/WebOS.Socket.php)
- **Class**: `WebOS_Socket`
- **Facade Entry Points**:
  - `WebOS.Socket()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.SocketConfigGet()` → `public static function ConfigGet($Payload = [], $Context = [])`
  - `WebOS.SocketTopicBuild()` → `public static function TopicBuild($Payload = [], $Context = [])`
  - `WebOS.SocketTopicParse()` → `public static function TopicParse($Payload = [], $Context = [])`
  - `WebOS.SocketHandshake()` → `public static function Handshake($Payload = [], $Context = [])`
  - `WebOS.SocketAllow()` → `public static function Allow($Payload = [], $Context = [])`
  - `WebOS.SocketPublish()` → `public static function Publish($Payload = [], $Context = [])`
  - `WebOS.SocketBroadcast()` → `public static function Broadcast($Payload = [], $Context = [])`
  - `WebOS.SocketEmit()` → `public static function Emit($Payload = [], $Context = [])`
  - `WebOS.SocketSubscribeToken()` → `public static function SubscribeToken($Payload = [], $Context = [])`
  - `WebOS.SocketUnsubscribeToken()` → `public static function UnsubscribeToken($Payload = [], $Context = [])`
  - `WebOS.SocketPresenceSet()` → `public static function PresenceSet($Payload = [], $Context = [])`
  - `WebOS.SocketPresenceGet()` → `public static function PresenceGet($Payload = [], $Context = [])`
  - `WebOS.SocketPresenceList()` → `public static function PresenceList($Payload = [], $Context = [])`
  - `WebOS.SocketAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `WebOS.SocketRetry()` → `public static function Retry($Payload = [], $Context = [])`
  - `WebOS.SocketDeadLetter()` → `public static function DeadLetter($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Socket()` |
| ConfigGet | `public static function ConfigGet($Payload = [], $Context = [])` | `WebOS.SocketConfigGet()` |
| TopicBuild | `public static function TopicBuild($Payload = [], $Context = [])` | `WebOS.SocketTopicBuild()` |
| TopicParse | `public static function TopicParse($Payload = [], $Context = [])` | `WebOS.SocketTopicParse()` |
| Handshake | `public static function Handshake($Payload = [], $Context = [])` | `WebOS.SocketHandshake()` |
| Allow | `public static function Allow($Payload = [], $Context = [])` | `WebOS.SocketAllow()` |
| Publish | `public static function Publish($Payload = [], $Context = [])` | `WebOS.SocketPublish()` |
| Broadcast | `public static function Broadcast($Payload = [], $Context = [])` | `WebOS.SocketBroadcast()` |
| Emit | `public static function Emit($Payload = [], $Context = [])` | `WebOS.SocketEmit()` |
| SubscribeToken | `public static function SubscribeToken($Payload = [], $Context = [])` | `WebOS.SocketSubscribeToken()` |
| UnsubscribeToken | `public static function UnsubscribeToken($Payload = [], $Context = [])` | `WebOS.SocketUnsubscribeToken()` |
| PresenceSet | `public static function PresenceSet($Payload = [], $Context = [])` | `WebOS.SocketPresenceSet()` |
| PresenceGet | `public static function PresenceGet($Payload = [], $Context = [])` | `WebOS.SocketPresenceGet()` |
| PresenceList | `public static function PresenceList($Payload = [], $Context = [])` | `WebOS.SocketPresenceList()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `WebOS.SocketAck()` |
| Retry | `public static function Retry($Payload = [], $Context = [])` | `WebOS.SocketRetry()` |
| DeadLetter | `public static function DeadLetter($Payload = [], $Context = [])` | `WebOS.SocketDeadLetter()` |
### <span id="webos-sorting"></span>WebOS.Sorting
- **Purpose**: Centralized, secure, deterministic sorting engine SQL-level + Memory-level sorting Multi-App, Multi-Tenant, Role-aware Stable sorting with forced tie-breakers Safe ORDER BY generation (no injection)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Sorting.php](WebOS/PHP.26.00.00/Engine/WebOS.Sorting.php)
- **Class**: `WebOS_Sorting`
- **Facade Entry Points**:
  - `WebOS.Sorting()` → `public static function Main($Payload, $Context)`
  - `WebOS.SortingPlan()` → `public static function Plan($Payload, $Context)`
  - `WebOS.SortingSql()` → `public static function Sql($Payload, $Context)`
  - `WebOS.SortingApply()` → `public static function Apply($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.Sorting()` |
| Plan | `public static function Plan($Payload, $Context)` | `WebOS.SortingPlan()` |
| Sql | `public static function Sql($Payload, $Context)` | `WebOS.SortingSql()` |
| Apply | `public static function Apply($Payload, $Context)` | `WebOS.SortingApply()` |
### <span id="webos-state"></span>WebOS.State
- **Purpose**: OS-level State Management for WebOS (Multi-App + Multi-Tenant) Strong State Read/Write + Atomic Transitions + Audit/History High-performance cache-first reads with DB as source of truth Race-condition safe via Lock + ExpectedVersion (optimistic) Event-ready (optional publish) and policy-ready (optional validate)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.State.php](WebOS/PHP.26.00.00/Engine/WebOS.State.php)
- **Class**: `WebOS_State`
- **Facade Entry Points**:
  - `WebOS.State()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.StateGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.StateExists()` → `public static function Exists(array $Payload = [], array $Context = [])`
  - `WebOS.StateSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `WebOS.StateNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `WebOS.StateUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `WebOS.StateDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `WebOS.StateTransition()` → `public static function Transition(array $Payload = [], array $Context = [])`
  - `WebOS.StateBatchGet()` → `public static function BatchGet(array $Payload = [], array $Context = [])`
  - `WebOS.StateBatchSet()` → `public static function BatchSet(array $Payload = [], array $Context = [])`
  - `WebOS.StateHistory()` → `public static function History(array $Payload = [], array $Context = [])`
  - `WebOS.StateExpire()` → `public static function Expire(array $Payload = [], array $Context = [])`
  - `WebOS.StateRefresh()` → `public static function Refresh(array $Payload = [], array $Context = [])`
  - `WebOS.StateCleanup()` → `public static function Cleanup(array $Payload = [], array $Context = [])`
  - `WebOS.StateExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.State()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.StateGet()` |
| Exists | `public static function Exists(array $Payload = [], array $Context = [])` | `WebOS.StateExists()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `WebOS.StateSet()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `WebOS.StateNew()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `WebOS.StateUpdate()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `WebOS.StateDelete()` |
| Transition | `public static function Transition(array $Payload = [], array $Context = [])` | `WebOS.StateTransition()` |
| BatchGet | `public static function BatchGet(array $Payload = [], array $Context = [])` | `WebOS.StateBatchGet()` |
| BatchSet | `public static function BatchSet(array $Payload = [], array $Context = [])` | `WebOS.StateBatchSet()` |
| History | `public static function History(array $Payload = [], array $Context = [])` | `WebOS.StateHistory()` |
| Expire | `public static function Expire(array $Payload = [], array $Context = [])` | `WebOS.StateExpire()` |
| Refresh | `public static function Refresh(array $Payload = [], array $Context = [])` | `WebOS.StateRefresh()` |
| Cleanup | `public static function Cleanup(array $Payload = [], array $Context = [])` | `WebOS.StateCleanup()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `WebOS.StateExplain()` |
### <span id="webos-stream"></span>WebOS.Stream
- **Purpose**: High-Performance Streaming Engine for WebOS Supports SSE, chunked streaming, publish/subscribe Multi-App + Multi-Tenant safe Enterprise-grade audit, policy & observability ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Stream.php](WebOS/PHP.26.00.00/Engine/WebOS.Stream.php)
- **Class**: `WebOS_Stream`
- **Facade Entry Points**:
  - `WebOS.Stream()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.StreamOpen()` → `public static function Open(array $Payload, array $Context)`
  - `WebOS.StreamPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `WebOS.StreamSubscribe()` → `public static function Subscribe(array $Payload, array $Context)`
  - `WebOS.StreamSSE()` → `public static function SSE(array $Payload, array $Context)`
  - `WebOS.StreamStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Stream()` |
| Open | `public static function Open(array $Payload, array $Context)` | `WebOS.StreamOpen()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `WebOS.StreamPublish()` |
| Subscribe | `public static function Subscribe(array $Payload, array $Context)` | `WebOS.StreamSubscribe()` |
| SSE | `public static function SSE(array $Payload, array $Context)` | `WebOS.StreamSSE()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `WebOS.StreamStats()` |
### <span id="webos-subscription"></span>WebOS.Subscription
- **Purpose**: Enterprise Grade Subscription Engine (Multi-App + Multi-Tenant) Plan Catalog + Subscription Lifecycle + Entitlements + Usage Metering (OS Layer) Deterministic State Machine + Audit Ready + Cache/Perf Ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Subscription.php](WebOS/PHP.26.00.00/Engine/WebOS.Subscription.php)
- **Class**: `WebOS_Subscription`
- **Facade Entry Points**:
  - `WebOS.Subscription()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.SubscriptionPlanList()` → `public static function PlanList(array $Payload, array $Context)`
  - `WebOS.SubscriptionPlanGet()` → `public static function PlanGet(array $Payload, array $Context)`
  - `WebOS.SubscriptionPlanNew()` → `public static function PlanNew(array $Payload, array $Context)`
  - `WebOS.SubscriptionPlanUpdate()` → `public static function PlanUpdate(array $Payload, array $Context)`
  - `WebOS.SubscriptionNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.SubscriptionGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.SubscriptionCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.SubscriptionPause()` → `public static function Pause(array $Payload, array $Context)`
  - `WebOS.SubscriptionResume()` → `public static function Resume(array $Payload, array $Context)`
  - `WebOS.SubscriptionEntitlementBuild()` → `public static function EntitlementBuild(array $Payload, array $Context)`
  - `WebOS.SubscriptionEntitlementCheck()` → `public static function EntitlementCheck(array $Payload, array $Context)`
  - `WebOS.SubscriptionLimitConsume()` → `public static function LimitConsume(array $Payload, array $Context)`
  - `WebOS.SubscriptionUsageAdd()` → `public static function UsageAdd(array $Payload, array $Context)`
  - `WebOS.SubscriptionUsageGet()` → `public static function UsageGet(array $Payload, array $Context)`
  - `WebOS.SubscriptionUpgrade()` → `public static function Upgrade(array $Payload, array $Context)`
  - `WebOS.SubscriptionDowngrade()` → `public static function Downgrade(array $Payload, array $Context)`
  - `WebOS.SubscriptionRenew()` → `public static function Renew(array $Payload, array $Context)`
  - `WebOS.SubscriptionProrationPreview()` → `public static function ProrationPreview(array $Payload, array $Context)`
  - `WebOS.SubscriptionWebhook()` → `public static function Webhook(array $Payload, array $Context)`
  - `WebOS.SubscriptionSync()` → `public static function Sync(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Subscription()` |
| PlanList | `public static function PlanList(array $Payload, array $Context)` | `WebOS.SubscriptionPlanList()` |
| PlanGet | `public static function PlanGet(array $Payload, array $Context)` | `WebOS.SubscriptionPlanGet()` |
| PlanNew | `public static function PlanNew(array $Payload, array $Context)` | `WebOS.SubscriptionPlanNew()` |
| PlanUpdate | `public static function PlanUpdate(array $Payload, array $Context)` | `WebOS.SubscriptionPlanUpdate()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.SubscriptionNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.SubscriptionGet()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.SubscriptionCancel()` |
| Pause | `public static function Pause(array $Payload, array $Context)` | `WebOS.SubscriptionPause()` |
| Resume | `public static function Resume(array $Payload, array $Context)` | `WebOS.SubscriptionResume()` |
| EntitlementBuild | `public static function EntitlementBuild(array $Payload, array $Context)` | `WebOS.SubscriptionEntitlementBuild()` |
| EntitlementCheck | `public static function EntitlementCheck(array $Payload, array $Context)` | `WebOS.SubscriptionEntitlementCheck()` |
| LimitConsume | `public static function LimitConsume(array $Payload, array $Context)` | `WebOS.SubscriptionLimitConsume()` |
| UsageAdd | `public static function UsageAdd(array $Payload, array $Context)` | `WebOS.SubscriptionUsageAdd()` |
| UsageGet | `public static function UsageGet(array $Payload, array $Context)` | `WebOS.SubscriptionUsageGet()` |
| Upgrade | `public static function Upgrade(array $Payload, array $Context)` | `WebOS.SubscriptionUpgrade()` |
| Downgrade | `public static function Downgrade(array $Payload, array $Context)` | `WebOS.SubscriptionDowngrade()` |
| Renew | `public static function Renew(array $Payload, array $Context)` | `WebOS.SubscriptionRenew()` |
| ProrationPreview | `public static function ProrationPreview(array $Payload, array $Context)` | `WebOS.SubscriptionProrationPreview()` |
| Webhook | `public static function Webhook(array $Payload, array $Context)` | `WebOS.SubscriptionWebhook()` |
| Sync | `public static function Sync(array $Payload, array $Context)` | `WebOS.SubscriptionSync()` |
### <span id="webos-tag"></span>WebOS.Tag
- **Purpose**: Universal Tag Engine for WebOS Multi-App + Multi-Tenant + High-Performance Entity-agnostic tagging (Post, Product, User, Order, etc.)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Tag.php](WebOS/PHP.26.00.00/Engine/WebOS.Tag.php)
- **Class**: `WebOS_Tag`
- **Facade Entry Points**:
  - `WebOS.Tag()` → `public static function Main($Payload, $Context)`
  - `WebOS.TagNew()` → `public static function New($Payload, $Context)`
  - `WebOS.TagGet()` → `public static function Get($Payload, $Context)`
  - `WebOS.TagAssign()` → `public static function Assign($Payload, $Context)`
  - `WebOS.TagUnassign()` → `public static function Unassign($Payload, $Context)`
  - `WebOS.TagList()` → `public static function List($Payload, $Context)`
  - `WebOS.TagNormalize()` → `public static function Normalize($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `WebOS.Tag()` |
| New | `public static function New($Payload, $Context)` | `WebOS.TagNew()` |
| Get | `public static function Get($Payload, $Context)` | `WebOS.TagGet()` |
| Assign | `public static function Assign($Payload, $Context)` | `WebOS.TagAssign()` |
| Unassign | `public static function Unassign($Payload, $Context)` | `WebOS.TagUnassign()` |
| List | `public static function List($Payload, $Context)` | `WebOS.TagList()` |
| Normalize | `public static function Normalize($Payload, $Context)` | `WebOS.TagNormalize()` |
### <span id="webos-task"></span>WebOS.Task
- **Purpose**: Universal Task Orchestration Engine for WebOS Convert any backend work into reliable Tasks Multi-App + Multi-Tenant safe execution Enterprise-grade retry, locking, auditing
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Task.php](WebOS/PHP.26.00.00/Engine/WebOS.Task.php)
- **Class**: `WebOS_Task`
- **Facade Entry Points**:
  - `WebOS.Task()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Task()` |
### <span id="webos-tax"></span>WebOS.Tax
- **Purpose**: Central Tax Decision & Calculation Engine for WebOS Works for any Website / WebApp / Mobile App / SaaS / Marketplace Multi-Tenant + Multi-App + Multi-Jurisdiction GST / VAT / SalesTax / Digital Goods / Reverse Charge ready
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Tax.php](WebOS/PHP.26.00.00/Engine/WebOS.Tax.php)
- **Class**: `WebOS_Tax`
- **Facade Entry Points**:
  - `WebOS.Tax()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.TaxCalculate()` → `public static function Calculate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Tax()` |
| Calculate | `public static function Calculate(array $Payload, array $Context)` | `WebOS.TaxCalculate()` |
### <span id="webos-template"></span>WebOS.Template
- **Purpose**: Enterprise-grade Template Rendering Engine for WebOS Multi-App + Multi-Brand + Multi-Tenant aware Secure, Cacheable, Overrideable View System
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Template.php](WebOS/PHP.26.00.00/Engine/WebOS.Template.php)
- **Class**: `WebOS_Template`
- **Facade Entry Points**:
  - `WebOS.Template()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.TemplateRender()` → `public static function Render(array $Payload, array $Context)`
  - `WebOS.TemplateResolve()` → `public static function Resolve(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Template()` |
| Render | `public static function Render(array $Payload, array $Context)` | `WebOS.TemplateRender()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.TemplateResolve()` |
### <span id="webos-tenant"></span>WebOS.Tenant
- **Purpose**: Enterprise-grade Multi-Tenant Runtime for WebOS (Multi-App + Multi-Brand) Deterministic Tenant resolution (Domain / Header / Path / Session) Tenant isolation metadata (Shared / Schema / DB-per-tenant / Hybrid shard) Tenant limits / quotas / governor switches Tenant keys & secrets lifecycle (verify / rotate / revoke) Tenant feature flags (enable/disable/config) High-performance caching (O(1) average) + negative caching
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Tenant.php](WebOS/PHP.26.00.00/Engine/WebOS.Tenant.php)
- **Class**: `WebOS_Tenant`
- **Facade Entry Points**:
  - `WebOS.Tenant()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.TenantResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.TenantCurrent()` → `public static function Current(array $Payload = [], array $Context = [])`
  - `WebOS.TenantGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.TenantNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `WebOS.TenantUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `WebOS.TenantDisable()` → `public static function Disable(array $Payload = [], array $Context = [])`
  - `WebOS.TenantDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `WebOS.TenantDomainBind()` → `public static function DomainBind(array $Payload = [], array $Context = [])`
  - `WebOS.TenantDomainUnbind()` → `public static function DomainUnbind(array $Payload = [], array $Context = [])`
  - `WebOS.TenantDomainList()` → `public static function DomainList(array $Payload = [], array $Context = [])`
  - `WebOS.TenantIsolationGet()` → `public static function IsolationGet(array $Payload = [], array $Context = [])`
  - `WebOS.TenantIsolationSet()` → `public static function IsolationSet(array $Payload = [], array $Context = [])`
  - `WebOS.TenantLimitGet()` → `public static function LimitGet(array $Payload = [], array $Context = [])`
  - `WebOS.TenantLimitSet()` → `public static function LimitSet(array $Payload = [], array $Context = [])`
  - `WebOS.TenantLimitCheck()` → `public static function LimitCheck(array $Payload = [], array $Context = [])`
  - `WebOS.TenantFeatureEnable()` → `public static function FeatureEnable(array $Payload = [], array $Context = [])`
  - `WebOS.TenantFeatureDisable()` → `public static function FeatureDisable(array $Payload = [], array $Context = [])`
  - `WebOS.TenantFeatureGet()` → `public static function FeatureGet(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Tenant()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.TenantResolve()` |
| Current | `public static function Current(array $Payload = [], array $Context = [])` | `WebOS.TenantCurrent()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.TenantGet()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `WebOS.TenantNew()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `WebOS.TenantUpdate()` |
| Disable | `public static function Disable(array $Payload = [], array $Context = [])` | `WebOS.TenantDisable()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `WebOS.TenantDelete()` |
| DomainBind | `public static function DomainBind(array $Payload = [], array $Context = [])` | `WebOS.TenantDomainBind()` |
| DomainUnbind | `public static function DomainUnbind(array $Payload = [], array $Context = [])` | `WebOS.TenantDomainUnbind()` |
| DomainList | `public static function DomainList(array $Payload = [], array $Context = [])` | `WebOS.TenantDomainList()` |
| IsolationGet | `public static function IsolationGet(array $Payload = [], array $Context = [])` | `WebOS.TenantIsolationGet()` |
| IsolationSet | `public static function IsolationSet(array $Payload = [], array $Context = [])` | `WebOS.TenantIsolationSet()` |
| LimitGet | `public static function LimitGet(array $Payload = [], array $Context = [])` | `WebOS.TenantLimitGet()` |
| LimitSet | `public static function LimitSet(array $Payload = [], array $Context = [])` | `WebOS.TenantLimitSet()` |
| LimitCheck | `public static function LimitCheck(array $Payload = [], array $Context = [])` | `WebOS.TenantLimitCheck()` |
| FeatureEnable | `public static function FeatureEnable(array $Payload = [], array $Context = [])` | `WebOS.TenantFeatureEnable()` |
| FeatureDisable | `public static function FeatureDisable(array $Payload = [], array $Context = [])` | `WebOS.TenantFeatureDisable()` |
| FeatureGet | `public static function FeatureGet(array $Payload = [], array $Context = [])` | `WebOS.TenantFeatureGet()` |
### <span id="webos-test"></span>WebOS.Test
- **Purpose**: Enterprise-grade Test Operating System for WebOS Multi-App + Multi-Brand + Multi-Tenant test runner (matrix mode) Engine/Router compliance tests (Main() mandatory, naming rules, routing) Security smoke tests + performance sampling + regression placeholders Reports: JSON / MD / TEXT / JUnit (basic) Designed for CI/CD release-gate (exit_code style response)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Test.php](WebOS/PHP.26.00.00/Engine/WebOS.Test.php)
- **Class**: `WebOS_Test`
- **Facade Entry Points**:
  - `WebOS.Test()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.TestSuite()` → `public static function Suite($Payload = [], $Context = [])`
  - `WebOS.TestCase()` → `public static function Case($Payload = [], $Context = [])`
  - `WebOS.TestRun()` → `public static function Run($Payload = [], $Context = [])`
  - `WebOS.TestMatrix()` → `public static function Matrix($Payload = [], $Context = [])`
  - `WebOS.TestReport()` → `public static function Report($Payload = [], $Context = [])`
  - `WebOS.TestEngineContract()` → `public static function EngineContract($Payload = [], $Context = [])`
  - `WebOS.TestRouterSmoke()` → `public static function RouterSmoke($Payload = [], $Context = [])`
  - `WebOS.TestSecurityInputSmoke()` → `public static function SecurityInputSmoke($Payload = [], $Context = [])`
  - `WebOS.TestAssertTrue()` → `public static function AssertTrue($Value, $Message = '')`
  - `WebOS.TestAssertEquals()` → `public static function AssertEquals($A, $B, $Message = '')`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Test()` |
| Suite | `public static function Suite($Payload = [], $Context = [])` | `WebOS.TestSuite()` |
| Case | `public static function Case($Payload = [], $Context = [])` | `WebOS.TestCase()` |
| Run | `public static function Run($Payload = [], $Context = [])` | `WebOS.TestRun()` |
| Matrix | `public static function Matrix($Payload = [], $Context = [])` | `WebOS.TestMatrix()` |
| Report | `public static function Report($Payload = [], $Context = [])` | `WebOS.TestReport()` |
| EngineContract | `public static function EngineContract($Payload = [], $Context = [])` | `WebOS.TestEngineContract()` |
| RouterSmoke | `public static function RouterSmoke($Payload = [], $Context = [])` | `WebOS.TestRouterSmoke()` |
| SecurityInputSmoke | `public static function SecurityInputSmoke($Payload = [], $Context = [])` | `WebOS.TestSecurityInputSmoke()` |
| AssertTrue | `public static function AssertTrue($Value, $Message = '')` | `WebOS.TestAssertTrue()` |
| AssertEquals | `public static function AssertEquals($A, $B, $Message = '')` | `WebOS.TestAssertEquals()` |
### <span id="webos-theme"></span>WebOS.Theme
- **Purpose**: OS-level Theme Engine for WebOS Multi-App, Multi-Brand, Multi-Tenant theme resolution Design Tokens, Assets, Templates, Variants Ultra-fast cached resolution with deterministic UID
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Theme.php](WebOS/PHP.26.00.00/Engine/WebOS.Theme.php)
- **Class**: `WebOS_Theme`
- **Facade Entry Points**:
  - `WebOS.Theme()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ThemeResolve()` → `public static function Resolve(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Theme()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.ThemeResolve()` |
### <span id="webos-timeout"></span>WebOS.Timeout
- **Purpose**: OS-grade Timeout / Deadline enforcement for: API, Cron, Webhook, Worker, Queue, Task, Pipeline (Unit + Step) Multi-App + Multi-Tenant safe budgets Nested scopes: Request → Unit → Step → SubStep Soft timeout (graceful) + Hard timeout (abort signal / optional throw)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Timeout.php](WebOS/PHP.26.00.00/Engine/WebOS.Timeout.php)
- **Class**: `WebOS_Timeout`
- **Facade Entry Points**:
  - `WebOS.Timeout()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.TimeoutStart()` → `public static function Start($Payload = [], $Context = [])`
  - `WebOS.TimeoutCheck()` → `public static function Check($Payload = [], $Context = [])`
  - `WebOS.TimeoutEnd()` → `public static function End($Payload = [], $Context = [])`
  - `WebOS.TimeoutBudget()` → `public static function Budget($Payload = [], $Context = [])`
  - `WebOS.TimeoutWrap()` → `public static function Wrap($Payload = [], $Context = [])`
  - `WebOS.TimeoutNow()` → `public static function Now($Payload = [], $Context = [])`
  - `WebOS.TimeoutDeadline()` → `public static function Deadline($Payload = [], $Context = [])`
  - `WebOS.TimeoutRemaining()` → `public static function Remaining($Payload = [], $Context = [])`
  - `WebOS.TimeoutKey()` → `public static function Key($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Timeout()` |
| Start | `public static function Start($Payload = [], $Context = [])` | `WebOS.TimeoutStart()` |
| Check | `public static function Check($Payload = [], $Context = [])` | `WebOS.TimeoutCheck()` |
| End | `public static function End($Payload = [], $Context = [])` | `WebOS.TimeoutEnd()` |
| Budget | `public static function Budget($Payload = [], $Context = [])` | `WebOS.TimeoutBudget()` |
| Wrap | `public static function Wrap($Payload = [], $Context = [])` | `WebOS.TimeoutWrap()` |
| Now | `public static function Now($Payload = [], $Context = [])` | `WebOS.TimeoutNow()` |
| Deadline | `public static function Deadline($Payload = [], $Context = [])` | `WebOS.TimeoutDeadline()` |
| Remaining | `public static function Remaining($Payload = [], $Context = [])` | `WebOS.TimeoutRemaining()` |
| Key | `public static function Key($Payload = [], $Context = [])` | `WebOS.TimeoutKey()` |
### <span id="webos-timezone"></span>WebOS.Timezone
- **Purpose**: Enterprise Grade Timezone Engine for Multi-App + Multi-Tenant WebOS Deterministic timezone resolution (Payload → User → Tenant → Brand → App → Ecosystem → UTC) High-performance (request-cache + optional cross-request cache) Safe parsing + UTC conversions + reporting boundaries (day/week/month) ----------------------------------------------------------------------------
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Timezone.php](WebOS/PHP.26.00.00/Engine/WebOS.Timezone.php)
- **Class**: `WebOS_Timezone`
- **Facade Entry Points**:
  - `WebOS.Timezone()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneNow()` → `public static function Now(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneToUTC()` → `public static function ToUTC(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneFromUTC()` → `public static function FromUTC(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneParse()` → `public static function Parse(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneFormat()` → `public static function Format(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneDayStartUTC()` → `public static function DayStartUTC(array $Payload = [], array $Context = [])`
  - `WebOS.TimezoneDayEndUTC()` → `public static function DayEndUTC(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Timezone()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `WebOS.TimezoneResolve()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `WebOS.TimezoneValidate()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.TimezoneGet()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `WebOS.TimezoneSet()` |
| Now | `public static function Now(array $Payload = [], array $Context = [])` | `WebOS.TimezoneNow()` |
| ToUTC | `public static function ToUTC(array $Payload = [], array $Context = [])` | `WebOS.TimezoneToUTC()` |
| FromUTC | `public static function FromUTC(array $Payload = [], array $Context = [])` | `WebOS.TimezoneFromUTC()` |
| Parse | `public static function Parse(array $Payload = [], array $Context = [])` | `WebOS.TimezoneParse()` |
| Format | `public static function Format(array $Payload = [], array $Context = [])` | `WebOS.TimezoneFormat()` |
| DayStartUTC | `public static function DayStartUTC(array $Payload = [], array $Context = [])` | `WebOS.TimezoneDayStartUTC()` |
| DayEndUTC | `public static function DayEndUTC(array $Payload = [], array $Context = [])` | `WebOS.TimezoneDayEndUTC()` |
### <span id="webos-toggle"></span>WebOS.Toggle
- **Purpose**: Enterprise-grade Feature Toggle / Flag / Kill-Switch Engine Multi-App, Multi-Brand, Multi-Tenant, User-aware resolution Ultra-fast read path using snapshot + cache Safe defaults + deterministic decisions
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Toggle.php](WebOS/PHP.26.00.00/Engine/WebOS.Toggle.php)
- **Class**: `WebOS_Toggle`
- **Facade Entry Points**:
  - `WebOS.Toggle()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.ToggleGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.ToggleValue()` → `public static function Value(array $Payload, array $Context)`
  - `WebOS.ToggleBatch()` → `public static function Batch(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Toggle()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.ToggleGet()` |
| Value | `public static function Value(array $Payload, array $Context)` | `WebOS.ToggleValue()` |
| Batch | `public static function Batch(array $Payload, array $Context)` | `WebOS.ToggleBatch()` |
### <span id="webos-trace"></span>WebOS.Trace
- **Purpose**: Distributed tracing for WebOS (API, Cron, Queue, Worker, Pipeline) Ultra-low overhead, async-safe, multi-tenant aware Correlates EngineRouter → Engine → DB → HTTP → Queue
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Trace.php](WebOS/PHP.26.00.00/Engine/WebOS.Trace.php)
- **Class**: `WebOS_Trace`
- **Facade Entry Points**:
  - `WebOS.Trace()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.TraceStart()` → `public static function Start(array $Payload = [], array $Context = [])`
  - `WebOS.TraceSpanStart()` → `public static function SpanStart(array $Payload = [], array $Context = [])`
  - `WebOS.TraceSpanEnd()` → `public static function SpanEnd(array $Payload = [], array $Context = [])`
  - `WebOS.TraceTag()` → `public static function Tag(array $Payload = [], array $Context = [])`
  - `WebOS.TraceError()` → `public static function Error(array $Payload = [], array $Context = [])`
  - `WebOS.TraceEnd()` → `public static function End(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Trace()` |
| Start | `public static function Start(array $Payload = [], array $Context = [])` | `WebOS.TraceStart()` |
| SpanStart | `public static function SpanStart(array $Payload = [], array $Context = [])` | `WebOS.TraceSpanStart()` |
| SpanEnd | `public static function SpanEnd(array $Payload = [], array $Context = [])` | `WebOS.TraceSpanEnd()` |
| Tag | `public static function Tag(array $Payload = [], array $Context = [])` | `WebOS.TraceTag()` |
| Error | `public static function Error(array $Payload = [], array $Context = [])` | `WebOS.TraceError()` |
| End | `public static function End(array $Payload = [], array $Context = [])` | `WebOS.TraceEnd()` |
### <span id="webos-transaction"></span>WebOS.Transaction
- **Purpose**: Universal Transaction Engine for WebOS Supports any backend: Wallet, E-Commerce, SaaS, MLM, Banking-style systems Multi-Tenant, Multi-App, Enterprise-grade integrity
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Transaction.php](WebOS/PHP.26.00.00/Engine/WebOS.Transaction.php)
- **Class**: `WebOS_Transaction`
- **Facade Entry Points**:
  - `WebOS.Transaction()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.TransactionNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.TransactionGet()` → `public static function Get(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Transaction()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.TransactionNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.TransactionGet()` |
### <span id="webos-twofactor"></span>WebOS.TwoFactor
- **Purpose**: Provide enterprise‑grade Two‑Factor Authentication (2FA) Support TOTP, Email OTP, SMS OTP, Backup Codes (extensible) Policy‑driven, risk‑aware, step‑up authentication Fully compatible with WebOS.Loader + WebOS.EngineRouter
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.TwoFactor.php](WebOS/PHP.26.00.00/Engine/WebOS.TwoFactor.php)
- **Class**: `WebOS_TwoFactor`
- **Facade Entry Points**:
  - `WebOS.TwoFactor()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.TwoFactorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.TwoFactorChallengeNew()` → `public static function ChallengeNew(array $Payload, array $Context)`
  - `WebOS.TwoFactorChallengeVerify()` → `public static function ChallengeVerify(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.TwoFactor()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.TwoFactorStatus()` |
| ChallengeNew | `public static function ChallengeNew(array $Payload, array $Context)` | `WebOS.TwoFactorChallengeNew()` |
| ChallengeVerify | `public static function ChallengeVerify(array $Payload, array $Context)` | `WebOS.TwoFactorChallengeVerify()` |
### <span id="webos-update"></span>WebOS.Update
- **Purpose**: Enterprise-grade Update Orchestration Engine Core / SDK / App / Brand / Tenant updates Zero-downtime (Blue-Green / Canary / Rolling) Safe rollback + audit trail Fully Loader + EngineRouter compatible
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Update.php](WebOS/PHP.26.00.00/Engine/WebOS.Update.php)
- **Class**: `WebOS_Update`
- **Facade Entry Points**:
  - `WebOS.Update()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.UpdateCheck()` → `public static function Check(array $Payload, array $Context)`
  - `WebOS.UpdatePlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.UpdateApply()` → `public static function Apply(array $Payload, array $Context)`
  - `WebOS.UpdateRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `WebOS.UpdateStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Update()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.UpdateCheck()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.UpdatePlan()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `WebOS.UpdateApply()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.UpdateRollback()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.UpdateStatus()` |
### <span id="webos-upgradeplanner"></span>WebOS.UpgradePlanner
- **Purpose**: Deterministic upgrade planning for WebOS ecosystem Multi-App, Multi-Brand, Multi-Tenant safe rollout planning Zero-downtime upgrade strategy generation Risk analysis, dependency ordering, rollback planning
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.UpgradePlanner.php](WebOS/PHP.26.00.00/Engine/WebOS.UpgradePlanner.php)
- **Class**: `WebOS_UpgradePlanner`
- **Facade Entry Points**:
  - `WebOS.UpgradePlanner()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.UpgradePlannerPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `WebOS.UpgradePlannerRisk()` → `public static function Risk(array $Payload, array $Context)`
  - `WebOS.UpgradePlannerSteps()` → `public static function Steps(array $Payload, array $Context)`
  - `WebOS.UpgradePlannerRollout()` → `public static function Rollout(array $Payload, array $Context)`
  - `WebOS.UpgradePlannerRollback()` → `public static function Rollback(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.UpgradePlanner()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `WebOS.UpgradePlannerPlan()` |
| Risk | `public static function Risk(array $Payload, array $Context)` | `WebOS.UpgradePlannerRisk()` |
| Steps | `public static function Steps(array $Payload, array $Context)` | `WebOS.UpgradePlannerSteps()` |
| Rollout | `public static function Rollout(array $Payload, array $Context)` | `WebOS.UpgradePlannerRollout()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `WebOS.UpgradePlannerRollback()` |
### <span id="webos-upload"></span>WebOS.Upload
- **Purpose**: Enterprise-grade Upload Engine for WebOS Multi-App, Multi-Tenant, High-Performance Upload OS Layer Supports chunked, resumable, secure uploads
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Upload.php](WebOS/PHP.26.00.00/Engine/WebOS.Upload.php)
- **Class**: `WebOS_Upload`
- **Facade Entry Points**:
  - `WebOS.Upload()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.UploadInit()` → `public static function Init(array $Payload, array $Context)`
  - `WebOS.UploadChunk()` → `public static function Chunk(array $Payload, array $Context)`
  - `WebOS.UploadCommit()` → `public static function Commit(array $Payload, array $Context)`
  - `WebOS.UploadAbort()` → `public static function Abort(array $Payload, array $Context)`
  - `WebOS.UploadGet()` → `public static function Get(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Upload()` |
| Init | `public static function Init(array $Payload, array $Context)` | `WebOS.UploadInit()` |
| Chunk | `public static function Chunk(array $Payload, array $Context)` | `WebOS.UploadChunk()` |
| Commit | `public static function Commit(array $Payload, array $Context)` | `WebOS.UploadCommit()` |
| Abort | `public static function Abort(array $Payload, array $Context)` | `WebOS.UploadAbort()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.UploadGet()` |
### <span id="webos-user"></span>WebOS.User
- **Purpose**: Enterprise Grade User Engine for WebOS (Multi-App + Multi-Tenant) High-performance read/write with cache-first + strict scope enforcement Centralized user identity, meta, roles integration points OS-grade deterministic outputs, safe errors, audit-ready events
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.User.php](WebOS/PHP.26.00.00/Engine/WebOS.User.php)
- **Class**: `WebOS_User`
- **Facade Entry Points**:
  - `WebOS.User()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.User()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.UserActions()` → `public static function Actions()`
  - `WebOS.UserGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.UserFind()` → `public static function Find(array $Payload, array $Context)`
  - `WebOS.UserNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.UserUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `WebOS.UserStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.UserGetByEmail()` → `public static function GetByEmail(array $Payload, array $Context)`
  - `WebOS.UserGetByUserName()` → `public static function GetByUserName(array $Payload, array $Context)`
  - `WebOS.UserGetByPhone()` → `public static function GetByPhone(array $Payload, array $Context)`
  - `WebOS.UserMetaGet()` → `public static function MetaGet(array $Payload, array $Context)`
  - `WebOS.UserMetaSet()` → `public static function MetaSet(array $Payload, array $Context)`
  - `WebOS.UserMetaDelete()` → `public static function MetaDelete(array $Payload, array $Context)`
  - `WebOS.UserMetaBulkGet()` → `public static function MetaBulkGet(array $Payload, array $Context)`
  - `WebOS.UserMetaBulkSet()` → `public static function MetaBulkSet(array $Payload, array $Context)`
  - `WebOS.UserRoleGrant()` → `public static function RoleGrant(array $Payload, array $Context)`
  - `WebOS.UserRoleRevoke()` → `public static function RoleRevoke(array $Payload, array $Context)`
  - `WebOS.UserRoleList()` → `public static function RoleList(array $Payload, array $Context)`
  - `WebOS.UserHasRole()` → `public static function HasRole(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.User()` |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.User()` |
| Actions | `public static function Actions()` | `WebOS.UserActions()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.UserGet()` |
| Find | `public static function Find(array $Payload, array $Context)` | `WebOS.UserFind()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.UserNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `WebOS.UserUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.UserStatus()` |
| GetByEmail | `public static function GetByEmail(array $Payload, array $Context)` | `WebOS.UserGetByEmail()` |
| GetByUserName | `public static function GetByUserName(array $Payload, array $Context)` | `WebOS.UserGetByUserName()` |
| GetByPhone | `public static function GetByPhone(array $Payload, array $Context)` | `WebOS.UserGetByPhone()` |
| MetaGet | `public static function MetaGet(array $Payload, array $Context)` | `WebOS.UserMetaGet()` |
| MetaSet | `public static function MetaSet(array $Payload, array $Context)` | `WebOS.UserMetaSet()` |
| MetaDelete | `public static function MetaDelete(array $Payload, array $Context)` | `WebOS.UserMetaDelete()` |
| MetaBulkGet | `public static function MetaBulkGet(array $Payload, array $Context)` | `WebOS.UserMetaBulkGet()` |
| MetaBulkSet | `public static function MetaBulkSet(array $Payload, array $Context)` | `WebOS.UserMetaBulkSet()` |
| RoleGrant | `public static function RoleGrant(array $Payload, array $Context)` | `WebOS.UserRoleGrant()` |
| RoleRevoke | `public static function RoleRevoke(array $Payload, array $Context)` | `WebOS.UserRoleRevoke()` |
| RoleList | `public static function RoleList(array $Payload, array $Context)` | `WebOS.UserRoleList()` |
| HasRole | `public static function HasRole(array $Payload, array $Context)` | `WebOS.UserHasRole()` |
### <span id="webos-vector"></span>WebOS.Vector
- **Purpose**: Enterprise-grade Vector & Embedding Management Engine Multi-App + Multi-Tenant + AI-ready Vector Store Semantic Search, Similarity, Hybrid Search foundation
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Vector.php](WebOS/PHP.26.00.00/Engine/WebOS.Vector.php)
- **Class**: `WebOS_Vector`
- **Facade Entry Points**:
  - `WebOS.Vector()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.VectorUpsert()` → `public static function Upsert(array $Payload = [], array $Context = [])`
  - `WebOS.VectorDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `WebOS.VectorGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `WebOS.VectorSearch()` → `public static function Search(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Vector()` |
| Upsert | `public static function Upsert(array $Payload = [], array $Context = [])` | `WebOS.VectorUpsert()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `WebOS.VectorDelete()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `WebOS.VectorGet()` |
| Search | `public static function Search(array $Payload = [], array $Context = [])` | `WebOS.VectorSearch()` |
### <span id="webos-version"></span>WebOS.Version
- **Purpose**: Central Version Intelligence Engine for WebOS Multi-App, Multi-Tenant, Multi-Layer version resolution Enterprise-grade rollout, canary, pinning & compatibility checks
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Version.php](WebOS/PHP.26.00.00/Engine/WebOS.Version.php)
- **Class**: `WebOS_Version`
- **Facade Entry Points**:
  - `WebOS.Version()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.VersionResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `WebOS.VersionGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.VersionCheck()` → `public static function Check(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Version()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `WebOS.VersionResolve()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.VersionGet()` |
| Check | `public static function Check(array $Payload, array $Context)` | `WebOS.VersionCheck()` |
### <span id="webos-wallet"></span>WebOS.Wallet
- **Purpose**: Enterprise-grade Wallet Engine for any backend (Website/WebApp/Mobile/SaaS) Multi-App + Multi-Tenant safe by design High-performance read paths + transaction-safe write paths Ledger-ready (double-entry) architecture with idempotency protection ---------------------------------------------------------------------------- DESIGN RULES (Router v7.0 Compatible): WebOS::WalletXxx() -> Engine=Wallet, Action=Xxx All methods static Main($Payload, $Context) mandatory fallback entry ---------------------------------------------------------------------------- DEPENDENCIES (Engines): WebOS::Security()   : validation helpers (optional, but recommended) WebOS::Auth()       : session/user validation (optional at engine level) WebOS::Role()       : role checks (optional at engine level) WebOS::Permission() : permission checks (recommended) WebOS::Database()   : database operations (CRUD abstraction) WebOS::Lock()       : concurrency control (recommended) WebOS::Cache()      : cache (read optimization) WebOS::Log()        : structured logs (optional) WebOS::Audit()      : audit trail (recommended) WebOS::EventBus()   : domain events (optional) ----------------------------------------------------------------------------
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Wallet.php](WebOS/PHP.26.00.00/Engine/WebOS.Wallet.php)
- **Class**: `WebOS_Wallet`
- **Facade Entry Points**:
  - `WebOS.Wallet()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.WalletCreate()` → `public static function Create(array $Payload, array $Context)`
  - `WebOS.WalletEnsure()` → `public static function Ensure(array $Payload, array $Context)`
  - `WebOS.WalletGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.WalletBalance()` → `public static function Balance(array $Payload, array $Context)`
  - `WebOS.WalletCredit()` → `public static function Credit(array $Payload, array $Context)`
  - `WebOS.WalletDebit()` → `public static function Debit(array $Payload, array $Context)`
  - `WebOS.WalletTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `WebOS.WalletFreeze()` → `public static function Freeze(array $Payload, array $Context)`
  - `WebOS.WalletUnfreeze()` → `public static function Unfreeze(array $Payload, array $Context)`
  - `WebOS.WalletStatement()` → `public static function Statement(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Wallet()` |
| Create | `public static function Create(array $Payload, array $Context)` | `WebOS.WalletCreate()` |
| Ensure | `public static function Ensure(array $Payload, array $Context)` | `WebOS.WalletEnsure()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.WalletGet()` |
| Balance | `public static function Balance(array $Payload, array $Context)` | `WebOS.WalletBalance()` |
| Credit | `public static function Credit(array $Payload, array $Context)` | `WebOS.WalletCredit()` |
| Debit | `public static function Debit(array $Payload, array $Context)` | `WebOS.WalletDebit()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `WebOS.WalletTransfer()` |
| Freeze | `public static function Freeze(array $Payload, array $Context)` | `WebOS.WalletFreeze()` |
| Unfreeze | `public static function Unfreeze(array $Payload, array $Context)` | `WebOS.WalletUnfreeze()` |
| Statement | `public static function Statement(array $Payload, array $Context)` | `WebOS.WalletStatement()` |
### <span id="webos-webhook"></span>WebOS.Webhook
- **Purpose**: Enterprise-grade Webhook Operating Layer (Inbound + Outbound) Multi-App + Multi-Tenant compatible by Context + Unit resolver Signature verify (HMAC), replay window support, idempotency protection Fast ACK design + optional async dispatch (Queue/Task if available) Works only via WebOS.Loader / WebOS.EngineRouter (no direct includes)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Webhook.php](WebOS/PHP.26.00.00/Engine/WebOS.Webhook.php)
- **Class**: `WebOS_Webhook`
- **Facade Entry Points**:
  - `WebOS.Webhook()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.WebhookIn()` → `public static function In($Payload = [], $Context = [])`
  - `WebOS.WebhookOut()` → `public static function Out($Payload = [], $Context = [])`
  - `WebOS.WebhookRoute()` → `public static function Route($Payload = [], $Context = [])`
  - `WebOS.WebhookVerify()` → `public static function Verify($Payload = [], $Context = [])`
  - `WebOS.WebhookIdempotency()` → `public static function Idempotency($Payload = [], $Context = [])`
  - `WebOS.WebhookNormalize()` → `public static function Normalize($Payload = [], $Context = [])`
  - `WebOS.WebhookDispatch()` → `public static function Dispatch($Payload = [], $Context = [])`
  - `WebOS.WebhookTenant()` → `public static function Tenant($Payload = [], $Context = [])`
  - `WebOS.WebhookScope()` → `public static function Scope($Payload = [], $Context = [])`
  - `WebOS.WebhookRespond()` → `public static function Respond($Payload = [], $Context = [], $HttpCode = 200)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Webhook()` |
| In | `public static function In($Payload = [], $Context = [])` | `WebOS.WebhookIn()` |
| Out | `public static function Out($Payload = [], $Context = [])` | `WebOS.WebhookOut()` |
| Route | `public static function Route($Payload = [], $Context = [])` | `WebOS.WebhookRoute()` |
| Verify | `public static function Verify($Payload = [], $Context = [])` | `WebOS.WebhookVerify()` |
| Idempotency | `public static function Idempotency($Payload = [], $Context = [])` | `WebOS.WebhookIdempotency()` |
| Normalize | `public static function Normalize($Payload = [], $Context = [])` | `WebOS.WebhookNormalize()` |
| Dispatch | `public static function Dispatch($Payload = [], $Context = [])` | `WebOS.WebhookDispatch()` |
| Tenant | `public static function Tenant($Payload = [], $Context = [])` | `WebOS.WebhookTenant()` |
| Scope | `public static function Scope($Payload = [], $Context = [])` | `WebOS.WebhookScope()` |
| Respond | `public static function Respond($Payload = [], $Context = [], $HttpCode = 200)` | `WebOS.WebhookRespond()` |
### <span id="webos-wellness"></span>WebOS.Wellness
- **Purpose**: Universal Wellness Domain Engine for WebOS (Multi-App + Multi-Tenant) Standardize Wellness Profile, Events, Habits, Goals, Aggregates High-performance, audit-friendly, permission-based backend layer
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Wellness.php](WebOS/PHP.26.00.00/Engine/WebOS.Wellness.php)
- **Class**: `WebOS_Wellness`
- **Facade Entry Points**:
  - `WebOS.Wellness()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.WellnessProfileGet()` → `public static function ProfileGet(array $Payload, array $Context)`
  - `WebOS.WellnessProfileSet()` → `public static function ProfileSet(array $Payload, array $Context)`
  - `WebOS.WellnessEventIngest()` → `public static function EventIngest(array $Payload, array $Context)`
  - `WebOS.WellnessDailyGet()` → `public static function DailyGet(array $Payload, array $Context)`
  - `WebOS.WellnessHabitNew()` → `public static function HabitNew(array $Payload, array $Context)`
  - `WebOS.WellnessGoalNew()` → `public static function GoalNew(array $Payload, array $Context)`
  - `WebOS.WellnessScoreCompute()` → `public static function ScoreCompute(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Wellness()` |
| ProfileGet | `public static function ProfileGet(array $Payload, array $Context)` | `WebOS.WellnessProfileGet()` |
| ProfileSet | `public static function ProfileSet(array $Payload, array $Context)` | `WebOS.WellnessProfileSet()` |
| EventIngest | `public static function EventIngest(array $Payload, array $Context)` | `WebOS.WellnessEventIngest()` |
| DailyGet | `public static function DailyGet(array $Payload, array $Context)` | `WebOS.WellnessDailyGet()` |
| HabitNew | `public static function HabitNew(array $Payload, array $Context)` | `WebOS.WellnessHabitNew()` |
| GoalNew | `public static function GoalNew(array $Payload, array $Context)` | `WebOS.WellnessGoalNew()` |
| ScoreCompute | `public static function ScoreCompute(array $Payload, array $Context)` | `WebOS.WellnessScoreCompute()` |
### <span id="webos-withdraw"></span>WebOS.Withdraw
- **Purpose**: Enterprise Grade Withdraw Engine for Multi-App + Multi-Tenant WebOS.
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Withdraw.php](WebOS/PHP.26.00.00/Engine/WebOS.Withdraw.php)
- **Class**: `WebOS_Withdraw`
- **Facade Entry Points**:
  - `WebOS.Withdraw()` → `public static function Main(array $Payload, array $Context)`
  - `WebOS.WithdrawQuote()` → `public static function Quote(array $Payload, array $Context)`
  - `WebOS.WithdrawNew()` → `public static function New(array $Payload, array $Context)`
  - `WebOS.WithdrawValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `WebOS.WithdrawApprove()` → `public static function Approve(array $Payload, array $Context)`
  - `WebOS.WithdrawProcess()` → `public static function Process(array $Payload, array $Context)`
  - `WebOS.WithdrawCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `WebOS.WithdrawStatus()` → `public static function Status(array $Payload, array $Context)`
  - `WebOS.WithdrawGet()` → `public static function Get(array $Payload, array $Context)`
  - `WebOS.WithdrawList()` → `public static function List(array $Payload, array $Context)`
  - `WebOS.WithdrawFee()` → `public static function Fee(array $Payload, array $Context)`
  - `WebOS.WithdrawLimits()` → `public static function Limits(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `WebOS.Withdraw()` |
| Quote | `public static function Quote(array $Payload, array $Context)` | `WebOS.WithdrawQuote()` |
| New | `public static function New(array $Payload, array $Context)` | `WebOS.WithdrawNew()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `WebOS.WithdrawValidate()` |
| Approve | `public static function Approve(array $Payload, array $Context)` | `WebOS.WithdrawApprove()` |
| Process | `public static function Process(array $Payload, array $Context)` | `WebOS.WithdrawProcess()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `WebOS.WithdrawCancel()` |
| Status | `public static function Status(array $Payload, array $Context)` | `WebOS.WithdrawStatus()` |
| Get | `public static function Get(array $Payload, array $Context)` | `WebOS.WithdrawGet()` |
| List | `public static function List(array $Payload, array $Context)` | `WebOS.WithdrawList()` |
| Fee | `public static function Fee(array $Payload, array $Context)` | `WebOS.WithdrawFee()` |
| Limits | `public static function Limits(array $Payload, array $Context)` | `WebOS.WithdrawLimits()` |
### <span id="webos-worker"></span>WebOS.Worker
- **Purpose**: OS-grade background job execution engine Multi-App + Multi-Tenant + Fair Scheduling Safe async execution with retries, locks, metrics
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Worker.php](WebOS/PHP.26.00.00/Engine/WebOS.Worker.php)
- **Class**: `WebOS_Worker`
- **Facade Entry Points**:
  - `WebOS.Worker()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `WebOS.WorkerRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `WebOS.WorkerOnce()` → `public static function Once(array $Payload = [], array $Context = [])`
  - `WebOS.WorkerExecute()` → `public static function Execute(array $Payload = [], array $Context = [])`
  - `WebOS.WorkerStats()` → `public static function Stats(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `WebOS.Worker()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `WebOS.WorkerRun()` |
| Once | `public static function Once(array $Payload = [], array $Context = [])` | `WebOS.WorkerOnce()` |
| Execute | `public static function Execute(array $Payload = [], array $Context = [])` | `WebOS.WorkerExecute()` |
| Stats | `public static function Stats(array $Payload = [], array $Context = [])` | `WebOS.WorkerStats()` |
### <span id="webos-workflow"></span>WebOS.Workflow
- **Purpose**: Workflow-as-OS-Resource for WebOS backend Multi-App + Multi-Tenant workflow definition + execution State/DAG/Saga-ready (v1 focuses on deterministic sequential execution with controlled branching hooks; graph expansion is forward compatible)
- **File**: [WebOS/PHP.26.00.00/Engine/WebOS.Workflow.php](WebOS/PHP.26.00.00/Engine/WebOS.Workflow.php)
- **Class**: `WebOS_Workflow`
- **Facade Entry Points**:
  - `WebOS.Workflow()` → `public static function Main($Payload = [], $Context = [])`
  - `WebOS.WorkflowNew()` → `public static function New($Payload = [], $Context = [])`
  - `WebOS.WorkflowUpdate()` → `public static function Update($Payload = [], $Context = [])`
  - `WebOS.WorkflowGet()` → `public static function Get($Payload = [], $Context = [])`
  - `WebOS.WorkflowList()` → `public static function List($Payload = [], $Context = [])`
  - `WebOS.WorkflowValidate()` → `public static function Validate($Payload = [], $Context = [])`
  - `WebOS.WorkflowDeploy()` → `public static function Deploy($Payload = [], $Context = [])`
  - `WebOS.WorkflowRun()` → `public static function Run($Payload = [], $Context = [])`
  - `WebOS.WorkflowResume()` → `public static function Resume($Payload = [], $Context = [])`
  - `WebOS.WorkflowCancel()` → `public static function Cancel($Payload = [], $Context = [])`
  - `WebOS.WorkflowStatus()` → `public static function Status($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `WebOS.Workflow()` |
| New | `public static function New($Payload = [], $Context = [])` | `WebOS.WorkflowNew()` |
| Update | `public static function Update($Payload = [], $Context = [])` | `WebOS.WorkflowUpdate()` |
| Get | `public static function Get($Payload = [], $Context = [])` | `WebOS.WorkflowGet()` |
| List | `public static function List($Payload = [], $Context = [])` | `WebOS.WorkflowList()` |
| Validate | `public static function Validate($Payload = [], $Context = [])` | `WebOS.WorkflowValidate()` |
| Deploy | `public static function Deploy($Payload = [], $Context = [])` | `WebOS.WorkflowDeploy()` |
| Run | `public static function Run($Payload = [], $Context = [])` | `WebOS.WorkflowRun()` |
| Resume | `public static function Resume($Payload = [], $Context = [])` | `WebOS.WorkflowResume()` |
| Cancel | `public static function Cancel($Payload = [], $Context = [])` | `WebOS.WorkflowCancel()` |
| Status | `public static function Status($Payload = [], $Context = [])` | `WebOS.WorkflowStatus()` |

## Missing / Unknown
The following engines had incomplete metadata during automated extraction:
- WebOS.Auth: No public static methods found
- WebOS.Env: No public static methods found
- WebOS.Env: Purpose not documented
- WebOS.Row: No public static methods found
