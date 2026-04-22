# PyOS Engines Reference (v26)

Generated automatically on 2025-12-24 16:12:31 UTC from live sources under `PyOS/Py.26.00.00/Engine`. Each section lists the engine purpose, location, and every public static entry exposed through the PyOS facade.

## Routing Rules
- Use `PyOS.<Engine><Action>()` from Loader/Router layers; `Main()` executes when no action is specified.
- `EngineRouter` auto-loads classes from `PyOS/Py.26.00.00/Engine`; keep methods static and side-effect free outside their logic blocks.
- Hooks run before/after corresponding engines when `PyOS.Hook.*` files exist; overrides in `Custom.*` take precedence over core engines.
- Always pass Context arrays (`Tenant/App/Brand/User/Unit`) without mutating them; return mutated copies through payloads.
- Maintain deterministic logging + deny-by-default checks so safety wrappers (lock, rate, policy) stay reliable across retries.

## Quick Index
- [PyOS.ABTest](#PyOS-abtest) — Enterprise-grade A/B Testing & Experiment Decision Engine Multi-Tenant + Multi-App + Multi-Brand compatible Deterministic, sticky, cache-first variant…
- [PyOS.Abuse](#PyOS-abuse) — Behaviour-based abuse detection & prevention engine Adaptive risk scoring (not fixed rate limit) Multi-App + Multi-Tenant safe High-performance (cache-first,…
- [PyOS.Account](#PyOS-account) — Central Account Engine for PyOS Backend Operating System Multi-App + Multi-Tenant Account Management High-Performance, Cache-Aware, Audit-Ready Acts as…
- [PyOS.Agent](#PyOS-agent) — Autonomous Agent Orchestration Engine for PyOS Acts as AI / Automation Workforce Layer Multi-App + Multi-Tenant safe Enterprise Governance + Policy aware
- [PyOS.AI](#PyOS-ai) — Enterprise Grade AI Orchestration Engine for PyOS (Multi-App / Multi-Tenant) Unified AI Runtime: Chat / Run / Embed / RAG / AgentRun Safe-by-default: scope,…
- [PyOS.Alert](#PyOS-alert) — PyOS Nervous System: Alert Event → Dedup → Incident → Dispatch → Delivery Multi-App + Multi-Tenant safe routing and isolation High-Performance, non-blocking…
- [PyOS.Analytics](#PyOS-analytics) — OS-level analytics & telemetry engine for PyOS Multi-App, Multi-Tenant, High-Performance analytics core Unified tracking for API, Cron, Worker, Webhook,…
- [PyOS.API](#PyOS-api) — Central API Gateway Engine for PyOS Enterprise-grade API orchestration Multi-App, Multi-Tenant, High-Performance backend core
- [PyOS.Appointment](#PyOS-appointment) — Universal, multi-tenant, multi-app Appointment & Scheduling Engine Supports clinics, services, meetings, demos, education, enterprise use Conflict-safe booking…
- [PyOS.AssetMgmt](#PyOS-assetmgmt) — Enterprise-grade Asset Management Engine Multi-App + Multi-Tenant compatible Physical, Digital, License, Subscription assets Full lifecycle: Create → Assign →…
- [PyOS.Assignment](#PyOS-assignment) — Enterprise-Grade Assignment Management Engine Multi-App + Multi-Tenant + High-Performance Education, Training, Enterprise Tasks compatible
- [PyOS.Attendance](#PyOS-attendance) — Enterprise-grade Attendance Management Engine Multi-App, Multi-Tenant, High-Performance OS-level backend component for PyOS
- [PyOS.Audit](#PyOS-audit) — Centralized, tamper-evident audit engine for PyOS Append-only audit fabric (API / Cron / Webhook / System) Multi-App + Multi-Tenant isolation High-performance…
- [PyOS.Auth](#PyOS-auth) — Enterprise Auth Brain for PyOS (Multi-App + Multi-Tenant) Identity (User/App/Tenant) Context Session validation + Force logout hooks Token parsing…
- [PyOS.Automation](#PyOS-automation) — System-level Automation Engine for PyOS. Provides enterprise-grade event → rule → pipeline → action orchestration across Multi-App, Multi-Brand, Multi-Tenant…
- [PyOS.Backup](#PyOS-backup) — OS-level Backup & Restore Engine for PyOS Multi-App, Multi-Tenant, Enterprise-grade Manifest-driven, Secure, Verifiable backups
- [PyOS.Balance](#PyOS-balance) — Balance state management (Available / Locked / Pending / Total) Ledger / Transaction driven balance updates Multi-Tenant + Multi-App compatible High-…
- [PyOS.Billing](#PyOS-billing) — Unified Billing Engine for PyOS Multi-App + Multi-Tenant + Multi-Brand billing brain Handles Products, Prices, Invoices, Subscriptions coordination
- [PyOS.Blockchain](#PyOS-blockchain) — Blockchain abstraction engine for PyOS Chain-agnostic (EVM, Tron, Solana, Internal Ledger) Secure orchestration of wallets, balances, transfers & indexing…
- [PyOS.Boot](#PyOS-boot) — Initialize complete Aliens ecosystem for each request Highlights : Normalizes Python environment (timezone, encoding, limits) Uses Aliens['PyOS']['version'] +…
- [PyOS.BotDetect](#PyOS-botdetect) — Enterprise-grade bot detection + abuse scoring for all PyOS entry points. Works in Multi-App + Multi-Tenant environment using Context isolation. Returns…
- [PyOS.Brand](#PyOS-brand) — Brand resolution layer for PyOS Multi-App + Multi-Tenant + Multi-Domain support White-label / Enterprise branding engine
- [PyOS.Cache](#PyOS-cache) — Enterprise-grade universal caching engine for PyOS 26.x DESIGN : Mode-Detection, Auto-UID, TTL, Logging, Force-Fresh.
- [PyOS.Cart](#PyOS-cart) — Universal Cart Engine for PyOS Works for ANY type of backend: E-commerce Food / Booking Subscription Services Marketplace Multi-Tenant + Multi-App safe High-…
- [PyOS.Category](#PyOS-category) — Universal Category / Taxonomy Engine for PyOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph…
- [PyOS.Certificate](#PyOS-certificate) — Centralized Certificate Engine for PyOS Issue, Validate, Revoke, Renew digital certificates Multi-App + Multi-Tenant compatible Secure signing, verification…
- [PyOS.Chat](#PyOS-chat) — Enterprise-grade Chat backbone for any PyOS App (Multi-App + Multi-Tenant) DM / Group / Channel chat spaces Messaging, membership, receipts scaffolding High-…
- [PyOS.Class](#PyOS-class) — Universal Class / Module loader for PyOS Multi-App + Multi-Tenant safe instantiation High-performance registry + reflection cache Dependency-aware lightweight…
- [PyOS.Cluster](#PyOS-cluster) — Provide cluster-level intelligence to PyOS Enable multi-node, multi-region, multi-tenant backend execution Act as the backbone for high-availability,…
- [PyOS.Comment](#PyOS-comment) — Universal Comment Engine for PyOS Multi-App + Multi-Tenant + Enterprise Grade Threaded comments with moderation, reactions, audit
- [PyOS.Compliance](#PyOS-compliance) — Central Compliance & Governance Engine for PyOS Enforces data policies, consent, retention, audit Multi-App + Multi-Tenant + Region aware
- [PyOS.ComplianceHR](#PyOS-compliancehr) — HR Compliance Operating Layer (Policy → Rules → Decision → Audit) Multi-App + Multi-Tenant compatible via Context (app/brand/ecosystem/user) High-Performance:…
- [PyOS.Config](#PyOS-config) — Centralized configuration manager for PyOS Multi-App, Multi-Tenant config storage In-Memory + File + Database hybrid config system Auto-load configs from: App…
- [PyOS.Constitution](#PyOS-constitution) — PyOS ka Supreme Governance Layer (Policy + Rules + Enforcement) Multi-App + Multi-Tenant + Enterprise Grade control plane Unit Execution…
- [PyOS.Constraint](#PyOS-constraint) — Universal Constraint Enforcement Engine for PyOS Enforce hard/soft/observe rules across API, Data, Security, Business layers Multi-App + Multi-Tenant +…
- [PyOS.Container](#PyOS-container) — Central runtime container for PyOS Context holder (Tenant, App, Brand, User, Env) Service registry with lazy-loading Multi-App + Multi-Tenant isolation…
- [PyOS.Content](#PyOS-content) — Universal Content Operating Engine for PyOS Supports ANY website / PyApp / SaaS / app backend Multi-Tenant + Multi-App + Multi-Brand aware Draft → Publish →…
- [PyOS.Context](#PyOS-context) — Centralized Context Manager for entire PyOS lifecycle Replaces scattered globals ($i, $app, $brand, $ecosystem) Provides unified API: ContextGet, ContextSet,…
- [PyOS.Core](#PyOS-core) — PyOS ka "Brain" / Central Kernel Multi-App + Multi-Tenant core registry Request Context (User, App, Brand, Tenant, Request) System Context (Version, Path,…
- [PyOS.Course](#PyOS-course) — Enterprise-grade Course backend engine (Multi-App + Multi-Tenant) Clean, scoped CRUD + enrollment + progress Loader-only usage via PyOS::CourseAction()
- [PyOS.Cron](#PyOS-cron) — Enterprise-grade Cron / Scheduler Engine for PyOS Multi-App + Multi-Tenant + Cluster-Safe job orchestration Central brain for all scheduled/background jobs
- [PyOS.Crypto](#PyOS-crypto) — Enterprise-grade Crypto Engine for PyOS Multi-App, Multi-Tenant, Chain-Agnostic crypto layer Wallet, Balance, Transfer, Ledger (Phase-1)
- [PyOS.CSRF](#PyOS-csrf) — Enterprise-grade CSRF protection for browser + session(cookie) requests Multi-App + Multi-Tenant isolation by scope (Tenant/App/Brand/User/Session) Supports…
- [PyOS.Currency](#PyOS-currency) — Enterprise-grade Currency + Money + FX conversion engine Multi-App + Multi-Tenant defaults, policies, and allowed currencies High-precision decimal safety (no…
- [PyOS.Database](#PyOS-database) — Central Database Operating Layer for PyOS Multi-App + Multi-Tenant + High-Performance DB Engine Read/Write split, Transactions, Safe Queries Powers AliensCRUD…
- [PyOS.DataPolicy](#PyOS-datapolicy) — OS-level Data Governance & Lifecycle Engine Retention, Archival, Purge, Legal Hold, Masking, Export Control Enforced across ALL units (API, Cron, Webhook, SDK)…
- [PyOS.Debugger](#PyOS-debugger) — Enterprise-grade Debugging Engine for PyOS Safe production debugging (no data leaks) Multi-App + Multi-Tenant isolation Integrated with Log, Trace, Metric,…
- [PyOS.DebuggerAI](#PyOS-debuggerai) — System-level autonomous debugging & incident intelligence engine Central error, anomaly, trace and incident brain for PyOS Safe, scoped, auditable debugging…
- [PyOS.Decision](#PyOS-decision) — Central deterministic decision engine for PyOS Access / Policy / Routing / Scoring decisions Multi-App, Multi-Brand, Multi-Tenant compatible Explainable,…
- [PyOS.Department](#PyOS-department) — Enterprise-grade Department engine (Multi-Tenant + Multi-App) Department hierarchy (Parent/Path/Depth), move, tree Members mapping, role-ready, policy-ready…
- [PyOS.Deployment](#PyOS-deployment) — Enterprise-grade deployment orchestration engine Multi-App + Multi-Brand + Multi-Tenant deployments Zero-downtime, rollback-first philosophy OS-style…
- [PyOS.Deposit](#PyOS-deposit) — Enterprise-grade Deposit engine (Multi-App + Multi-Tenant) Deposit Intent → Provider Init → Webhook/Confirm → Ledger Posting Idempotency + Locking + Audit +…
- [PyOS.Device](#PyOS-device) — Enterprise-grade Device Identity + Trust + Risk Layer for PyOS Multi-App + Multi-Tenant device registry Session binding support (anti-hijack baseline)…
- [PyOS.DigitalTwin](#PyOS-digitaltwin) — Runtime Digital Twin for PyOS (System / App / Tenant) Snapshot, Diff, Replay, Simulation, Forecast Safe experimentation + predictive intelligence
- [PyOS.Doc](#PyOS-doc) — Make PyOS a self-documenting backend Operating System Auto-generate documentation for Engines, Units, Modules Multi-App + Multi-Brand + Multi-Tenant aware AI-…
- [PyOS.Doctor](#PyOS-doctor) — Enterprise Grade Doctor Domain Engine (Multi-App + Multi-Tenant) Doctor Identity, Verification, Clinic Linking, Schedule, Availability High-Performance Read…
- [PyOS.Download](#PyOS-download) — Secure, high-performance, enterprise-grade download engine Multi-App + Multi-Tenant safe delivery Supports private/public/signed downloads
- [PyOS.EducationAI](#PyOS-educationai) — Enterprise Grade AI Engine for Education Systems Powering LMS, Tutors, Exams, Content Factory Multi-App + Multi-Tenant + High Performance
- [PyOS.Email](#PyOS-email) — Enterprise-grade Email Engine for PyOS Multi-App, Multi-Brand, Multi-Tenant High-performance async email orchestration Template + Provider abstraction Queue-…
- [PyOS.Embedding](#PyOS-embedding) — Enterprise-grade Embedding orchestration engine Multi-App, Multi-Tenant, High-Performance Provider-agnostic (Azure/OpenAI/Local/Future) Works as Semantic…
- [PyOS.Emergency](#PyOS-emergency) — Enterprise-grade Incident / Emergency OS Layer for PyOS Multi-App + Multi-Tenant safe incident lifecycle: New → Get → Update → Escalate → Action → Close →…
- [PyOS.Employee](#PyOS-employee) — Enterprise-grade Employee Engine Multi-Tenant + Multi-App Workforce Core Central employee abstraction for HR, Payroll, Attendance, Projects
- [PyOS.Emulator](#PyOS-emulator) — Provide a virtualized execution layer for PyOS Units Emulate API / Cron / Webhook / Component execution safely Enable Sandbox, Replay, Shadow, Chaos and Perf…
- [PyOS.Encryption](#PyOS-encryption) — Central cryptography engine for PyOS Encryption / Decryption (AEAD) Hashing & Password Hashing HMAC Sign / Verify Random / Nonce generation
- [PyOS.Env](#PyOS-env) — Purpose not documented in source.
- [PyOS.Environment](#PyOS-environment) — Enterprise-grade Environment Control Plane for PyOS (Multi-App + Multi-Tenant) Single source of truth: Env Profile (perf + security + ops + log + flags)…
- [PyOS.EventBus](#PyOS-eventbus) — Central nervous system for PyOS Durable, multi-tenant, multi-app event orchestration High-performance publish / subscribe / consume
- [PyOS.Exam](#PyOS-exam) — Central Exam Engine for PyOS Works for LMS, HR, Certification, Medical, Compliance exams OS-level exam lifecycle management Highlights : Loader + EngineRouter…
- [PyOS.Executor](#PyOS-executor) — Central execution engine for all Units (API/Cron/Webhook/Asset/Component/Template) Boot files remain orchestration-only (no functions/
- [PyOS.Experiment](#PyOS-experiment) — Enterprise-grade Experimentation Engine for PyOS A/B Tests, Canary Releases, Progressive Rollouts Multi-App + Multi-Tenant safe by design Cache-first, ultra-…
- [PyOS.Failover](#PyOS-failover) — Central failover decision & state engine for PyOS Policy-driven, multi-scope failover orchestration Supports Global / App / Tenant / Dependency scopes
- [PyOS.Feature](#PyOS-feature) — Enterprise-grade Feature Flag & Toggle Engine Multi-App, Multi-Tenant, Role/User scoped Deterministic rollout, variant & kill-switch support
- [PyOS.FeatureFlag](#PyOS-featureflag) — OS-level Feature Flag system for PyOS Runtime feature enable/disable without deployment Multi-App + Multi-Tenant + Role/User aware Deterministic rollout (hash…
- [PyOS.Feed](#PyOS-feed) — Universal Feed / Activity / Timeline Engine Works for Social, Enterprise, LMS, E-Commerce, System Feeds Multi-App + Multi-Tenant + High-Performance Push / Pull…
- [PyOS.Filter](#PyOS-filter) — Universal, secure, high-performance filter engine One DSL → DB / Analytics / API ready Policy-driven, tenant-safe, cache-aware filtering
- [PyOS.Firewall](#PyOS-firewall) — Central request firewall for PyOS Entry-layer protection for API / Webhook / Asset / Cron / Component Multi-App + Multi-Tenant aware High-performance, cache-…
- [PyOS.Formula](#PyOS-formula) — Enterprise-grade Formula Engine for PyOS Backend OS Safe formula evaluation without eval() Multi-App + Multi-Tenant + Multi-Brand scoping via Context Compile…
- [PyOS.Friend](#PyOS-friend) — Enterprise-grade Friend / Relationship Engine Multi-Tenant + Multi-App compatible Symmetric friendship graph (A↔B) Privacy, abuse-safe, audit-ready
- [PyOS.Generator](#PyOS-generator) — Enterprise-grade code, unit, app, module generator Backend factory for PyOS (OS-level capability) Deterministic, auditable, multi-layer aware generation
- [PyOS.Governor](#PyOS-governor) — Global control & protection layer for PyOS. Acts as: Resource Governor Policy Enforcer Quota & Limit Controller Circuit Breaker Gate Multi-Tenant Safety Layer
- [PyOS.Health](#PyOS-health) — Enterprise Health System for PyOS (Multi-App + Multi-Tenant) Liveness / Readiness probes for Load Balancers & Orchestrators Quick/Standard/Deep health reports…
- [PyOS.HealthMonitor](#PyOS-healthmonitor) — System, App, Tenant & Unit health monitoring Liveness, Readiness & Deep Health checks Metric ingestion with ultra-low overhead SLO breach detection & degrade…
- [PyOS.HTTP](#PyOS-http) — Enterprise-grade HTTP Client Engine for PyOS Secure, observable, multi-tenant outbound HTTP layer SSRF-safe by default Retry, timeout, cache, trace, log…
- [PyOS.Identity](#PyOS-identity) — Enterprise-grade Identity Fabric for PyOS (Multi-App + Multi-Tenant) Login / Register / Session / Token / OTP orchestration via PyOS Engines Centralized…
- [PyOS.Index](#PyOS-index) — Central Index Intelligence Layer for PyOS Fast Unit Resolution (Exact + Wildcard) Cache Index & Scope Safety Index Registry, Health, and Audit
- [PyOS.Indexer](#PyOS-indexer) — Universal indexing engine for all data types (Post, Page, Product, User…) Multi-App + Multi-Tenant safe indexing Incremental, idempotent, audit-ready indexing…
- [PyOS.Insurance](#PyOS-insurance) — Universal Insurance Engine for Multi-App + Multi-Tenant PyOS Covers: Policies, Plans, Claims, Payouts, Audit, Documents, Integrations Designed as "Coverage…
- [PyOS.Inventory](#PyOS-inventory) — Enterprise-grade Inventory Operating Engine (Multi-App, Multi-Tenant) Ledger-first design (truth = immutable ledger; balance = projection) Oversell protection…
- [PyOS.Invoice](#PyOS-invoice) — Enterprise-grade Invoicing Operating Layer for PyOS Multi-App + Multi-Tenant + High-Performance + Audit-Proof Strict Loader/Router compatibility…
- [PyOS.Knowledge](#PyOS-knowledge) — Central Knowledge Engine for PyOS Multi-App, Multi-Tenant, Enterprise Knowledge Layer Facts, Documents, Entities, Relations, Graph & AI-ready
- [PyOS.KnowledgeGraph](#PyOS-knowledgegraph) — Enterprise-grade Knowledge Graph Engine for PyOS Multi-App + Multi-Tenant semantic data layer Acts as intelligence backbone for Search, AI, Recommendation,…
- [PyOS.LabReport](#PyOS-labreport) — Enterprise-grade Lab Report lifecycle engine Multi-App + Multi-Tenant + High-Security medical data handling Works as core backend module for Hospital / Lab /…
- [PyOS.Learning](#PyOS-learning) — PyOS ka OS-level Learning Engine (Observe → Learn → Suggest/Act → Verify) Multi-App + Multi-Tenant compatible (Context based scoping) High-performance, async-…
- [PyOS.Leave](#PyOS-leave) — Enterprise-grade Leave Management Engine Multi-App, Multi-Tenant, Policy-driven Fully compatible with PyOS.Loader & EngineRouter
- [PyOS.Ledger](#PyOS-ledger) — Immutable, enterprise-grade ledger engine Multi-App + Multi-Tenant financial journal Append-only, idempotent, audit-ready
- [PyOS.Lesson](#PyOS-lesson) — Universal Lesson Engine for PyOS OS-level Lesson primitive (not LMS-only) Multi-App + Multi-Tenant + Multi-Brand compatible
- [PyOS.Like](#PyOS-like) — Universal Like / Unlike / Reaction engine Works with ANY entity (Post, Comment, Product, Video, Profile, etc.) Multi-App + Multi-Tenant + Enterprise Grade
- [PyOS.Linter](#PyOS-linter) — Enterprise-grade static code analysis engine for PyOS Enforces AliensStyle + AliensGrade contracts Validates Engine, Boot, Unit, SDK, App, Brand code Acts as…
- [PyOS.LoadBalancer](#PyOS-loadbalancer) — Logical load balancing for PyOS backend OS Multi-App, Multi-Tenant, Multi-Cluster routing High-performance decision engine (app-level LB) Zero-downtime…
- [PyOS.Locale](#PyOS-locale) — Enterprise-grade Locale Operating System layer for PyOS Multi-App + Multi-Brand + Multi-Tenant locale resolution + formatting Safe, deterministic, cached…
- [PyOS.Location](#PyOS-location) — Enterprise-grade Location & Geo-Intelligence Engine Unified location resolution for API, Web, Mobile, SaaS Multi-App + Multi-Tenant aware Security, Privacy &…
- [PyOS.Lock](#PyOS-lock) — Distributed Lock Engine for PyOS Kernel Prevent race conditions, double execution, data corruption Support API, Cron, Queue, Worker, Task, EventBus
- [PyOS.Log](#PyOS-log) — Enterprise-grade structured logging backbone for PyOS Multi-App + Multi-Tenant safe (App/Brand/Ecosystem/User/RequestId) High-performance buffered writes with…
- [PyOS.Login](#PyOS-login) — Enterprise-grade Login Engine for PyOS Multi-App, Multi-Tenant, Secure Authentication Gateway Acts as Identity Entry Point (Password-based v1)
- [PyOS.Logout](#PyOS-logout) — Enterprise-grade Logout & Access Revocation Engine Multi-App, Multi-Tenant, Multi-Device compatible Token, Session, Cache & Permission invalidation Policy,…
- [PyOS.Manifest](#PyOS-manifest) — Single Source Of Truth (SSOT) for all configuration Enterprise-grade Manifest engine for: Multi-App Multi-Brand Multi-Tenant Multi-Version PyOS Load + Merge +…
- [PyOS.Media](#PyOS-media) — Unified Media Engine for PyOS Backend OS Upload, Store, Secure, Deliver, Audit media assets Multi-App + Multi-Tenant safe by design
- [PyOS.MedicalRecord](#PyOS-medicalrecord) — Universal Medical Record Engine for PyOS Multi-App + Multi-Tenant + Enterprise Grade Versioned, Audited, Secure Medical Records
- [PyOS.Memory](#PyOS-memory) — Unified system-level memory engine for PyOS Tier-0 (Request Memory) + Tier-2 (Shared Cache) Strict Tenant + App + Scope isolation Safe, fast, auditable memory…
- [PyOS.Message](#PyOS-message) — Universal Messaging Engine for PyOS Supports chat, system messages, notifications, workflows Multi-Tenant + Multi-App safe High-performance, async-ready,…
- [PyOS.Metric](#PyOS-metric) — Enterprise-grade Metrics Engine for PyOS Ultra low-overhead counters, gauges, histograms Multi-App + Multi-Tenant safe instrumentation High-cardinality guard…
- [PyOS.Migration](#PyOS-migration) — Enterprise-grade Migration Engine for PyOS Multi-App + Multi-Tenant schema migration system Deterministic, Idempotent, Audited migrations Loader +…
- [PyOS.Mock](#PyOS-mock) — Enterprise-grade Mock Engine for PyOS API / HTTP / DB / Email / SMS / Webhook mocking Multi-App + Multi-Tenant safe mock resolution Deterministic + Chaos…
- [PyOS.Module](#PyOS-module) — [] PyOS ke liye OS-level Module Registry + ModuleMap builder Multi-App + Multi-Tenant module enable/disable, bind/unbind Ultra-fast Resolve + Units/Map cache…
- [PyOS.Monitor](#PyOS-monitor) — Enterprise-grade observability engine for PyOS Monitor API / Cron / Webhook / Asset / Component lifecycle Measure execution time, memory usage, result state…
- [PyOS.Node](#PyOS-node) — Distributed Node Management Engine for PyOS Represents a physical / virtual runtime instance (Node) Supports Multi-App + Multi-Tenant execution Provides Node…
- [PyOS.OAuth](#PyOS-oauth) — OAuth2 Authorization Server + Client Engine for PyOS Central identity authorization fabric Supports Code Flow + PKCE + Refresh + Introspection Fully Loader +…
- [PyOS.Optimize](#PyOS-optimize) — Self-optimization engine for PyOS Performance, cache, DB, concurrency, degradation planning Multi-App + Multi-Tenant aware optimization
- [PyOS.Orchestrator](#PyOS-orchestrator) — Engine-of-Engines: universal execution orchestration for PyOS Sync + Async dispatch (Run / Enqueue / Dispatch) Pipeline / Workflow runner (step-based…
- [PyOS.Order](#PyOS-order) — Enterprise Grade Order Operating Engine (Multi-App + Multi-Tenant) Full Order Lifecycle: New, Get, List, Update, Confirm, Cancel, Pay, Fulfill, Ship, Deliver,…
- [PyOS.Organization](#PyOS-organization) — Enterprise-grade Organization (Tenant) Engine for PyOS Multi-App + Multi-Tenant organization lifecycle, membership, roles, units, settings, policies, audit…
- [PyOS.Page](#PyOS-page) — Page Operating Engine for PyOS Multi-App + Multi-Brand + Multi-Tenant Page Resolution Security-aware, Cache-aware, AI-ready Page Backend
- [PyOS.Partition](#PyOS-partition) — High-performance data partitioning OS layer Multi-App + Multi-Tenant aware Time / Tenant / Policy driven partition resolution Zero-downtime rotation & routing…
- [PyOS.Patient](#PyOS-patient) — Enterprise Grade Patient Engine (Multi-App + Multi-Tenant) OS-level entity management: Patient identity, lifecycle, privacy-ready High-performance patterns:…
- [PyOS.Payment](#PyOS-payment) — Unified payment orchestration engine for PyOS Multi-App, Multi-Tenant, Multi-Provider payment backbone Gateway-agnostic normalized payment lifecycle
- [PyOS.Payout](#PyOS-payout) — Unified outgoing funds engine (Payout / Withdraw / Settlement) Multi-App, Multi-Tenant, Multi-Gateway Async-first, Idempotent, Audit-proof Highlights : Loader…
- [PyOS.Payroll](#PyOS-payroll) — Enterprise-grade Payroll Operating Engine Multi-App, Multi-Tenant, Policy-Driven Payroll Deterministic, Auditable, High-Performance
- [PyOS.Performance](#PyOS-performance) — PyOS High-Performance Operating System Engine Measure, track, score and optimize runtime performance Request → Unit → Span → Metrics lifecycle
- [PyOS.Permission](#PyOS-permission) — Enterprise Grade Permission Engine for PyOS (Multi-App + Multi-Tenant) Central permission gate for API/Cron/Webhook/Assets and business actions RBAC-first…
- [PyOS.Pipeline](#PyOS-pipeline) — OS-level Pipeline / Workflow Engine Deterministic, idempotent, resumable execution Multi-Tenant + Multi-App safe Sync + Async (Queue/Worker) execution…
- [PyOS.Planner](#PyOS-planner) — Convert Intent → Deterministic Plan (execution-ready blueprint) Act as OS-level orchestration brain for PyOS Support Multi-App, Multi-Brand, Multi-Tenant…
- [PyOS.Policy](#PyOS-policy) — Central Policy Engine for PyOS (System Constitution) Access, Security, Rate, Cache, Feature, Compliance policies Multi-Tenant + Multi-App + Multi-Brand aware…
- [PyOS.PolicyAI](#PyOS-policyai) — Central Policy + Decision Engine for PyOS Deterministic + AI-assisted policy evaluation Multi-App, Multi-Brand, Multi-Tenant safe decisions
- [PyOS.Post](#PyOS-post) — Universal Content / Post Engine for PyOS Supports Blog, News, Docs, Articles, CMS, Headless APIs Enterprise Grade + Multi-App + Multi-Tenant safe
- [PyOS.Predict](#PyOS-predict) — Enterprise-grade prediction engine for PyOS Works across all Apps, Tenants, Brands Deterministic, auditable, cache-safe predictions Designed for AI / ML /…
- [PyOS.Prescription](#PyOS-prescription) — Enterprise-grade Prescription Management Engine Multi-App, Multi-Tenant, Multi-Role compatible Secure, auditable, high-performance medical workflow core
- [PyOS.Product](#PyOS-product) — Universal Product / Catalog Engine for PyOS Multi-App + Multi-Tenant + Enterprise-Grade Supports Products, Variants, Pricing, Inventory, Media, SEO Backend…
- [PyOS.Profile](#PyOS-profile) — Universal Profile Engine for PyOS Multi-App + Multi-Tenant + Multi-Brand user identity layer Public / Private / Admin profile isolation Schema-driven, cache-…
- [PyOS.Profiler](#PyOS-profiler) — High-performance profiling engine for PyOS Measure execution time, memory, spans, violations Feed data to Monitor, Metric, Trace, Log engines Multi-Tenant +…
- [PyOS.Push](#PyOS-push) — Unified Push Notification Engine for PyOS Supports Multi-App, Multi-Tenant architecture OS-grade abstraction over push delivery Queue-ready, Provider-…
- [PyOS.Quarantine](#PyOS-quarantine) — Central isolation engine for risky entities Multi-App + Multi-Tenant safe quarantine system Works as ACTION layer after detection engines
- [PyOS.Query](#PyOS-query) — Secure, Multi-Tenant, Multi-App Query Operating System SQL abstraction with strict safety & performance rules Zero raw SQL by default Prepared statements only…
- [PyOS.QueryAI](#PyOS-queryai) — Intelligent Query Engine for PyOS Natural Language → Secure Query Plan → Retrieval → Answer Multi-App + Multi-Tenant + Permission-Aware by default
- [PyOS.Queue](#PyOS-queue) — Enterprise-grade Queue Engine for PyOS (Multi-App + Multi-Tenant) High-performance job enqueue, reserve/lease, ack, fail, retry, DLQ OS-level async backbone…
- [PyOS.RateLimit](#PyOS-ratelimit) — Enterprise-grade Rate Limiting for PyOS Units (API/Webhook/Cron/etc.) Multi-App + Multi-Tenant isolation via Context-aware keys High-performance counters with…
- [PyOS.Recommend](#PyOS-recommend) — Universal Recommendation Engine for PyOS (Multi-App + Multi-Tenant) Provide "Suggest / Track / Profile / Explain" as a backend OS capability Safe,…
- [PyOS.Recovery](#PyOS-recovery) — Enterprise-grade Account Recovery Engine Password reset, access regain, emergency lockdown Multi-App + Multi-Tenant + High-Security recovery workflows
- [PyOS.Recruitment](#PyOS-recruitment) — Enterprise-grade Recruitment / ATS backend engine Multi-Tenant + Multi-App + Multi-Org safe Works as a core OS-level backend service Fully compatible with…
- [PyOS.Refund](#PyOS-refund) — Enterprise Grade Refund Management Engine Multi-App + Multi-Tenant safe by Context High-performance state machine + audit timeline Idempotent request support…
- [PyOS.Region](#PyOS-region) — Region / Geo Intelligence as OS-level capability (Multi-App + Multi-Tenant) Canonical region resolution (Country / Subdivision / City / ZIP) Policy overlay…
- [PyOS.Release](#PyOS-release) — Enterprise-grade Release Lifecycle Engine for PyOS Multi-App + Multi-Tenant + Multi-Strategy Releases Deterministic, Auditable, Rollbackable OS-level Releases
- [PyOS.Relevance](#PyOS-relevance) — Enterprise-grade relevance scoring engine Deterministic + explainable relevance for any entity Multi-App, Multi-Tenant, Multi-Mode (search/feed/recommend)…
- [PyOS.Replica](#PyOS-replica) — Read / Write DB replica routing for PyOS Multi-App + Multi-Tenant aware Lag-aware, policy-driven replica selection Fail-safe primary fallback
- [PyOS.Report](#PyOS-report) — Universal Reporting Engine for PyOS Multi-App, Multi-Tenant, Enterprise-Grade Reporting OS Layer Live + Async + Materialized report execution orchestration
- [PyOS.Restore](#PyOS-restore) — Enterprise-grade Restore Orchestration Engine Multi-App, Multi-Tenant, Safe & Auditable Restore Snapshot / PITR / Partial Restore Support Zero-downtime capable…
- [PyOS.Result](#PyOS-result) — Enterprise-grade Result Management Engine Multi-App + Multi-Tenant safe High-performance, audit-ready, reproducible result system
- [PyOS.Retry](#PyOS-retry) — Deterministic, policy-driven retry system Supports Inline, Deferred, Scheduled retry modes Enforces retry budget, idempotency, and safety Multi-App + Multi-…
- [PyOS.Role](#PyOS-role) — Enterprise-grade Authorization Engine (Role + Optional Permission Gate) Multi-App + Multi-Tenant safe role resolution and checks Strict, deterministic,…
- [PyOS.RoleHR](#PyOS-rolehr) — Enterprise-grade HR Role Management Engine Multi-Tenant + Multi-App + Multi-Scope role orchestration Authoritative HR role source for Permission & Auth engines
- [PyOS.Root](#PyOS-root) — Global Root Orchestrator for PyOS Single entry governance layer for all Units (API, Cron, Webhook, etc.) Builds RootContext (Tenant, App, User, Policy, Unit)…
- [PyOS.Row](#PyOS-row) — [] PyOS ka core CRUD engine. Enterprise-grade, multi-app, multi-tenant projects ke liye high-performance Row / Rows / Insert / Update / Status / Delete /…
- [PyOS.RuleEngine](#PyOS-ruleengine) — Central Decision & Rule Evaluation Engine for PyOS Guard, Compute, Route, Policy & Workflow Rules Multi-App, Multi-Tenant, Multi-Brand aware High-performance,…
- [PyOS.Sandbox](#PyOS-sandbox) — Secure execution envelope for Units / APIs / Hooks / Plugins Enforce policy-based isolation (Multi-App + Multi-Tenant) Runtime guards: time, memory, output,…
- [PyOS.Scaling](#PyOS-scaling) — OS-level adaptive scaling brain for PyOS Multi-App, Multi-Tenant, Enterprise-grade performance control Policy-driven, metric-aware, safe auto-scaling
- [PyOS.Schema](#PyOS-schema) — Treat Database Schema as OS-Level Asset Schema Registry (Source of Truth) Live DB Introspection Drift Detection (Registry vs Reality) Safe Migration Planning…
- [PyOS.SDK](#PyOS-sdk) — Enterprise SDK Orchestrator for PyOS (Multi-App + Multi-Tenant) Deterministic SDK Module/Provider resolution with layered overrides Registry-driven SDK…
- [PyOS.Search](#PyOS-search) — Enterprise-grade Search Engine for Multi-App + Multi-Tenant PyOS Query, Suggest, Index (hooks-ready), Health, Explain Permission-aware + Cache-safe + High-…
- [PyOS.Secrets](#PyOS-secrets) — Centralized, secure, enterprise-grade Secrets Management Engine Multi-Tenant + Multi-App + Multi-Brand aware Supports versioning, rotation, policies, audit…
- [PyOS.SecRuleurity](#PyOS-secruleurity) — PyOS OS-Level Rule Engine (Enterprise Grade) Multi-App + Multi-Tenant Policy / Decision Layer Data-driven rules (no hard-coded if-else across apps) Fast…
- [PyOS.Security](#PyOS-security) — Universal Category / Taxonomy Engine for PyOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph…
- [PyOS.SecurityScan](#PyOS-securityscan) — Enterprise-grade static & policy security scanner for PyOS Multi-App + Multi-Tenant aware Release gate + audit-ready security reports PyOS-specific rule…
- [PyOS.SelfTest](#PyOS-selftest) — Self-healing engine for PyOS Detect, plan, apply and rollback system repairs Multi-App + Multi-Tenant safe Policy driven, audit ready, zero-downtime friendly
- [PyOS.Semantic](#PyOS-semantic) — Provide semantic intelligence layer for PyOS Meaning-based search, indexing, embeddings & ranking Multi-App, Multi-Tenant, Enterprise-grade isolation
- [PyOS.Server](#PyOS-server) — Server-level Operating System engine for PyOS Provides node identity, health, capacity, modes & cluster signals Works across Single Server → Multi-Server →…
- [PyOS.Session](#PyOS-session) — Enterprise-grade Session Operating Layer for PyOS (Multi-App, Multi-Tenant) Supports Browser Cookie Sessions + API Bearer Sessions + Refresh Tokens High-…
- [PyOS.Shard](#PyOS-shard) — Enterprise-grade Sharding Engine for PyOS (Multi-App + Multi-Tenant) Deterministic shard routing (tenant/user/table/module) Directory-based shard map with…
- [PyOS.Share](#PyOS-share) — Enterprise Grade Share System (Multi-App + Multi-Tenant) Create secure share links (tokenized, scoped, revoke-able) Validate/Open share…
- [PyOS.Shipping](#PyOS-shipping) — Enterprise-grade Shipping & Logistics Engine Multi-App + Multi-Tenant + OS-Level abstraction Carrier-agnostic, Rule-driven, Auditable shipping system
- [PyOS.Signal](#PyOS-signal) — PyOS Control-Plane Signaling Engine (OS-Level Signals) Multi-App + Multi-Tenant safe signaling (Scope enforced) High-performance signals using adapters (Cache…
- [PyOS.Signature](#PyOS-signature) — Enterprise-grade request signing & verification engine Protect APIs, Webhooks, internal service calls Prevent tampering, replay attacks, cross-tenant abuse
- [PyOS.Simulation](#PyOS-simulation) — Enterprise-grade Simulation Engine for PyOS (Multi-App + Multi-Tenant) Run realistic sandbox simulations of API/Cron/Webhook/Engine scenarios Deterministic…
- [PyOS.SmartContract](#PyOS-smartcontract) — Smart Contract Operating System Layer (Registry + Deploy + Call + Send) Multi-App + Multi-Tenant compatible (Context enforced) High-Performance, Cache-first,…
- [PyOS.SMS](#PyOS-sms) — Enterprise-grade SMS orchestration layer for PyOS Backend OS Multi-App + Multi-Tenant provider-agnostic SMS sending OTP suite (Send OTP + Verify) with anti-…
- [PyOS.SocialGraph](#PyOS-socialgraph) — Enterprise-grade Social Graph kernel for Multi-App + Multi-Tenant PyOS Generic Node↔Node relationships (FOLLOW, FRIEND, BLOCK, MUTE, MEMBER...) High-…
- [PyOS.Socket](#PyOS-socket) — PyOS real-time backend backbone (WS/SSE/Long-Poll orchestration) Gateway + Backplane driven architecture (Python = policy + routing + publish) Multi-App + Multi-…
- [PyOS.Sorting](#PyOS-sorting) — Centralized, secure, deterministic sorting engine SQL-level + Memory-level sorting Multi-App, Multi-Tenant, Role-aware Stable sorting with forced tie-breakers…
- [PyOS.State](#PyOS-state) — OS-level State Management for PyOS (Multi-App + Multi-Tenant) Strong State Read/Write + Atomic Transitions + Audit/History High-performance cache-first reads…
- [PyOS.Stream](#PyOS-stream) — High-Performance Streaming Engine for PyOS Supports SSE, chunked streaming, publish/subscribe Multi-App + Multi-Tenant safe Enterprise-grade audit, policy &…
- [PyOS.Subscription](#PyOS-subscription) — Enterprise Grade Subscription Engine (Multi-App + Multi-Tenant) Plan Catalog + Subscription Lifecycle + Entitlements + Usage Metering (OS Layer) Deterministic…
- [PyOS.Tag](#PyOS-tag) — Universal Tag Engine for PyOS Multi-App + Multi-Tenant + High-Performance Entity-agnostic tagging (Post, Product, User, Order, etc.)
- [PyOS.Task](#PyOS-task) — Universal Task Orchestration Engine for PyOS Convert any backend work into reliable Tasks Multi-App + Multi-Tenant safe execution Enterprise-grade retry,…
- [PyOS.Tax](#PyOS-tax) — Central Tax Decision & Calculation Engine for PyOS Works for any Website / PyApp / Mobile App / SaaS / Marketplace Multi-Tenant + Multi-App + Multi-…
- [PyOS.Template](#PyOS-template) — Enterprise-grade Template Rendering Engine for PyOS Multi-App + Multi-Brand + Multi-Tenant aware Secure, Cacheable, Overrideable View System
- [PyOS.Tenant](#PyOS-tenant) — Enterprise-grade Multi-Tenant Runtime for PyOS (Multi-App + Multi-Brand) Deterministic Tenant resolution (Domain / Header / Path / Session) Tenant isolation…
- [PyOS.Test](#PyOS-test) — Enterprise-grade Test Operating System for PyOS Multi-App + Multi-Brand + Multi-Tenant test runner (matrix mode) Engine/Router compliance tests (Main()…
- [PyOS.Theme](#PyOS-theme) — OS-level Theme Engine for PyOS Multi-App, Multi-Brand, Multi-Tenant theme resolution Design Tokens, Assets, Templates, Variants Ultra-fast cached resolution…
- [PyOS.Timeout](#PyOS-timeout) — OS-grade Timeout / Deadline enforcement for: API, Cron, Webhook, Worker, Queue, Task, Pipeline (Unit + Step) Multi-App + Multi-Tenant safe budgets Nested…
- [PyOS.Timezone](#PyOS-timezone) — Enterprise Grade Timezone Engine for Multi-App + Multi-Tenant PyOS Deterministic timezone resolution (Payload → User → Tenant → Brand → App → Ecosystem → UTC)…
- [PyOS.Toggle](#PyOS-toggle) — Enterprise-grade Feature Toggle / Flag / Kill-Switch Engine Multi-App, Multi-Brand, Multi-Tenant, User-aware resolution Ultra-fast read path using snapshot +…
- [PyOS.Trace](#PyOS-trace) — Distributed tracing for PyOS (API, Cron, Queue, Worker, Pipeline) Ultra-low overhead, async-safe, multi-tenant aware Correlates EngineRouter → Engine → DB →…
- [PyOS.Transaction](#PyOS-transaction) — Universal Transaction Engine for PyOS Supports any backend: Wallet, E-Commerce, SaaS, MLM, Banking-style systems Multi-Tenant, Multi-App, Enterprise-grade…
- [PyOS.TwoFactor](#PyOS-twofactor) — Provide enterprise‑grade Two‑Factor Authentication (2FA) Support TOTP, Email OTP, SMS OTP, Backup Codes (extensible) Policy‑driven, risk‑aware, step‑up…
- [PyOS.Update](#PyOS-update) — Enterprise-grade Update Orchestration Engine Core / SDK / App / Brand / Tenant updates Zero-downtime (Blue-Green / Canary / Rolling) Safe rollback + audit…
- [PyOS.UpgradePlanner](#PyOS-upgradeplanner) — Deterministic upgrade planning for PyOS ecosystem Multi-App, Multi-Brand, Multi-Tenant safe rollout planning Zero-downtime upgrade strategy generation Risk…
- [PyOS.Upload](#PyOS-upload) — Enterprise-grade Upload Engine for PyOS Multi-App, Multi-Tenant, High-Performance Upload OS Layer Supports chunked, resumable, secure uploads
- [PyOS.User](#PyOS-user) — Enterprise Grade User Engine for PyOS (Multi-App + Multi-Tenant) High-performance read/write with cache-first + strict scope enforcement Centralized user…
- [PyOS.Vector](#PyOS-vector) — Enterprise-grade Vector & Embedding Management Engine Multi-App + Multi-Tenant + AI-ready Vector Store Semantic Search, Similarity, Hybrid Search foundation
- [PyOS.Version](#PyOS-version) — Central Version Intelligence Engine for PyOS Multi-App, Multi-Tenant, Multi-Layer version resolution Enterprise-grade rollout, canary, pinning & compatibility…
- [PyOS.Wallet](#PyOS-wallet) — Enterprise-grade Wallet Engine for any backend (Website/PyApp/Mobile/SaaS) Multi-App + Multi-Tenant safe by design High-performance read paths + transaction-…
- [PyOS.Webhook](#PyOS-webhook) — Enterprise-grade Webhook Operating Layer (Inbound + Outbound) Multi-App + Multi-Tenant compatible by Context + Unit resolver Signature verify (HMAC), replay…
- [PyOS.Wellness](#PyOS-wellness) — Universal Wellness Domain Engine for PyOS (Multi-App + Multi-Tenant) Standardize Wellness Profile, Events, Habits, Goals, Aggregates High-performance, audit-…
- [PyOS.Withdraw](#PyOS-withdraw) — Enterprise Grade Withdraw Engine for Multi-App + Multi-Tenant PyOS.
- [PyOS.Worker](#PyOS-worker) — OS-grade background job execution engine Multi-App + Multi-Tenant + Fair Scheduling Safe async execution with retries, locks, metrics
- [PyOS.Workflow](#PyOS-workflow) — Workflow-as-OS-Resource for PyOS backend Multi-App + Multi-Tenant workflow definition + execution State/DAG/Saga-ready (v1 focuses on deterministic sequential…

## Engine Details
### <span id="PyOS-abtest"></span>PyOS.ABTest
- **Purpose**: Enterprise-grade A/B Testing & Experiment Decision Engine Multi-Tenant + Multi-App + Multi-Brand compatible Deterministic, sticky, cache-first variant assignment Deeply integrated with PyOS core engines
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.ABTest.py](PyOS/Py.26.00.00/Engine/PyOS.ABTest.py)
- **Class**: `PyOS_ABTest`
- **Facade Entry Points**:
  - `PyOS.ABTest()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.ABTest()` |
### <span id="PyOS-abuse"></span>PyOS.Abuse
- **Purpose**: Behaviour-based abuse detection & prevention engine Adaptive risk scoring (not fixed rate limit) Multi-App + Multi-Tenant safe High-performance (cache-first, O(1) hot path)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Abuse.py](PyOS/Py.26.00.00/Engine/PyOS.Abuse.py)
- **Class**: `PyOS_Abuse`
- **Facade Entry Points**:
  - `PyOS.Abuse()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AbuseDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `PyOS.AbuseFail()` → `public static function Fail(array $Payload, array $Context)`
  - `PyOS.AbuseSuccess()` → `public static function Success(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Abuse()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `PyOS.AbuseDecide()` |
| Fail | `public static function Fail(array $Payload, array $Context)` | `PyOS.AbuseFail()` |
| Success | `public static function Success(array $Payload, array $Context)` | `PyOS.AbuseSuccess()` |
### <span id="PyOS-account"></span>PyOS.Account
- **Purpose**: Central Account Engine for PyOS Backend Operating System Multi-App + Multi-Tenant Account Management High-Performance, Cache-Aware, Audit-Ready Acts as Identity Container for all apps and tenants
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Account.py](PyOS/Py.26.00.00/Engine/PyOS.Account.py)
- **Class**: `PyOS_Account`
- **Facade Entry Points**:
  - `PyOS.Account()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AccountNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.AccountGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.AccountStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Account()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.AccountNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.AccountGet()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.AccountStatus()` |
### <span id="PyOS-agent"></span>PyOS.Agent
- **Purpose**: Autonomous Agent Orchestration Engine for PyOS Acts as AI / Automation Workforce Layer Multi-App + Multi-Tenant safe Enterprise Governance + Policy aware
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Agent.py](PyOS/Py.26.00.00/Engine/PyOS.Agent.py)
- **Class**: `PyOS_Agent`
- **Facade Entry Points**:
  - `PyOS.Agent()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AgentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.AgentRun()` → `public static function Run(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Agent()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.AgentNew()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.AgentRun()` |
### <span id="PyOS-ai"></span>PyOS.AI
- **Purpose**: Enterprise Grade AI Orchestration Engine for PyOS (Multi-App / Multi-Tenant) Unified AI Runtime: Chat / Run / Embed / RAG / AgentRun Safe-by-default: scope, limits, policy gates, audit, rate-limit, cache Provider abstraction: Azure/OpenAI/Local via HTTP Engine + Secrets Engine
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.AI.py](PyOS/Py.26.00.00/Engine/PyOS.AI.py)
- **Class**: `PyOS_AI`
- **Facade Entry Points**:
  - `PyOS.AI()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AIChat()` → `public static function Chat(array $Payload, array $Context)`
  - `PyOS.AIRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.AIEmbed()` → `public static function Embed(array $Payload, array $Context)`
  - `PyOS.AIRagQuery()` → `public static function RagQuery(array $Payload, array $Context)`
  - `PyOS.AIAgentRun()` → `public static function AgentRun(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.AI()` |
| Chat | `public static function Chat(array $Payload, array $Context)` | `PyOS.AIChat()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.AIRun()` |
| Embed | `public static function Embed(array $Payload, array $Context)` | `PyOS.AIEmbed()` |
| RagQuery | `public static function RagQuery(array $Payload, array $Context)` | `PyOS.AIRagQuery()` |
| AgentRun | `public static function AgentRun(array $Payload, array $Context)` | `PyOS.AIAgentRun()` |
### <span id="PyOS-alert"></span>PyOS.Alert
- **Purpose**: PyOS Nervous System: Alert Event → Dedup → Incident → Dispatch → Delivery Multi-App + Multi-Tenant safe routing and isolation High-Performance, non-blocking delivery via Queue/Worker ---------------------------------------------------------------------------- DESIGN (AliensGrade): Boot files = orchestration only Engine = intelligence + policies No direct requires/includes here (EngineRouter loads this file) All dependencies called via PyOS::EngineAction() facade ---------------------------------------------------------------------------- DEPENDENCIES (Expected Engines): PyOS.Cache   : CacheGet, CacheSet (or Cache Main wrapper) PyOS.Lock    : LockAcquire, LockRelease PyOS.Queue   : QueuePush PyOS.Database: DatabaseNew, DatabaseUpdate, DatabaseRow, DatabaseRows PyOS.Security: SecurityHash, SecurityNow, SecurityValidate PyOS.Log     : Log (or LOG) PyOS.Metric  : MetricInc, MetricTiming PyOS.Trace   : TraceStart, TraceEnd (optional) ---------------------------------------------------------------------------- TABLES (Recommended): PyOS_alert_event PyOS_alert_incident PyOS_alert_delivery PyOS_alert_ack PyOS_alert_rule PyOS_alert_policy ---------------------------------------------------------------------------- PUBLIC API (Loader Calls): PyOS::AlertNew([...])        → create event + incident + dispatch jobs PyOS::AlertDispatch([...])   → dispatch deliveries (usually Worker) PyOS::AlertAck([...])        → acknowledge incident PyOS::AlertResolve([...])    → resolve incident PyOS::AlertSnooze([...])     → snooze incident PyOS::AlertIncidentGet([...]) PyOS::AlertDeliveryList([...]) PyOS::ALERT([...])           → Main() ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Alert.py](PyOS/Py.26.00.00/Engine/PyOS.Alert.py)
- **Class**: `PyOS_Alert`
- **Facade Entry Points**:
  - `PyOS.Alert()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.AlertNew()` → `public static function New($Payload = [], $Context = [])`
  - `PyOS.AlertDispatch()` → `public static function Dispatch($Payload = [], $Context = [])`
  - `PyOS.AlertAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `PyOS.AlertSnooze()` → `public static function Snooze($Payload = [], $Context = [])`
  - `PyOS.AlertResolve()` → `public static function Resolve($Payload = [], $Context = [])`
  - `PyOS.AlertIncidentGet()` → `public static function IncidentGet($Payload = [], $Context = [])`
  - `PyOS.AlertDeliveryList()` → `public static function DeliveryList($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Alert()` |
| New | `public static function New($Payload = [], $Context = [])` | `PyOS.AlertNew()` |
| Dispatch | `public static function Dispatch($Payload = [], $Context = [])` | `PyOS.AlertDispatch()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `PyOS.AlertAck()` |
| Snooze | `public static function Snooze($Payload = [], $Context = [])` | `PyOS.AlertSnooze()` |
| Resolve | `public static function Resolve($Payload = [], $Context = [])` | `PyOS.AlertResolve()` |
| IncidentGet | `public static function IncidentGet($Payload = [], $Context = [])` | `PyOS.AlertIncidentGet()` |
| DeliveryList | `public static function DeliveryList($Payload = [], $Context = [])` | `PyOS.AlertDeliveryList()` |
### <span id="PyOS-analytics"></span>PyOS.Analytics
- **Purpose**: OS-level analytics & telemetry engine for PyOS Multi-App, Multi-Tenant, High-Performance analytics core Unified tracking for API, Cron, Worker, Webhook, System units
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Analytics.py](PyOS/Py.26.00.00/Engine/PyOS.Analytics.py)
- **Class**: `PyOS_Analytics`
- **Facade Entry Points**:
  - `PyOS.Analytics()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AnalyticsTrack()` → `public static function Track(array $Payload, array $Context)`
  - `PyOS.AnalyticsBatch()` → `public static function Batch(array $Payload, array $Context)`
  - `PyOS.AnalyticsQuery()` → `public static function Query(array $Payload, array $Context)`
  - `PyOS.AnalyticsRollup()` → `public static function Rollup(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Analytics()` |
| Track | `public static function Track(array $Payload, array $Context)` | `PyOS.AnalyticsTrack()` |
| Batch | `public static function Batch(array $Payload, array $Context)` | `PyOS.AnalyticsBatch()` |
| Query | `public static function Query(array $Payload, array $Context)` | `PyOS.AnalyticsQuery()` |
| Rollup | `public static function Rollup(array $Payload, array $Context)` | `PyOS.AnalyticsRollup()` |
### <span id="PyOS-api"></span>PyOS.API
- **Purpose**: Central API Gateway Engine for PyOS Enterprise-grade API orchestration Multi-App, Multi-Tenant, High-Performance backend core
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.API.py](PyOS/Py.26.00.00/Engine/PyOS.API.py)
- **Class**: `PyOS_API`
- **Facade Entry Points**:
  - `PyOS.API()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.API()` |
### <span id="PyOS-appointment"></span>PyOS.Appointment
- **Purpose**: Universal, multi-tenant, multi-app Appointment & Scheduling Engine Supports clinics, services, meetings, demos, education, enterprise use Conflict-safe booking with Hold → Confirm flow Fully compatible with PyOS Loader & EngineRouter
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Appointment.py](PyOS/Py.26.00.00/Engine/PyOS.Appointment.py)
- **Class**: `PyOS_Appointment`
- **Facade Entry Points**:
  - `PyOS.Appointment()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AppointmentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.AppointmentGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.AppointmentList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.AppointmentCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.AppointmentReschedule()` → `public static function Reschedule(array $Payload, array $Context)`
  - `PyOS.AppointmentSlotHold()` → `public static function SlotHold(array $Payload, array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Appointment()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.AppointmentNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.AppointmentGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.AppointmentList()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.AppointmentCancel()` |
| Reschedule | `public static function Reschedule(array $Payload, array $Context)` | `PyOS.AppointmentReschedule()` |
| SlotHold | `public static function SlotHold(array $Payload, array $Context = [])` | `PyOS.AppointmentSlotHold()` |
### <span id="PyOS-assetmgmt"></span>PyOS.AssetMgmt
- **Purpose**: Enterprise-grade Asset Management Engine Multi-App + Multi-Tenant compatible Physical, Digital, License, Subscription assets Full lifecycle: Create → Assign → Maintain → Audit → Retire
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.AssetMgmt.py](PyOS/Py.26.00.00/Engine/PyOS.AssetMgmt.py)
- **Class**: `PyOS_AssetMgmt`
- **Facade Entry Points**:
  - `PyOS.AssetMgmt()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AssetMgmtAssetNew()` → `public static function AssetNew(array $Payload, array $Context)`
  - `PyOS.AssetMgmtAssetGet()` → `public static function AssetGet(array $Payload, array $Context)`
  - `PyOS.AssetMgmtAssetUpdate()` → `public static function AssetUpdate(array $Payload, array $Context)`
  - `PyOS.AssetMgmtAssign()` → `public static function Assign(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.AssetMgmt()` |
| AssetNew | `public static function AssetNew(array $Payload, array $Context)` | `PyOS.AssetMgmtAssetNew()` |
| AssetGet | `public static function AssetGet(array $Payload, array $Context)` | `PyOS.AssetMgmtAssetGet()` |
| AssetUpdate | `public static function AssetUpdate(array $Payload, array $Context)` | `PyOS.AssetMgmtAssetUpdate()` |
| Assign | `public static function Assign(array $Payload, array $Context)` | `PyOS.AssetMgmtAssign()` |
### <span id="PyOS-assignment"></span>PyOS.Assignment
- **Purpose**: Enterprise-Grade Assignment Management Engine Multi-App + Multi-Tenant + High-Performance Education, Training, Enterprise Tasks compatible
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Assignment.py](PyOS/Py.26.00.00/Engine/PyOS.Assignment.py)
- **Class**: `PyOS_Assignment`
- **Facade Entry Points**:
  - `PyOS.Assignment()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AssignmentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.AssignmentList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.AssignmentGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.AssignmentSubmit()` → `public static function Submit(array $Payload, array $Context)`
  - `PyOS.AssignmentGrade()` → `public static function Grade(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Assignment()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.AssignmentNew()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.AssignmentList()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.AssignmentGet()` |
| Submit | `public static function Submit(array $Payload, array $Context)` | `PyOS.AssignmentSubmit()` |
| Grade | `public static function Grade(array $Payload, array $Context)` | `PyOS.AssignmentGrade()` |
### <span id="PyOS-attendance"></span>PyOS.Attendance
- **Purpose**: Enterprise-grade Attendance Management Engine Multi-App, Multi-Tenant, High-Performance OS-level backend component for PyOS
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Attendance.py](PyOS/Py.26.00.00/Engine/PyOS.Attendance.py)
- **Class**: `PyOS_Attendance`
- **Facade Entry Points**:
  - `PyOS.Attendance()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AttendanceSessionNew()` → `public static function SessionNew(array $Payload, array $Context)`
  - `PyOS.AttendanceMark()` → `public static function Mark(array $Payload, array $Context)`
  - `PyOS.AttendanceSummary()` → `public static function Summary(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Attendance()` |
| SessionNew | `public static function SessionNew(array $Payload, array $Context)` | `PyOS.AttendanceSessionNew()` |
| Mark | `public static function Mark(array $Payload, array $Context)` | `PyOS.AttendanceMark()` |
| Summary | `public static function Summary(array $Payload, array $Context)` | `PyOS.AttendanceSummary()` |
### <span id="PyOS-audit"></span>PyOS.Audit
- **Purpose**: Centralized, tamper-evident audit engine for PyOS Append-only audit fabric (API / Cron / Webhook / System) Multi-App + Multi-Tenant isolation High-performance write path Privacy-safe, compliance-ready (SOC2 / ISO / GDPR)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Audit.py](PyOS/Py.26.00.00/Engine/PyOS.Audit.py)
- **Class**: `PyOS_Audit`
- **Facade Entry Points**:
  - `PyOS.Audit()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AuditLog()` → `public static function Log(array $Payload, array $Context)`
  - `PyOS.AuditVerify()` → `public static function Verify(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Audit()` |
| Log | `public static function Log(array $Payload, array $Context)` | `PyOS.AuditLog()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.AuditVerify()` |
### <span id="PyOS-auth"></span>PyOS.Auth
- **Purpose**: Enterprise Auth Brain for PyOS (Multi-App + Multi-Tenant) Identity (User/App/Tenant) Context Session validation + Force logout hooks Token parsing (Bearer/API-Key) + lightweight signed token support Highlights  : Boot-friendly (Orchestration-only boots call this engine) Deny-by-default security mindset High-performance: minimal DB assumptions + cache-ready Dependencies : PyOS::Cache()   (optional but recommended) PyOS::Config()  (optional; used for secrets/TTLs/policies) PyOS::Log()     (optional; for security logging) PyOS::Security()(optional; for IP/user-agent checks) Globals/Consts   : App, i Notes : DB tables (sessions/tokens/attempts) are integration-ready via stubs. ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Auth.py](PyOS/Py.26.00.00/Engine/PyOS.Auth.py)
- **Class**: `PyOS_Auth`
- **Facade Entry Points**: None documented
### <span id="PyOS-automation"></span>PyOS.Automation
- **Purpose**: System-level Automation Engine for PyOS. Provides enterprise-grade event → rule → pipeline → action orchestration across Multi-App, Multi-Brand, Multi-Tenant environments.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Automation.py](PyOS/Py.26.00.00/Engine/PyOS.Automation.py)
- **Class**: `PyOS_Automation`
- **Facade Entry Points**:
  - `PyOS.Automation()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.AutomationNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.AutomationList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.AutomationTrigger()` → `public static function Trigger(array $Payload, array $Context)`
  - `PyOS.AutomationConsume()` → `public static function Consume(array $Payload, array $Context)`
  - `PyOS.AutomationRun()` → `public static function Run(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Automation()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.AutomationNew()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.AutomationList()` |
| Trigger | `public static function Trigger(array $Payload, array $Context)` | `PyOS.AutomationTrigger()` |
| Consume | `public static function Consume(array $Payload, array $Context)` | `PyOS.AutomationConsume()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.AutomationRun()` |
### <span id="PyOS-backup"></span>PyOS.Backup
- **Purpose**: OS-level Backup & Restore Engine for PyOS Multi-App, Multi-Tenant, Enterprise-grade Manifest-driven, Secure, Verifiable backups
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Backup.py](PyOS/Py.26.00.00/Engine/PyOS.Backup.py)
- **Class**: `PyOS_Backup`
- **Facade Entry Points**:
  - `PyOS.Backup()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.BackupCreate()` → `public static function Create(array $Payload, array $Context)`
  - `PyOS.BackupRestore()` → `public static function Restore(array $Payload, array $Context)`
  - `PyOS.BackupVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.BackupList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.BackupDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.BackupPolicySet()` → `public static function PolicySet(array $Payload, array $Context)`
  - `PyOS.BackupScheduleSet()` → `public static function ScheduleSet(array $Payload, array $Context)`
  - `PyOS.BackupExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Backup()` |
| Create | `public static function Create(array $Payload, array $Context)` | `PyOS.BackupCreate()` |
| Restore | `public static function Restore(array $Payload, array $Context)` | `PyOS.BackupRestore()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.BackupVerify()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.BackupList()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.BackupDelete()` |
| PolicySet | `public static function PolicySet(array $Payload, array $Context)` | `PyOS.BackupPolicySet()` |
| ScheduleSet | `public static function ScheduleSet(array $Payload, array $Context)` | `PyOS.BackupScheduleSet()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.BackupExport()` |
### <span id="PyOS-balance"></span>PyOS.Balance
- **Purpose**: Balance state management (Available / Locked / Pending / Total) Ledger / Transaction driven balance updates Multi-Tenant + Multi-App compatible High-concurrency safe (lock + version aware)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Balance.py](PyOS/Py.26.00.00/Engine/PyOS.Balance.py)
- **Class**: `PyOS_Balance`
- **Facade Entry Points**:
  - `PyOS.Balance()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.BalanceGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.BalanceApply()` → `public static function Apply(array $Payload, array $Context)`
  - `PyOS.BalanceHold()` → `public static function Hold(array $Payload, array $Context)`
  - `PyOS.BalanceRelease()` → `public static function Release(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Balance()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.BalanceGet()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `PyOS.BalanceApply()` |
| Hold | `public static function Hold(array $Payload, array $Context)` | `PyOS.BalanceHold()` |
| Release | `public static function Release(array $Payload, array $Context)` | `PyOS.BalanceRelease()` |
### <span id="PyOS-billing"></span>PyOS.Billing
- **Purpose**: Unified Billing Engine for PyOS Multi-App + Multi-Tenant + Multi-Brand billing brain Handles Products, Prices, Invoices, Subscriptions coordination
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Billing.py](PyOS/Py.26.00.00/Engine/PyOS.Billing.py)
- **Class**: `PyOS_Billing`
- **Facade Entry Points**:
  - `PyOS.Billing()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.BillingProductUpsert()` → `public static function ProductUpsert(array $Payload, array $Context)`
  - `PyOS.BillingInvoiceDraft()` → `public static function InvoiceDraft(array $Payload, array $Context)`
  - `PyOS.BillingInvoiceAddItem()` → `public static function InvoiceAddItem(array $Payload, array $Context)`
  - `PyOS.BillingInvoiceFinalize()` → `public static function InvoiceFinalize(array $Payload, array $Context)`
  - `PyOS.BillingInvoicePay()` → `public static function InvoicePay(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Billing()` |
| ProductUpsert | `public static function ProductUpsert(array $Payload, array $Context)` | `PyOS.BillingProductUpsert()` |
| InvoiceDraft | `public static function InvoiceDraft(array $Payload, array $Context)` | `PyOS.BillingInvoiceDraft()` |
| InvoiceAddItem | `public static function InvoiceAddItem(array $Payload, array $Context)` | `PyOS.BillingInvoiceAddItem()` |
| InvoiceFinalize | `public static function InvoiceFinalize(array $Payload, array $Context)` | `PyOS.BillingInvoiceFinalize()` |
| InvoicePay | `public static function InvoicePay(array $Payload, array $Context)` | `PyOS.BillingInvoicePay()` |
### <span id="PyOS-blockchain"></span>PyOS.Blockchain
- **Purpose**: Blockchain abstraction engine for PyOS Chain-agnostic (EVM, Tron, Solana, Internal Ledger) Secure orchestration of wallets, balances, transfers & indexing High-performance, async-ready, auditable backend OS layer
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Blockchain.py](PyOS/Py.26.00.00/Engine/PyOS.Blockchain.py)
- **Class**: `PyOS_Blockchain`
- **Facade Entry Points**:
  - `PyOS.Blockchain()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.BlockchainChains()` → `public static function Chains(array $Payload, array $Context)`
  - `PyOS.BlockchainBalance()` → `public static function Balance(array $Payload, array $Context)`
  - `PyOS.BlockchainTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `PyOS.BlockchainTxGet()` → `public static function TxGet(array $Payload, array $Context)`
  - `PyOS.BlockchainIndexRun()` → `public static function IndexRun(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Blockchain()` |
| Chains | `public static function Chains(array $Payload, array $Context)` | `PyOS.BlockchainChains()` |
| Balance | `public static function Balance(array $Payload, array $Context)` | `PyOS.BlockchainBalance()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `PyOS.BlockchainTransfer()` |
| TxGet | `public static function TxGet(array $Payload, array $Context)` | `PyOS.BlockchainTxGet()` |
| IndexRun | `public static function IndexRun(array $Payload, array $Context)` | `PyOS.BlockchainIndexRun()` |
### <span id="PyOS-boot"></span>PyOS.Boot
- **Purpose**: Initialize complete Aliens ecosystem for each request Highlights : Normalizes Python environment (timezone, encoding, limits) Uses Aliens['PyOS']['version'] + ['path'] as single source of truth Prepares unified Aliens container for Paths / Request / System Loads Config + Manifest with merge strategy Detects Tenant + App + Brand (Multi-App + Multi-Tenant ready) Initializes core Engines (Log, Cache, Database, Session) Measures Boot time (ms) for performance monitoring Dependencies: Aliens['PyOS']['path'] / Aliens['PyOS']['version'] (pre-filled before Boot) d($Msg) / x($Msg,$Type,$Unit) for logging (optional but recommended) PyOS::Log(), PyOS::Cache(), PyOS::Database(), PyOS::Session() engines Style      : AliensStyle (PascalCase names, clean sections, echo-logging, no jugaad) Hinglish allowed in comments, no Hindi script ============================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Boot.py](PyOS/Py.26.00.00/Engine/PyOS.Boot.py)
- **Class**: `PyOS_Boot`
- **Facade Entry Points**:
  - `PyOS.Boot()` → `public static function Main(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Boot()` |
### <span id="PyOS-botdetect"></span>PyOS.BotDetect
- **Purpose**: Enterprise-grade bot detection + abuse scoring for all PyOS entry points. Works in Multi-App + Multi-Tenant environment using Context isolation. Returns deterministic Decision object: Allow + Action + RiskScore + Reasons + TTL + Key + Meta Designed for High-Performance: cache-first counters, minimal parsing.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.BotDetect.py](PyOS/Py.26.00.00/Engine/PyOS.BotDetect.py)
- **Class**: `PyOS_BotDetect`
- **Facade Entry Points**:
  - `PyOS.BotDetect()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.BotDetectAssess()` → `public static function Assess(array $Payload = [], array $Context = [])`
  - `PyOS.BotDetectSignal()` → `public static function Signal(array $Payload = [], array $Context = [])`
  - `PyOS.BotDetectBlock()` → `public static function Block(array $Payload = [], array $Context = [])`
  - `PyOS.BotDetectAllowList()` → `public static function AllowList(array $Payload = [], array $Context = [])`
  - `PyOS.BotDetectStats()` → `public static function Stats(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.BotDetect()` |
| Assess | `public static function Assess(array $Payload = [], array $Context = [])` | `PyOS.BotDetectAssess()` |
| Signal | `public static function Signal(array $Payload = [], array $Context = [])` | `PyOS.BotDetectSignal()` |
| Block | `public static function Block(array $Payload = [], array $Context = [])` | `PyOS.BotDetectBlock()` |
| AllowList | `public static function AllowList(array $Payload = [], array $Context = [])` | `PyOS.BotDetectAllowList()` |
| Stats | `public static function Stats(array $Payload = [], array $Context = [])` | `PyOS.BotDetectStats()` |
### <span id="PyOS-brand"></span>PyOS.Brand
- **Purpose**: Brand resolution layer for PyOS Multi-App + Multi-Tenant + Multi-Domain support White-label / Enterprise branding engine
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Brand.py](PyOS/Py.26.00.00/Engine/PyOS.Brand.py)
- **Class**: `PyOS_Brand`
- **Facade Entry Points**:
  - `PyOS.Brand()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.BrandResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.BrandResolveFromDB()` → `public static function ResolveFromDB(array $Args)`
  - `PyOS.BrandGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.BrandGetFromDB()` → `public static function GetFromDB(array $Args)`
  - `PyOS.BrandConfig()` → `public static function Config(array $Payload, array $Context)`
  - `PyOS.BrandBuildConfig()` → `public static function BuildConfig(array $Args)`
  - `PyOS.BrandInvalidateCache()` → `public static function InvalidateCache(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Brand()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.BrandResolve()` |
| ResolveFromDB | `public static function ResolveFromDB(array $Args)` | `PyOS.BrandResolveFromDB()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.BrandGet()` |
| GetFromDB | `public static function GetFromDB(array $Args)` | `PyOS.BrandGetFromDB()` |
| Config | `public static function Config(array $Payload, array $Context)` | `PyOS.BrandConfig()` |
| BuildConfig | `public static function BuildConfig(array $Args)` | `PyOS.BrandBuildConfig()` |
| InvalidateCache | `public static function InvalidateCache(array $Payload, array $Context)` | `PyOS.BrandInvalidateCache()` |
### <span id="PyOS-cache"></span>PyOS.Cache
- **Purpose**: Enterprise-grade universal caching engine for PyOS 26.x DESIGN      : Mode-Detection, Auto-UID, TTL, Logging, Force-Fresh.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Cache.py](PyOS/Py.26.00.00/Engine/PyOS.Cache.py)
- **Class**: `PyOS_Cache`
- **Facade Entry Points**:
  - `PyOS.Cache()` → `public static function Main($Payload, $Context)`
  - `PyOS.CacheGet()` → `public static function Get($Payload, $Context)`
  - `PyOS.CacheDelete()` → `public static function Delete($Payload, $Context)`
  - `PyOS.CacheCacheFlushAll()` → `public static function CacheFlushAll()`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.Cache()` |
| Get | `public static function Get($Payload, $Context)` | `PyOS.CacheGet()` |
| Delete | `public static function Delete($Payload, $Context)` | `PyOS.CacheDelete()` |
| CacheFlushAll | `public static function CacheFlushAll()` | `PyOS.CacheCacheFlushAll()` |
### <span id="PyOS-cart"></span>PyOS.Cart
- **Purpose**: Universal Cart Engine for PyOS Works for ANY type of backend: E-commerce Food / Booking Subscription Services Marketplace Multi-Tenant + Multi-App safe High-Performance OS-level Cart
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Cart.py](PyOS/Py.26.00.00/Engine/PyOS.Cart.py)
- **Class**: `PyOS_Cart`
- **Facade Entry Points**:
  - `PyOS.Cart()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.CartGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.CartAdd()` → `public static function Add(array $Payload, array $Context)`
  - `PyOS.CartUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.CartRemove()` → `public static function Remove(array $Payload, array $Context)`
  - `PyOS.CartClear()` → `public static function Clear(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Cart()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.CartGet()` |
| Add | `public static function Add(array $Payload, array $Context)` | `PyOS.CartAdd()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.CartUpdate()` |
| Remove | `public static function Remove(array $Payload, array $Context)` | `PyOS.CartRemove()` |
| Clear | `public static function Clear(array $Payload, array $Context)` | `PyOS.CartClear()` |
### <span id="PyOS-category"></span>PyOS.Category
- **Purpose**: Universal Category / Taxonomy Engine for PyOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph extensions High-performance read patterns with cache + selective invalidation Enterprise governance: validation, policy checks, audit hooks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Category.py](PyOS/Py.26.00.00/Engine/PyOS.Category.py)
- **Class**: `PyOS_Category`
- **Facade Entry Points**:
  - `PyOS.Category()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.CategoryGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.CategoryList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.CategorySearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.CategoryNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.CategoryUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.CategoryStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.CategoryDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.CategoryTree()` → `public static function Tree(array $Payload, array $Context)`
  - `PyOS.CategoryChildren()` → `public static function Children(array $Payload, array $Context)`
  - `PyOS.CategoryBreadcrumb()` → `public static function Breadcrumb(array $Payload, array $Context)`
  - `PyOS.CategoryMove()` → `public static function Move(array $Payload, array $Context)`
  - `PyOS.CategoryRebuild()` → `public static function Rebuild(array $Payload, array $Context)`
  - `PyOS.CategoryMapAdd()` → `public static function MapAdd(array $Payload, array $Context)`
  - `PyOS.CategoryMapRemove()` → `public static function MapRemove(array $Payload, array $Context)`
  - `PyOS.CategoryMapList()` → `public static function MapList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Category()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.CategoryGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.CategoryList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.CategorySearch()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.CategoryNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.CategoryUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.CategoryStatus()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.CategoryDelete()` |
| Tree | `public static function Tree(array $Payload, array $Context)` | `PyOS.CategoryTree()` |
| Children | `public static function Children(array $Payload, array $Context)` | `PyOS.CategoryChildren()` |
| Breadcrumb | `public static function Breadcrumb(array $Payload, array $Context)` | `PyOS.CategoryBreadcrumb()` |
| Move | `public static function Move(array $Payload, array $Context)` | `PyOS.CategoryMove()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `PyOS.CategoryRebuild()` |
| MapAdd | `public static function MapAdd(array $Payload, array $Context)` | `PyOS.CategoryMapAdd()` |
| MapRemove | `public static function MapRemove(array $Payload, array $Context)` | `PyOS.CategoryMapRemove()` |
| MapList | `public static function MapList(array $Payload, array $Context)` | `PyOS.CategoryMapList()` |
### <span id="PyOS-certificate"></span>PyOS.Certificate
- **Purpose**: Centralized Certificate Engine for PyOS Issue, Validate, Revoke, Renew digital certificates Multi-App + Multi-Tenant compatible Secure signing, verification and audit tracking
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Certificate.py](PyOS/Py.26.00.00/Engine/PyOS.Certificate.py)
- **Class**: `PyOS_Certificate`
- **Facade Entry Points**:
  - `PyOS.Certificate()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.CertificateIssue()` → `public static function Issue(array $Payload, array $Context)`
  - `PyOS.CertificateValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.CertificateRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `PyOS.CertificateRenew()` → `public static function Renew(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Certificate()` |
| Issue | `public static function Issue(array $Payload, array $Context)` | `PyOS.CertificateIssue()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.CertificateValidate()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `PyOS.CertificateRevoke()` |
| Renew | `public static function Renew(array $Payload, array $Context)` | `PyOS.CertificateRenew()` |
### <span id="PyOS-chat"></span>PyOS.Chat
- **Purpose**: Enterprise-grade Chat backbone for any PyOS App (Multi-App + Multi-Tenant) DM / Group / Channel chat spaces Messaging, membership, receipts scaffolding High-performance read paths (cache-ready), write paths (lock-ready) Strict isolation: TenantId + AppId on every query
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Chat.py](PyOS/Py.26.00.00/Engine/PyOS.Chat.py)
- **Class**: `PyOS_Chat`
- **Facade Entry Points**:
  - `PyOS.Chat()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ChatNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ChatList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.ChatSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.ChatMessages()` → `public static function Messages(array $Payload, array $Context)`
  - `PyOS.ChatMemberAdd()` → `public static function MemberAdd(array $Payload, array $Context)`
  - `PyOS.ChatMemberRemove()` → `public static function MemberRemove(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Chat()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ChatNew()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.ChatList()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.ChatSend()` |
| Messages | `public static function Messages(array $Payload, array $Context)` | `PyOS.ChatMessages()` |
| MemberAdd | `public static function MemberAdd(array $Payload, array $Context)` | `PyOS.ChatMemberAdd()` |
| MemberRemove | `public static function MemberRemove(array $Payload, array $Context)` | `PyOS.ChatMemberRemove()` |
### <span id="PyOS-class"></span>PyOS.Class
- **Purpose**: Universal Class / Module loader for PyOS Multi-App + Multi-Tenant safe instantiation High-performance registry + reflection cache Dependency-aware lightweight DI Strict AliensGrade security & isolation
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Class.py](PyOS/Py.26.00.00/Engine/PyOS.Class.py)
- **Class**: `across`
- **Facade Entry Points**:
  - `PyOS.Class()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ClassResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.ClassLoad()` → `public static function Load(array $Payload, array $Context)`
  - `PyOS.ClassGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ClassNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ClassHas()` → `public static function Has(array $Payload, array $Context)`
  - `PyOS.ClassForget()` → `public static function Forget(array $Payload, array $Context)`
  - `PyOS.ClassFlush()` → `public static function Flush(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Class()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.ClassResolve()` |
| Load | `public static function Load(array $Payload, array $Context)` | `PyOS.ClassLoad()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ClassGet()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ClassNew()` |
| Has | `public static function Has(array $Payload, array $Context)` | `PyOS.ClassHas()` |
| Forget | `public static function Forget(array $Payload, array $Context)` | `PyOS.ClassForget()` |
| Flush | `public static function Flush(array $Payload, array $Context)` | `PyOS.ClassFlush()` |
### <span id="PyOS-cluster"></span>PyOS.Cluster
- **Purpose**: Provide cluster-level intelligence to PyOS Enable multi-node, multi-region, multi-tenant backend execution Act as the backbone for high-availability, failover, and routing decisions
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Cluster.py](PyOS/Py.26.00.00/Engine/PyOS.Cluster.py)
- **Class**: `PyOS_Cluster`
- **Facade Entry Points**:
  - `PyOS.Cluster()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ClusterTopologyGet()` → `public static function TopologyGet(array $Payload, array $Context)`
  - `PyOS.ClusterNodeHeartbeat()` → `public static function NodeHeartbeat(array $Payload, array $Context)`
  - `PyOS.ClusterRouteResolve()` → `public static function RouteResolve(array $Payload, array $Context)`
  - `PyOS.ClusterFailover()` → `public static function Failover(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Cluster()` |
| TopologyGet | `public static function TopologyGet(array $Payload, array $Context)` | `PyOS.ClusterTopologyGet()` |
| NodeHeartbeat | `public static function NodeHeartbeat(array $Payload, array $Context)` | `PyOS.ClusterNodeHeartbeat()` |
| RouteResolve | `public static function RouteResolve(array $Payload, array $Context)` | `PyOS.ClusterRouteResolve()` |
| Failover | `public static function Failover(array $Payload, array $Context)` | `PyOS.ClusterFailover()` |
### <span id="PyOS-comment"></span>PyOS.Comment
- **Purpose**: Universal Comment Engine for PyOS Multi-App + Multi-Tenant + Enterprise Grade Threaded comments with moderation, reactions, audit
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Comment.py](PyOS/Py.26.00.00/Engine/PyOS.Comment.py)
- **Class**: `PyOS_Comment`
- **Facade Entry Points**:
  - `PyOS.Comment()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.CommentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.CommentList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.CommentDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Comment()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.CommentNew()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.CommentList()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.CommentDelete()` |
### <span id="PyOS-compliance"></span>PyOS.Compliance
- **Purpose**: Central Compliance & Governance Engine for PyOS Enforces data policies, consent, retention, audit Multi-App + Multi-Tenant + Region aware
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Compliance.py](PyOS/Py.26.00.00/Engine/PyOS.Compliance.py)
- **Class**: `PyOS_Compliance`
- **Facade Entry Points**:
  - `PyOS.Compliance()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ComplianceCheck()` → `public static function Check(array $Payload, array $Context)`
  - `PyOS.ComplianceGate()` → `public static function Gate(array $Payload, array $Context)`
  - `PyOS.ComplianceConsentGet()` → `public static function ConsentGet(array $Payload, array $Context)`
  - `PyOS.ComplianceConsentSet()` → `public static function ConsentSet(array $Payload, array $Context)`
  - `PyOS.ComplianceAudit()` → `public static function Audit(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Compliance()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.ComplianceCheck()` |
| Gate | `public static function Gate(array $Payload, array $Context)` | `PyOS.ComplianceGate()` |
| ConsentGet | `public static function ConsentGet(array $Payload, array $Context)` | `PyOS.ComplianceConsentGet()` |
| ConsentSet | `public static function ConsentSet(array $Payload, array $Context)` | `PyOS.ComplianceConsentSet()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `PyOS.ComplianceAudit()` |
### <span id="PyOS-compliancehr"></span>PyOS.ComplianceHR
- **Purpose**: HR Compliance Operating Layer (Policy → Rules → Decision → Audit) Multi-App + Multi-Tenant compatible via Context (app/brand/ecosystem/user) High-Performance: Policy caching + rule indexing + fast evaluation path ---------------------------------------------------------------------------- Dependencies (Engines via PyOS.Loader): PyOS::Security*   (sanitize + validation) PyOS::Auth*       (actor/session validation) PyOS::Role*       (role checks) PyOS::Permission* (fine-grained permission checks) PyOS::Cache*      (policy cache) PyOS::Database*   (persistence) PyOS::Audit*      (tamper-evident audit trail) PyOS::Task*       (approval/remediation tasks) PyOS::EventBus*   (events publish) PyOS::Log*        (structured logs) ---------------------------------------------------------------------------- AliensGrade Guarantees: Deny-by-default (missing policy/rule/unit → safe decision + auditable reason) No business logic in Boot files; Engine is pure backend power Deterministic outputs: Decision Contract is stable across apps/tenants ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.ComplianceHR.py](PyOS/Py.26.00.00/Engine/PyOS.ComplianceHR.py)
- **Class**: `PyOS_ComplianceHR`
- **Facade Entry Points**:
  - `PyOS.ComplianceHR()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.ComplianceHREvaluate()` → `public static function Evaluate($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRPolicyList()` → `public static function PolicyList($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRPolicyGet()` → `public static function PolicyGet($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRPolicyNew()` → `public static function PolicyNew($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRPolicyUpdate()` → `public static function PolicyUpdate($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRPolicyActivate()` → `public static function PolicyActivate($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRPolicyRollback()` → `public static function PolicyRollback($Payload = [], $Context = [])`
  - `PyOS.ComplianceHREvidenceNew()` → `public static function EvidenceNew($Payload = [], $Context = [])`
  - `PyOS.ComplianceHREvidenceVerify()` → `public static function EvidenceVerify($Payload = [], $Context = [])`
  - `PyOS.ComplianceHREvidenceLink()` → `public static function EvidenceLink($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRViolationNew()` → `public static function ViolationNew($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRViolationAssign()` → `public static function ViolationAssign($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRViolationClose()` → `public static function ViolationClose($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRViolationEscalate()` → `public static function ViolationEscalate($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRReportGenerate()` → `public static function ReportGenerate($Payload = [], $Context = [])`
  - `PyOS.ComplianceHRReportExport()` → `public static function ReportExport($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.ComplianceHR()` |
| Evaluate | `public static function Evaluate($Payload = [], $Context = [])` | `PyOS.ComplianceHREvaluate()` |
| PolicyList | `public static function PolicyList($Payload = [], $Context = [])` | `PyOS.ComplianceHRPolicyList()` |
| PolicyGet | `public static function PolicyGet($Payload = [], $Context = [])` | `PyOS.ComplianceHRPolicyGet()` |
| PolicyNew | `public static function PolicyNew($Payload = [], $Context = [])` | `PyOS.ComplianceHRPolicyNew()` |
| PolicyUpdate | `public static function PolicyUpdate($Payload = [], $Context = [])` | `PyOS.ComplianceHRPolicyUpdate()` |
| PolicyActivate | `public static function PolicyActivate($Payload = [], $Context = [])` | `PyOS.ComplianceHRPolicyActivate()` |
| PolicyRollback | `public static function PolicyRollback($Payload = [], $Context = [])` | `PyOS.ComplianceHRPolicyRollback()` |
| EvidenceNew | `public static function EvidenceNew($Payload = [], $Context = [])` | `PyOS.ComplianceHREvidenceNew()` |
| EvidenceVerify | `public static function EvidenceVerify($Payload = [], $Context = [])` | `PyOS.ComplianceHREvidenceVerify()` |
| EvidenceLink | `public static function EvidenceLink($Payload = [], $Context = [])` | `PyOS.ComplianceHREvidenceLink()` |
| ViolationNew | `public static function ViolationNew($Payload = [], $Context = [])` | `PyOS.ComplianceHRViolationNew()` |
| ViolationAssign | `public static function ViolationAssign($Payload = [], $Context = [])` | `PyOS.ComplianceHRViolationAssign()` |
| ViolationClose | `public static function ViolationClose($Payload = [], $Context = [])` | `PyOS.ComplianceHRViolationClose()` |
| ViolationEscalate | `public static function ViolationEscalate($Payload = [], $Context = [])` | `PyOS.ComplianceHRViolationEscalate()` |
| ReportGenerate | `public static function ReportGenerate($Payload = [], $Context = [])` | `PyOS.ComplianceHRReportGenerate()` |
| ReportExport | `public static function ReportExport($Payload = [], $Context = [])` | `PyOS.ComplianceHRReportExport()` |
### <span id="PyOS-config"></span>PyOS.Config
- **Purpose**: Centralized configuration manager for PyOS Multi-App, Multi-Tenant config storage In-Memory + File + Database hybrid config system Auto-load configs from: App → Brand → SDK → PyOS Core Supports override chain & fallback chain
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Config.py](PyOS/Py.26.00.00/Engine/PyOS.Config.py)
- **Class**: `PyOS_Config`
- **Facade Entry Points**:
  - `PyOS.Config()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ConfigGet()` → `public static function Get(string $App = null, string $Key = null, $Default = null)`
  - `PyOS.ConfigSet()` → `public static function Set(string $App = null, string $Key = null, $Value = null)`
  - `PyOS.ConfigDelete()` → `public static function Delete(string $App = null, string $Key = null)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Config()` |
| Get | `public static function Get(string $App = null, string $Key = null, $Default = null)` | `PyOS.ConfigGet()` |
| Set | `public static function Set(string $App = null, string $Key = null, $Value = null)` | `PyOS.ConfigSet()` |
| Delete | `public static function Delete(string $App = null, string $Key = null)` | `PyOS.ConfigDelete()` |
### <span id="PyOS-constitution"></span>PyOS.Constitution
- **Purpose**: PyOS ka Supreme Governance Layer (Policy + Rules + Enforcement) Multi-App + Multi-Tenant + Enterprise Grade control plane Unit Execution (API/Cron/Webhook/Asset/Component/Template) ko rules se govern karna Security, Compliance, Data Governance, Cost/Performance Governor
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Constitution.py](PyOS/Py.26.00.00/Engine/PyOS.Constitution.py)
- **Class**: `PyOS_Constitution`
- **Facade Entry Points**:
  - `PyOS.Constitution()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionLoad()` → `public static function Load(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionEvaluate()` → `public static function Evaluate(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionEnforce()` → `public static function Enforce(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionCacheWarm()` → `public static function CacheWarm(array $Payload = [], array $Context = [])`
  - `PyOS.ConstitutionAudit()` → `public static function Audit(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Constitution()` |
| Load | `public static function Load(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionLoad()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionResolve()` |
| Evaluate | `public static function Evaluate(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionEvaluate()` |
| Enforce | `public static function Enforce(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionEnforce()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionExplain()` |
| CacheWarm | `public static function CacheWarm(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionCacheWarm()` |
| Audit | `public static function Audit(array $Payload = [], array $Context = [])` | `PyOS.ConstitutionAudit()` |
### <span id="PyOS-constraint"></span>PyOS.Constraint
- **Purpose**: Universal Constraint Enforcement Engine for PyOS Enforce hard/soft/observe rules across API, Data, Security, Business layers Multi-App + Multi-Tenant + Override-safe Deterministic, Auditable, High-Performance
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Constraint.py](PyOS/Py.26.00.00/Engine/PyOS.Constraint.py)
- **Class**: `PyOS_Constraint`
- **Facade Entry Points**:
  - `PyOS.Constraint()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ConstraintCheck()` → `public static function Check(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Constraint()` |
| Check | `public static function Check(array $Payload = [], array $Context = [])` | `PyOS.ConstraintCheck()` |
### <span id="PyOS-container"></span>PyOS.Container
- **Purpose**: Central runtime container for PyOS Context holder (Tenant, App, Brand, User, Env) Service registry with lazy-loading Multi-App + Multi-Tenant isolation Deterministic, secure, high-performance backend core
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Container.py](PyOS/Py.26.00.00/Engine/PyOS.Container.py)
- **Class**: `PyOS_Container`
- **Facade Entry Points**:
  - `PyOS.Container()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerInit()` → `public static function Init(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerBind()` → `public static function Bind(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerHas()` → `public static function Has(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerSeal()` → `public static function Seal(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerContext()` → `public static function Context(array $Payload = [], array $Context = [])`
  - `PyOS.ContainerStats()` → `public static function Stats(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Container()` |
| Init | `public static function Init(array $Payload = [], array $Context = [])` | `PyOS.ContainerInit()` |
| Bind | `public static function Bind(array $Payload = [], array $Context = [])` | `PyOS.ContainerBind()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `PyOS.ContainerSet()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.ContainerGet()` |
| Has | `public static function Has(array $Payload = [], array $Context = [])` | `PyOS.ContainerHas()` |
| Seal | `public static function Seal(array $Payload = [], array $Context = [])` | `PyOS.ContainerSeal()` |
| Context | `public static function Context(array $Payload = [], array $Context = [])` | `PyOS.ContainerContext()` |
| Stats | `public static function Stats(array $Payload = [], array $Context = [])` | `PyOS.ContainerStats()` |
### <span id="PyOS-content"></span>PyOS.Content
- **Purpose**: Universal Content Operating Engine for PyOS Supports ANY website / PyApp / SaaS / app backend Multi-Tenant + Multi-App + Multi-Brand aware Draft → Publish → Version → Cache → Search ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Content.py](PyOS/Py.26.00.00/Engine/PyOS.Content.py)
- **Class**: `PyOS_Content`
- **Facade Entry Points**:
  - `PyOS.Content()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ContentGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ContentList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.ContentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ContentUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.ContentPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `PyOS.ContentDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Content()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ContentGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.ContentList()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ContentNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.ContentUpdate()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.ContentPublish()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.ContentDelete()` |
### <span id="PyOS-context"></span>PyOS.Context
- **Purpose**: Centralized Context Manager for entire PyOS lifecycle Replaces scattered globals ($i, $app, $brand, $ecosystem) Provides unified API: ContextGet, ContextSet, ContextHas, ContextPick Supports Multi-App + Multi-Tenant isolation Lazy-loaded nodes (user/app/brand/tenant/runtime) Snapshot + Restore for Worker / Queue Jobs Scoped Overrides for internal tasks & temporary impersonation ---------------------------------------------------------------------------- Dependencies: PyOS.EngineRouter (Engine Resolver Layer) PyOS.Auth (Lazy user loader) PyOS.App   (App resolver) PyOS.Brand (Brand resolver) PyOS.Tenant (Ecosystem / tenant resolver) PyOS.Request / PyOS.Response (Optional integrations) ---------------------------------------------------------------------------- AliensStyle Rules: PascalCase for everything CleanCode structure with Section Headers No Hindi script (Hinglish allowed) Engine must be lightweight, powerful, override-friendly ---------------------------------------------------------------------------- Highlights: Multi-layer context tree (request/user/app/brand/tenant/env/runtime/...) Dot-notation access: ContextGet("user.id") Full in-memory caching (per request) Lockable context (AliensGrade: production mode safety) Can be extended by App/Brand/SDK overrides ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Context.py](PyOS/Py.26.00.00/Engine/PyOS.Context.py)
- **Class**: `PyOS_Context`
- **Facade Entry Points**:
  - `PyOS.Context()` → `public static function Main(array $Payload = [], array $Context = []): mixed`
  - `PyOS.ContextGet()` → `public static function Get(array $Payload = [], array $Context = []): mixed`
  - `PyOS.ContextSet()` → `public static function Set(array $Payload = [], array $Context = []): bool`
  - `PyOS.ContextHas()` → `public static function Has(array $Payload = [], array $Context = []): bool`
  - `PyOS.ContextPick()` → `public static function Pick(array $Payload = [], array $Context = []): array`
  - `PyOS.ContextScope()` → `public static function Scope(array $Payload = [], array $Context = []): mixed`
  - `PyOS.ContextSnapshot()` → `public static function Snapshot(array $Payload = [], array $Context = []): array`
  - `PyOS.ContextRestore()` → `public static function Restore(array $Payload = [], array $Context = []): bool`
  - `PyOS.ContextLock()` → `public static function Lock(): void`
  - `PyOS.ContextUnlock()` → `public static function Unlock(): void`
  - `PyOS.ContextContextGet()` → `public static function ContextGet(string $Key): mixed`
  - `PyOS.ContextContextSet()` → `public static function ContextSet(array $Data): bool`
  - `PyOS.ContextContextHas()` → `public static function ContextHas(string $Key): bool`
  - `PyOS.ContextContextPick()` → `public static function ContextPick(array $Keys): array`
  - `PyOS.ContextContextSnapshot()` → `public static function ContextSnapshot(): array`
  - `PyOS.ContextContextRestore()` → `public static function ContextRestore(array $Tree): bool`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = []): mixed` | `PyOS.Context()` |
| Get | `public static function Get(array $Payload = [], array $Context = []): mixed` | `PyOS.ContextGet()` |
| Set | `public static function Set(array $Payload = [], array $Context = []): bool` | `PyOS.ContextSet()` |
| Has | `public static function Has(array $Payload = [], array $Context = []): bool` | `PyOS.ContextHas()` |
| Pick | `public static function Pick(array $Payload = [], array $Context = []): array` | `PyOS.ContextPick()` |
| Scope | `public static function Scope(array $Payload = [], array $Context = []): mixed` | `PyOS.ContextScope()` |
| Snapshot | `public static function Snapshot(array $Payload = [], array $Context = []): array` | `PyOS.ContextSnapshot()` |
| Restore | `public static function Restore(array $Payload = [], array $Context = []): bool` | `PyOS.ContextRestore()` |
| Lock | `public static function Lock(): void` | `PyOS.ContextLock()` |
| Unlock | `public static function Unlock(): void` | `PyOS.ContextUnlock()` |
| ContextGet | `public static function ContextGet(string $Key): mixed` | `PyOS.ContextContextGet()` |
| ContextSet | `public static function ContextSet(array $Data): bool` | `PyOS.ContextContextSet()` |
| ContextHas | `public static function ContextHas(string $Key): bool` | `PyOS.ContextContextHas()` |
| ContextPick | `public static function ContextPick(array $Keys): array` | `PyOS.ContextContextPick()` |
| ContextSnapshot | `public static function ContextSnapshot(): array` | `PyOS.ContextContextSnapshot()` |
| ContextRestore | `public static function ContextRestore(array $Tree): bool` | `PyOS.ContextContextRestore()` |
### <span id="PyOS-core"></span>PyOS.Core
- **Purpose**: PyOS ka "Brain" / Central Kernel Multi-App + Multi-Tenant core registry Request Context (User, App, Brand, Tenant, Request) System Context (Version, Path, Env, Execution) Config Loader (PyOS + App + Brand + Env) Memory Store (super-fast per request) Engine Registry (Cache, Database, HTTP...) Hooks (Before/After/Custom events) Trace System (Aliens Insight)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Core.py](PyOS/Py.26.00.00/Engine/PyOS.Core.py)
- **Class**: `PyOS_Core`
- **Facade Entry Points**:
  - `PyOS.CoreInitSystem()` → `public static function InitSystem(): void`
  - `PyOS.CoreInitGlobals()` → `public static function InitGlobals(): void`
  - `PyOS.CoreSet()` → `public static function Set(string $Key, mixed $Val): void`
  - `PyOS.CoreGet()` → `public static function Get(string $Key, mixed $Default = null): mixed`
  - `PyOS.CoreSetContext()` → `public static function SetContext(string $Key, mixed $Val): void`
  - `PyOS.CoreContext()` → `public static function Context(string $Key, mixed $Default = null): mixed`
  - `PyOS.CoreApp()` → `public static function App(string $Key=null, mixed $Default=null): mixed`
  - `PyOS.CoreBrand()` → `public static function Brand(string $Key=null, mixed $Default=null): mixed`
  - `PyOS.CoreUser()` → `public static function User(string $Key=null, mixed $Default=null): mixed`
  - `PyOS.CoreTenant()` → `public static function Tenant(string $Key=null, mixed $Default=null): mixed`
  - `PyOS.CoreRequest()` → `public static function Request(string $Key=null, mixed $Default=null): mixed`
  - `PyOS.CoreSystem()` → `public static function System(string $Key=null, mixed $Default=null): mixed`
  - `PyOS.CoreMemSet()` → `public static function MemSet(string $Key, mixed $Val): void`
  - `PyOS.CoreMemGet()` → `public static function MemGet(string $Key, mixed $Default=null): mixed`
  - `PyOS.CoreSetConfig()` → `public static function SetConfig(string $Key, mixed $Val): void`
  - `PyOS.CoreConfig()` → `public static function Config(string $Key, mixed $Default=null): mixed`
  - `PyOS.CoreEngine()` → `public static function Engine(string $Name): ?string`
  - `PyOS.CoreRegisterEngine()` → `public static function RegisterEngine(string $Name, string $Class): void`
  - `PyOS.CoreEngines()` → `public static function Engines(): array`
  - `PyOS.CoreTrace()` → `public static function Trace(string $Msg): void`
  - `PyOS.CoreGetTrace()` → `public static function GetTrace(): array`
  - `PyOS.CoreHookAdd()` → `public static function HookAdd(string $Event, callable $Fn): void`
  - `PyOS.CoreHookRun()` → `public static function HookRun(string $Event, mixed &$Payload=null): void`

| Method | Signature | Facade |
| --- | --- | --- |
| InitSystem | `public static function InitSystem(): void` | `PyOS.CoreInitSystem()` |
| InitGlobals | `public static function InitGlobals(): void` | `PyOS.CoreInitGlobals()` |
| Set | `public static function Set(string $Key, mixed $Val): void` | `PyOS.CoreSet()` |
| Get | `public static function Get(string $Key, mixed $Default = null): mixed` | `PyOS.CoreGet()` |
| SetContext | `public static function SetContext(string $Key, mixed $Val): void` | `PyOS.CoreSetContext()` |
| Context | `public static function Context(string $Key, mixed $Default = null): mixed` | `PyOS.CoreContext()` |
| App | `public static function App(string $Key=null, mixed $Default=null): mixed` | `PyOS.CoreApp()` |
| Brand | `public static function Brand(string $Key=null, mixed $Default=null): mixed` | `PyOS.CoreBrand()` |
| User | `public static function User(string $Key=null, mixed $Default=null): mixed` | `PyOS.CoreUser()` |
| Tenant | `public static function Tenant(string $Key=null, mixed $Default=null): mixed` | `PyOS.CoreTenant()` |
| Request | `public static function Request(string $Key=null, mixed $Default=null): mixed` | `PyOS.CoreRequest()` |
| System | `public static function System(string $Key=null, mixed $Default=null): mixed` | `PyOS.CoreSystem()` |
| MemSet | `public static function MemSet(string $Key, mixed $Val): void` | `PyOS.CoreMemSet()` |
| MemGet | `public static function MemGet(string $Key, mixed $Default=null): mixed` | `PyOS.CoreMemGet()` |
| SetConfig | `public static function SetConfig(string $Key, mixed $Val): void` | `PyOS.CoreSetConfig()` |
| Config | `public static function Config(string $Key, mixed $Default=null): mixed` | `PyOS.CoreConfig()` |
| Engine | `public static function Engine(string $Name): ?string` | `PyOS.CoreEngine()` |
| RegisterEngine | `public static function RegisterEngine(string $Name, string $Class): void` | `PyOS.CoreRegisterEngine()` |
| Engines | `public static function Engines(): array` | `PyOS.CoreEngines()` |
| Trace | `public static function Trace(string $Msg): void` | `PyOS.CoreTrace()` |
| GetTrace | `public static function GetTrace(): array` | `PyOS.CoreGetTrace()` |
| HookAdd | `public static function HookAdd(string $Event, callable $Fn): void` | `PyOS.CoreHookAdd()` |
| HookRun | `public static function HookRun(string $Event, mixed &$Payload=null): void` | `PyOS.CoreHookRun()` |
### <span id="PyOS-course"></span>PyOS.Course
- **Purpose**: Enterprise-grade Course backend engine (Multi-App + Multi-Tenant) Clean, scoped CRUD + enrollment + progress Loader-only usage via PyOS::CourseAction()
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Course.py](PyOS/Py.26.00.00/Engine/PyOS.Course.py)
- **Class**: `PyOS_Course`
- **Facade Entry Points**:
  - `PyOS.Course()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.CourseNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `PyOS.CourseGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.CourseList()` → `public static function List(array $Payload = [], array $Context = [])`
  - `PyOS.CourseUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `PyOS.CourseStatus()` → `public static function Status(array $Payload = [], array $Context = [])`
  - `PyOS.CourseDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `PyOS.CourseEnroll()` → `public static function Enroll(array $Payload = [], array $Context = [])`
  - `PyOS.CourseProgressGet()` → `public static function ProgressGet(array $Payload = [], array $Context = [])`
  - `PyOS.CourseProgressSet()` → `public static function ProgressSet(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Course()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `PyOS.CourseNew()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.CourseGet()` |
| List | `public static function List(array $Payload = [], array $Context = [])` | `PyOS.CourseList()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `PyOS.CourseUpdate()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `PyOS.CourseStatus()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `PyOS.CourseDelete()` |
| Enroll | `public static function Enroll(array $Payload = [], array $Context = [])` | `PyOS.CourseEnroll()` |
| ProgressGet | `public static function ProgressGet(array $Payload = [], array $Context = [])` | `PyOS.CourseProgressGet()` |
| ProgressSet | `public static function ProgressSet(array $Payload = [], array $Context = [])` | `PyOS.CourseProgressSet()` |
### <span id="PyOS-cron"></span>PyOS.Cron
- **Purpose**: Enterprise-grade Cron / Scheduler Engine for PyOS Multi-App + Multi-Tenant + Cluster-Safe job orchestration Central brain for all scheduled/background jobs
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Cron.py](PyOS/Py.26.00.00/Engine/PyOS.Cron.py)
- **Class**: `PyOS_Cron`
- **Facade Entry Points**:
  - `PyOS.Cron()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.CronTick()` → `public static function Tick(array $Payload = [], array $Context = [])`
  - `PyOS.CronEnqueue()` → `public static function Enqueue(array $Payload = [], array $Context = [])`
  - `PyOS.CronClaim()` → `public static function Claim(array $Payload = [], array $Context = [])`
  - `PyOS.CronRun()` → `public static function Run(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Cron()` |
| Tick | `public static function Tick(array $Payload = [], array $Context = [])` | `PyOS.CronTick()` |
| Enqueue | `public static function Enqueue(array $Payload = [], array $Context = [])` | `PyOS.CronEnqueue()` |
| Claim | `public static function Claim(array $Payload = [], array $Context = [])` | `PyOS.CronClaim()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `PyOS.CronRun()` |
### <span id="PyOS-crypto"></span>PyOS.Crypto
- **Purpose**: Enterprise-grade Crypto Engine for PyOS Multi-App, Multi-Tenant, Chain-Agnostic crypto layer Wallet, Balance, Transfer, Ledger (Phase-1)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Crypto.py](PyOS/Py.26.00.00/Engine/PyOS.Crypto.py)
- **Class**: `PyOS_Crypto`
- **Facade Entry Points**:
  - `PyOS.Crypto()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.CryptoWalletNew()` → `public static function WalletNew(array $Payload, array $Context)`
  - `PyOS.CryptoBalanceGet()` → `public static function BalanceGet(array $Payload, array $Context)`
  - `PyOS.CryptoTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `PyOS.CryptoLedgerList()` → `public static function LedgerList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Crypto()` |
| WalletNew | `public static function WalletNew(array $Payload, array $Context)` | `PyOS.CryptoWalletNew()` |
| BalanceGet | `public static function BalanceGet(array $Payload, array $Context)` | `PyOS.CryptoBalanceGet()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `PyOS.CryptoTransfer()` |
| LedgerList | `public static function LedgerList(array $Payload, array $Context)` | `PyOS.CryptoLedgerList()` |
### <span id="PyOS-csrf"></span>PyOS.CSRF
- **Purpose**: Enterprise-grade CSRF protection for browser + session(cookie) requests Multi-App + Multi-Tenant isolation by scope (Tenant/App/Brand/User/Session) Supports Header + Body token modes (cookie mode optional) Optional Origin/Referer validation Optional Replay protection for high-risk routes (nonce store) ---------------------------------------------------------------------------- DESIGN RULES (PyOS.EngineRouter v7.0): Called only via PyOS::CSRF* static calls Main($Payload,$Context) is mandatory fallback No includes, no boot logic, no global
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.CSRF.py](PyOS/Py.26.00.00/Engine/PyOS.CSRF.py)
- **Class**: `instantiation`
- **Facade Entry Points**:
  - `PyOS.CSRF()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.CSRFPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `PyOS.CSRFGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.CSRFNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `PyOS.CSRFRotate()` → `public static function Rotate(array $Payload = [], array $Context = [])`
  - `PyOS.CSRFRequire()` → `public static function Require(array $Payload = [], array $Context = [])`
  - `PyOS.CSRFValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.CSRF()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `PyOS.CSRFPolicy()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.CSRFGet()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `PyOS.CSRFNew()` |
| Rotate | `public static function Rotate(array $Payload = [], array $Context = [])` | `PyOS.CSRFRotate()` |
| Require | `public static function Require(array $Payload = [], array $Context = [])` | `PyOS.CSRFRequire()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `PyOS.CSRFValidate()` |
### <span id="PyOS-currency"></span>PyOS.Currency
- **Purpose**: Enterprise-grade Currency + Money + FX conversion engine Multi-App + Multi-Tenant defaults, policies, and allowed currencies High-precision decimal safety (no float math), rounding policies Fast catalog/config/rate caching with scoped keys Audit-ready outputs + deterministic error contracts
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Currency.py](PyOS/Py.26.00.00/Engine/PyOS.Currency.py)
- **Class**: `PyOS_Currency`
- **Facade Entry Points**:
  - `PyOS.Currency()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.CurrencyList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.CurrencyGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.CurrencyValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.CurrencyNormalize()` → `public static function Normalize(array $Payload, array $Context)`
  - `PyOS.CurrencyFormat()` → `public static function Format(array $Payload, array $Context)`
  - `PyOS.CurrencyDefault()` → `public static function Default(array $Payload, array $Context)`
  - `PyOS.CurrencyAllowed()` → `public static function Allowed(array $Payload, array $Context)`
  - `PyOS.CurrencyRounding()` → `public static function Rounding(array $Payload, array $Context)`
  - `PyOS.CurrencySpread()` → `public static function Spread(array $Payload, array $Context)`
  - `PyOS.CurrencyRateGet()` → `public static function RateGet(array $Payload, array $Context)`
  - `PyOS.CurrencyConvert()` → `public static function Convert(array $Payload, array $Context)`
  - `PyOS.CurrencyRateSet()` → `public static function RateSet(array $Payload, array $Context)`
  - `PyOS.CurrencyRateRefresh()` → `public static function RateRefresh(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Currency()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.CurrencyList()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.CurrencyGet()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.CurrencyValidate()` |
| Normalize | `public static function Normalize(array $Payload, array $Context)` | `PyOS.CurrencyNormalize()` |
| Format | `public static function Format(array $Payload, array $Context)` | `PyOS.CurrencyFormat()` |
| Default | `public static function Default(array $Payload, array $Context)` | `PyOS.CurrencyDefault()` |
| Allowed | `public static function Allowed(array $Payload, array $Context)` | `PyOS.CurrencyAllowed()` |
| Rounding | `public static function Rounding(array $Payload, array $Context)` | `PyOS.CurrencyRounding()` |
| Spread | `public static function Spread(array $Payload, array $Context)` | `PyOS.CurrencySpread()` |
| RateGet | `public static function RateGet(array $Payload, array $Context)` | `PyOS.CurrencyRateGet()` |
| Convert | `public static function Convert(array $Payload, array $Context)` | `PyOS.CurrencyConvert()` |
| RateSet | `public static function RateSet(array $Payload, array $Context)` | `PyOS.CurrencyRateSet()` |
| RateRefresh | `public static function RateRefresh(array $Payload, array $Context)` | `PyOS.CurrencyRateRefresh()` |
### <span id="PyOS-database"></span>PyOS.Database
- **Purpose**: Central Database Operating Layer for PyOS Multi-App + Multi-Tenant + High-Performance DB Engine Read/Write split, Transactions, Safe Queries Powers AliensCRUD & all data engines
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Database.py](PyOS/Py.26.00.00/Engine/PyOS.Database.py)
- **Class**: `PyOS_Database`
- **Facade Entry Points**:
  - `PyOS.Database()` → `public static function Main($Payload, $Context)`
  - `PyOS.DatabaseRow()` → `public static function Row($Payload, $Context)`
  - `PyOS.DatabaseRows()` → `public static function Rows($Payload, $Context)`
  - `PyOS.DatabaseExec()` → `public static function Exec($Payload, $Context)`
  - `PyOS.DatabaseBegin()` → `public static function Begin($Payload, $Context)`
  - `PyOS.DatabaseCommit()` → `public static function Commit($Payload, $Context)`
  - `PyOS.DatabaseRollback()` → `public static function Rollback($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.Database()` |
| Row | `public static function Row($Payload, $Context)` | `PyOS.DatabaseRow()` |
| Rows | `public static function Rows($Payload, $Context)` | `PyOS.DatabaseRows()` |
| Exec | `public static function Exec($Payload, $Context)` | `PyOS.DatabaseExec()` |
| Begin | `public static function Begin($Payload, $Context)` | `PyOS.DatabaseBegin()` |
| Commit | `public static function Commit($Payload, $Context)` | `PyOS.DatabaseCommit()` |
| Rollback | `public static function Rollback($Payload, $Context)` | `PyOS.DatabaseRollback()` |
### <span id="PyOS-datapolicy"></span>PyOS.DataPolicy
- **Purpose**: OS-level Data Governance & Lifecycle Engine Retention, Archival, Purge, Legal Hold, Masking, Export Control Enforced across ALL units (API, Cron, Webhook, SDK) Multi-App + Multi-Tenant + Override aware
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.DataPolicy.py](PyOS/Py.26.00.00/Engine/PyOS.DataPolicy.py)
- **Class**: `PyOS_DataPolicy`
- **Facade Entry Points**:
  - `PyOS.DataPolicy()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.DataPolicyResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.DataPolicyAllow()` → `public static function Allow(array $Payload, array $Context)`
  - `PyOS.DataPolicyMask()` → `public static function Mask(array $Payload, array $Context)`
  - `PyOS.DataPolicyPurgePlan()` → `public static function PurgePlan(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.DataPolicy()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.DataPolicyResolve()` |
| Allow | `public static function Allow(array $Payload, array $Context)` | `PyOS.DataPolicyAllow()` |
| Mask | `public static function Mask(array $Payload, array $Context)` | `PyOS.DataPolicyMask()` |
| PurgePlan | `public static function PurgePlan(array $Payload, array $Context)` | `PyOS.DataPolicyPurgePlan()` |
### <span id="PyOS-debugger"></span>PyOS.Debugger
- **Purpose**: Enterprise-grade Debugging Engine for PyOS Safe production debugging (no data leaks) Multi-App + Multi-Tenant isolation Integrated with Log, Trace, Metric, Monitor Near-zero overhead when disabled
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Debugger.py](PyOS/Py.26.00.00/Engine/PyOS.Debugger.py)
- **Class**: `PyOS_Debugger`
- **Facade Entry Points**:
  - `PyOS.Debugger()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DebuggerMode()` → `public static function Mode(array $Payload, array $Context)`
  - `PyOS.DebuggerOn()` → `public static function On(array $Payload, array $Context)`
  - `PyOS.DebuggerOff()` → `public static function Off(array $Payload, array $Context)`
  - `PyOS.DebuggerLog()` → `public static function Log(array $Payload, array $Context)`
  - `PyOS.DebuggerStart()` → `public static function Start(array $Payload, array $Context)`
  - `PyOS.DebuggerEnd()` → `public static function End(array $Payload, array $Context)`
  - `PyOS.DebuggerSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Debugger()` |
| Mode | `public static function Mode(array $Payload, array $Context)` | `PyOS.DebuggerMode()` |
| On | `public static function On(array $Payload, array $Context)` | `PyOS.DebuggerOn()` |
| Off | `public static function Off(array $Payload, array $Context)` | `PyOS.DebuggerOff()` |
| Log | `public static function Log(array $Payload, array $Context)` | `PyOS.DebuggerLog()` |
| Start | `public static function Start(array $Payload, array $Context)` | `PyOS.DebuggerStart()` |
| End | `public static function End(array $Payload, array $Context)` | `PyOS.DebuggerEnd()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `PyOS.DebuggerSnapshot()` |
### <span id="PyOS-debuggerai"></span>PyOS.DebuggerAI
- **Purpose**: System-level autonomous debugging & incident intelligence engine Central error, anomaly, trace and incident brain for PyOS Safe, scoped, auditable debugging for Multi-App & Multi-Tenant systems
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.DebuggerAI.py](PyOS/Py.26.00.00/Engine/PyOS.DebuggerAI.py)
- **Class**: `PyOS_DebuggerAI`
- **Facade Entry Points**:
  - `PyOS.DebuggerAI()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAITraceStart()` → `public static function TraceStart(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAITraceEnd()` → `public static function TraceEnd(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAIErrorIngest()` → `public static function ErrorIngest(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAIAnomalyDetect()` → `public static function AnomalyDetect(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAIExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAIFixPlan()` → `public static function FixPlan(array $Payload = [], array $Context = [])`
  - `PyOS.DebuggerAIFixApply()` → `public static function FixApply(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAI()` |
| TraceStart | `public static function TraceStart(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAITraceStart()` |
| TraceEnd | `public static function TraceEnd(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAITraceEnd()` |
| ErrorIngest | `public static function ErrorIngest(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAIErrorIngest()` |
| AnomalyDetect | `public static function AnomalyDetect(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAIAnomalyDetect()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAIExplain()` |
| FixPlan | `public static function FixPlan(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAIFixPlan()` |
| FixApply | `public static function FixApply(array $Payload = [], array $Context = [])` | `PyOS.DebuggerAIFixApply()` |
### <span id="PyOS-decision"></span>PyOS.Decision
- **Purpose**: Central deterministic decision engine for PyOS Access / Policy / Routing / Scoring decisions Multi-App, Multi-Brand, Multi-Tenant compatible Explainable, auditable, cache-aware decisions
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Decision.py](PyOS/Py.26.00.00/Engine/PyOS.Decision.py)
- **Class**: `PyOS_Decision`
- **Facade Entry Points**:
  - `PyOS.Decision()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DecisionAllow()` → `public static function Allow(array $Payload, array $Context)`
  - `PyOS.DecisionRoute()` → `public static function Route(array $Payload, array $Context)`
  - `PyOS.DecisionScore()` → `public static function Score(array $Payload, array $Context)`
  - `PyOS.DecisionPolicy()` → `public static function Policy(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Decision()` |
| Allow | `public static function Allow(array $Payload, array $Context)` | `PyOS.DecisionAllow()` |
| Route | `public static function Route(array $Payload, array $Context)` | `PyOS.DecisionRoute()` |
| Score | `public static function Score(array $Payload, array $Context)` | `PyOS.DecisionScore()` |
| Policy | `public static function Policy(array $Payload, array $Context)` | `PyOS.DecisionPolicy()` |
### <span id="PyOS-department"></span>PyOS.Department
- **Purpose**: Enterprise-grade Department engine (Multi-Tenant + Multi-App) Department hierarchy (Parent/Path/Depth), move, tree Members mapping, role-ready, policy-ready Soft delete (status=9), deny-by-default
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Department.py](PyOS/Py.26.00.00/Engine/PyOS.Department.py)
- **Class**: `PyOS_Department`
- **Facade Entry Points**:
  - `PyOS.Department()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DepartmentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.DepartmentGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.DepartmentList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.DepartmentUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.DepartmentStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.DepartmentTree()` → `public static function Tree(array $Payload, array $Context)`
  - `PyOS.DepartmentMove()` → `public static function Move(array $Payload, array $Context)`
  - `PyOS.DepartmentMemberAdd()` → `public static function MemberAdd(array $Payload, array $Context)`
  - `PyOS.DepartmentMemberRemove()` → `public static function MemberRemove(array $Payload, array $Context)`
  - `PyOS.DepartmentMemberList()` → `public static function MemberList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Department()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.DepartmentNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.DepartmentGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.DepartmentList()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.DepartmentUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.DepartmentStatus()` |
| Tree | `public static function Tree(array $Payload, array $Context)` | `PyOS.DepartmentTree()` |
| Move | `public static function Move(array $Payload, array $Context)` | `PyOS.DepartmentMove()` |
| MemberAdd | `public static function MemberAdd(array $Payload, array $Context)` | `PyOS.DepartmentMemberAdd()` |
| MemberRemove | `public static function MemberRemove(array $Payload, array $Context)` | `PyOS.DepartmentMemberRemove()` |
| MemberList | `public static function MemberList(array $Payload, array $Context)` | `PyOS.DepartmentMemberList()` |
### <span id="PyOS-deployment"></span>PyOS.Deployment
- **Purpose**: Enterprise-grade deployment orchestration engine Multi-App + Multi-Brand + Multi-Tenant deployments Zero-downtime, rollback-first philosophy OS-style deployment (not framework-style)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Deployment.py](PyOS/Py.26.00.00/Engine/PyOS.Deployment.py)
- **Class**: `PyOS_Deployment`
- **Facade Entry Points**:
  - `PyOS.Deployment()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DeploymentPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.DeploymentRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.DeploymentRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `PyOS.DeploymentStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Deployment()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.DeploymentPlan()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.DeploymentRun()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.DeploymentRollback()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.DeploymentStatus()` |
### <span id="PyOS-deposit"></span>PyOS.Deposit
- **Purpose**: Enterprise-grade Deposit engine (Multi-App + Multi-Tenant) Deposit Intent → Provider Init → Webhook/Confirm → Ledger Posting Idempotency + Locking + Audit + Trace friendly Works for any backend: ecommerce, SaaS, wallet, fintech, crypto, education
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Deposit.py](PyOS/Py.26.00.00/Engine/PyOS.Deposit.py)
- **Class**: `PyOS_Deposit`
- **Facade Entry Points**:
  - `PyOS.Deposit()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DepositNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.DepositInit()` → `public static function Init(array $Payload, array $Context)`
  - `PyOS.DepositGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.DepositList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.DepositCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.DepositWebhook()` → `public static function Webhook(array $Payload, array $Context)`
  - `PyOS.DepositConfirm()` → `public static function Confirm(array $Payload, array $Context)`
  - `PyOS.DepositReconcile()` → `public static function Reconcile(array $Payload, array $Context)`
  - `PyOS.DepositAdminAdjust()` → `public static function AdminAdjust(array $Payload, array $Context)`
  - `PyOS.DepositExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Deposit()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.DepositNew()` |
| Init | `public static function Init(array $Payload, array $Context)` | `PyOS.DepositInit()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.DepositGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.DepositList()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.DepositCancel()` |
| Webhook | `public static function Webhook(array $Payload, array $Context)` | `PyOS.DepositWebhook()` |
| Confirm | `public static function Confirm(array $Payload, array $Context)` | `PyOS.DepositConfirm()` |
| Reconcile | `public static function Reconcile(array $Payload, array $Context)` | `PyOS.DepositReconcile()` |
| AdminAdjust | `public static function AdminAdjust(array $Payload, array $Context)` | `PyOS.DepositAdminAdjust()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.DepositExport()` |
### <span id="PyOS-device"></span>PyOS.Device
- **Purpose**: Enterprise-grade Device Identity + Trust + Risk Layer for PyOS Multi-App + Multi-Tenant device registry Session binding support (anti-hijack baseline) Blocklist support (device/fingerprint) High-performance request-level cache + optional persistence
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Device.py](PyOS/Py.26.00.00/Engine/PyOS.Device.py)
- **Class**: `PyOS_Device`
- **Facade Entry Points**:
  - `PyOS.Device()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DeviceIdentify()` → `public static function Identify(array $Payload, array $Context)`
  - `PyOS.DeviceGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.DeviceGetByFingerprint()` → `public static function GetByFingerprint(array $Payload, array $Context)`
  - `PyOS.DeviceTrust()` → `public static function Trust(array $Payload, array $Context)`
  - `PyOS.DeviceBindSession()` → `public static function BindSession(array $Payload, array $Context)`
  - `PyOS.DeviceValidateSession()` → `public static function ValidateSession(array $Payload, array $Context)`
  - `PyOS.DeviceEvent()` → `public static function Event(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Device()` |
| Identify | `public static function Identify(array $Payload, array $Context)` | `PyOS.DeviceIdentify()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.DeviceGet()` |
| GetByFingerprint | `public static function GetByFingerprint(array $Payload, array $Context)` | `PyOS.DeviceGetByFingerprint()` |
| Trust | `public static function Trust(array $Payload, array $Context)` | `PyOS.DeviceTrust()` |
| BindSession | `public static function BindSession(array $Payload, array $Context)` | `PyOS.DeviceBindSession()` |
| ValidateSession | `public static function ValidateSession(array $Payload, array $Context)` | `PyOS.DeviceValidateSession()` |
| Event | `public static function Event(array $Payload, array $Context)` | `PyOS.DeviceEvent()` |
### <span id="PyOS-digitaltwin"></span>PyOS.DigitalTwin
- **Purpose**: Runtime Digital Twin for PyOS (System / App / Tenant) Snapshot, Diff, Replay, Simulation, Forecast Safe experimentation + predictive intelligence
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.DigitalTwin.py](PyOS/Py.26.00.00/Engine/PyOS.DigitalTwin.py)
- **Class**: `PyOS_DigitalTwin`
- **Facade Entry Points**:
  - `PyOS.DigitalTwin()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DigitalTwinSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`
  - `PyOS.DigitalTwinGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.DigitalTwinDiff()` → `public static function Diff(array $Payload, array $Context)`
  - `PyOS.DigitalTwinReplay()` → `public static function Replay(array $Payload, array $Context)`
  - `PyOS.DigitalTwinSimulate()` → `public static function Simulate(array $Payload, array $Context)`
  - `PyOS.DigitalTwinForecast()` → `public static function Forecast(array $Payload, array $Context)`
  - `PyOS.DigitalTwinExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.DigitalTwin()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `PyOS.DigitalTwinSnapshot()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.DigitalTwinGet()` |
| Diff | `public static function Diff(array $Payload, array $Context)` | `PyOS.DigitalTwinDiff()` |
| Replay | `public static function Replay(array $Payload, array $Context)` | `PyOS.DigitalTwinReplay()` |
| Simulate | `public static function Simulate(array $Payload, array $Context)` | `PyOS.DigitalTwinSimulate()` |
| Forecast | `public static function Forecast(array $Payload, array $Context)` | `PyOS.DigitalTwinForecast()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.DigitalTwinExport()` |
### <span id="PyOS-doc"></span>PyOS.Doc
- **Purpose**: Make PyOS a self-documenting backend Operating System Auto-generate documentation for Engines, Units, Modules Multi-App + Multi-Brand + Multi-Tenant aware AI-readable + Human-readable documentation Enterprise-grade compliance, validation & versioning
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Doc.py](PyOS/Py.26.00.00/Engine/PyOS.Doc.py)
- **Class**: `PyOS_Doc`
- **Facade Entry Points**:
  - `PyOS.Doc()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.DocBuild()` → `public static function Build(array $Payload, array $Context)`
  - `PyOS.DocServe()` → `public static function Serve(array $Payload, array $Context)`
  - `PyOS.DocSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.DocPage()` → `public static function Page(array $Payload, array $Context)`
  - `PyOS.DocValidate()` → `public static function Validate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Doc()` |
| Build | `public static function Build(array $Payload, array $Context)` | `PyOS.DocBuild()` |
| Serve | `public static function Serve(array $Payload, array $Context)` | `PyOS.DocServe()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.DocSearch()` |
| Page | `public static function Page(array $Payload, array $Context)` | `PyOS.DocPage()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.DocValidate()` |
### <span id="PyOS-doctor"></span>PyOS.Doctor
- **Purpose**: Enterprise Grade Doctor Domain Engine (Multi-App + Multi-Tenant) Doctor Identity, Verification, Clinic Linking, Schedule, Availability High-Performance Read Paths + Safe Cache Scoping
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Doctor.py](PyOS/Py.26.00.00/Engine/PyOS.Doctor.py)
- **Class**: `PyOS_Doctor`
- **Facade Entry Points**:
  - `PyOS.Doctor()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DoctorNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.DoctorGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.DoctorUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.DoctorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.DoctorList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.DoctorSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.DoctorVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.DoctorScheduleSet()` → `public static function ScheduleSet(array $Payload, array $Context)`
  - `PyOS.DoctorScheduleGet()` → `public static function ScheduleGet(array $Payload, array $Context)`
  - `PyOS.DoctorAvailability()` → `public static function Availability(array $Payload, array $Context)`
  - `PyOS.DoctorStats()` → `public static function Stats(array $Payload, array $Context)`
  - `PyOS.DoctorExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Doctor()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.DoctorNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.DoctorGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.DoctorUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.DoctorStatus()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.DoctorList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.DoctorSearch()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.DoctorVerify()` |
| ScheduleSet | `public static function ScheduleSet(array $Payload, array $Context)` | `PyOS.DoctorScheduleSet()` |
| ScheduleGet | `public static function ScheduleGet(array $Payload, array $Context)` | `PyOS.DoctorScheduleGet()` |
| Availability | `public static function Availability(array $Payload, array $Context)` | `PyOS.DoctorAvailability()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `PyOS.DoctorStats()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.DoctorExport()` |
### <span id="PyOS-download"></span>PyOS.Download
- **Purpose**: Secure, high-performance, enterprise-grade download engine Multi-App + Multi-Tenant safe delivery Supports private/public/signed downloads
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Download.py](PyOS/Py.26.00.00/Engine/PyOS.Download.py)
- **Class**: `PyOS_Download`
- **Facade Entry Points**:
  - `PyOS.Download()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.DownloadGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.DownloadLink()` → `public static function Link(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Download()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.DownloadGet()` |
| Link | `public static function Link(array $Payload, array $Context)` | `PyOS.DownloadLink()` |
### <span id="PyOS-educationai"></span>PyOS.EducationAI
- **Purpose**: Enterprise Grade AI Engine for Education Systems Powering LMS, Tutors, Exams, Content Factory Multi-App + Multi-Tenant + High Performance
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.EducationAI.py](PyOS/Py.26.00.00/Engine/PyOS.EducationAI.py)
- **Class**: `PyOS_EducationAI`
- **Facade Entry Points**:
  - `PyOS.EducationAI()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EducationAIConfigGet()` → `public static function ConfigGet(array $Payload, array $Context)`
  - `PyOS.EducationAIPolicyCheck()` → `public static function PolicyCheck(array $Payload, array $Context)`
  - `PyOS.EducationAITutorStart()` → `public static function TutorStart(array $Payload, array $Context)`
  - `PyOS.EducationAITutorAsk()` → `public static function TutorAsk(array $Payload, array $Context)`
  - `PyOS.EducationAILessonGenerate()` → `public static function LessonGenerate(array $Payload, array $Context)`
  - `PyOS.EducationAILessonGenerateWorker()` → `public static function LessonGenerateWorker(array $Payload, array $Context)`
  - `PyOS.EducationAIEvaluateQuiz()` → `public static function EvaluateQuiz(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.EducationAI()` |
| ConfigGet | `public static function ConfigGet(array $Payload, array $Context)` | `PyOS.EducationAIConfigGet()` |
| PolicyCheck | `public static function PolicyCheck(array $Payload, array $Context)` | `PyOS.EducationAIPolicyCheck()` |
| TutorStart | `public static function TutorStart(array $Payload, array $Context)` | `PyOS.EducationAITutorStart()` |
| TutorAsk | `public static function TutorAsk(array $Payload, array $Context)` | `PyOS.EducationAITutorAsk()` |
| LessonGenerate | `public static function LessonGenerate(array $Payload, array $Context)` | `PyOS.EducationAILessonGenerate()` |
| LessonGenerateWorker | `public static function LessonGenerateWorker(array $Payload, array $Context)` | `PyOS.EducationAILessonGenerateWorker()` |
| EvaluateQuiz | `public static function EvaluateQuiz(array $Payload, array $Context)` | `PyOS.EducationAIEvaluateQuiz()` |
### <span id="PyOS-email"></span>PyOS.Email
- **Purpose**: Enterprise-grade Email Engine for PyOS Multi-App, Multi-Brand, Multi-Tenant High-performance async email orchestration Template + Provider abstraction Queue-ready, audit-ready, secure by default
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Email.py](PyOS/Py.26.00.00/Engine/PyOS.Email.py)
- **Class**: `PyOS_Email`
- **Facade Entry Points**:
  - `PyOS.Email()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EmailSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.EmailBatch()` → `public static function Batch(array $Payload, array $Context)`
  - `PyOS.EmailTemplateRender()` → `public static function TemplateRender(array $Payload, array $Context)`
  - `PyOS.EmailStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Email()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.EmailSend()` |
| Batch | `public static function Batch(array $Payload, array $Context)` | `PyOS.EmailBatch()` |
| TemplateRender | `public static function TemplateRender(array $Payload, array $Context)` | `PyOS.EmailTemplateRender()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.EmailStatus()` |
### <span id="PyOS-embedding"></span>PyOS.Embedding
- **Purpose**: Enterprise-grade Embedding orchestration engine Multi-App, Multi-Tenant, High-Performance Provider-agnostic (Azure/OpenAI/Local/Future) Works as Semantic Intelligence Kernel for PyOS
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Embedding.py](PyOS/Py.26.00.00/Engine/PyOS.Embedding.py)
- **Class**: `PyOS_Embedding`
- **Facade Entry Points**:
  - `PyOS.Embedding()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EmbeddingUpsert()` → `public static function Upsert(array $Payload, array $Context)`
  - `PyOS.EmbeddingSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.EmbeddingDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.EmbeddingHealth()` → `public static function Health(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Embedding()` |
| Upsert | `public static function Upsert(array $Payload, array $Context)` | `PyOS.EmbeddingUpsert()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.EmbeddingSearch()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.EmbeddingDelete()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.EmbeddingHealth()` |
### <span id="PyOS-emergency"></span>PyOS.Emergency
- **Purpose**: Enterprise-grade Incident / Emergency OS Layer for PyOS Multi-App + Multi-Tenant safe incident lifecycle: New → Get → Update → Escalate → Action → Close → Audit SafeMode orchestration (Normal/SafeL1/SafeL2/SafeL3/Lockdown) Deterministic, deny-by-default, audit-first, hook-compatible
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Emergency.py](PyOS/Py.26.00.00/Engine/PyOS.Emergency.py)
- **Class**: `PyOS_Emergency`
- **Facade Entry Points**:
  - `PyOS.Emergency()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyEscalate()` → `public static function Escalate(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyClose()` → `public static function Close(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyList()` → `public static function List(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyAction()` → `public static function Action(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyStatus()` → `public static function Status(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencySafeModeEnable()` → `public static function SafeModeEnable(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencySafeModeDisable()` → `public static function SafeModeDisable(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyKillSwitchSet()` → `public static function KillSwitchSet(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyTrafficShed()` → `public static function TrafficShed(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyFailover()` → `public static function Failover(array $Payload = [], array $Context = [])`
  - `PyOS.EmergencyNotify()` → `public static function Notify(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Emergency()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `PyOS.EmergencyNew()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.EmergencyGet()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `PyOS.EmergencyUpdate()` |
| Escalate | `public static function Escalate(array $Payload = [], array $Context = [])` | `PyOS.EmergencyEscalate()` |
| Close | `public static function Close(array $Payload = [], array $Context = [])` | `PyOS.EmergencyClose()` |
| List | `public static function List(array $Payload = [], array $Context = [])` | `PyOS.EmergencyList()` |
| Action | `public static function Action(array $Payload = [], array $Context = [])` | `PyOS.EmergencyAction()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `PyOS.EmergencyStatus()` |
| SafeModeEnable | `public static function SafeModeEnable(array $Payload = [], array $Context = [])` | `PyOS.EmergencySafeModeEnable()` |
| SafeModeDisable | `public static function SafeModeDisable(array $Payload = [], array $Context = [])` | `PyOS.EmergencySafeModeDisable()` |
| KillSwitchSet | `public static function KillSwitchSet(array $Payload = [], array $Context = [])` | `PyOS.EmergencyKillSwitchSet()` |
| TrafficShed | `public static function TrafficShed(array $Payload = [], array $Context = [])` | `PyOS.EmergencyTrafficShed()` |
| Failover | `public static function Failover(array $Payload = [], array $Context = [])` | `PyOS.EmergencyFailover()` |
| Notify | `public static function Notify(array $Payload = [], array $Context = [])` | `PyOS.EmergencyNotify()` |
### <span id="PyOS-employee"></span>PyOS.Employee
- **Purpose**: Enterprise-grade Employee Engine Multi-Tenant + Multi-App Workforce Core Central employee abstraction for HR, Payroll, Attendance, Projects
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Employee.py](PyOS/Py.26.00.00/Engine/PyOS.Employee.py)
- **Class**: `PyOS_Employee`
- **Facade Entry Points**:
  - `PyOS.Employee()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EmployeeNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.EmployeeGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.EmployeeUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.EmployeeStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.EmployeeList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.EmployeeTransfer()` → `public static function Transfer(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Employee()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.EmployeeNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.EmployeeGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.EmployeeUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.EmployeeStatus()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.EmployeeList()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `PyOS.EmployeeTransfer()` |
### <span id="PyOS-emulator"></span>PyOS.Emulator
- **Purpose**: Provide a virtualized execution layer for PyOS Units Emulate API / Cron / Webhook / Component execution safely Enable Sandbox, Replay, Shadow, Chaos and Perf modes Support Multi-App, Multi-Brand, Multi-Tenant isolation Act as PyOS "Virtual Backend Machine"
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Emulator.py](PyOS/Py.26.00.00/Engine/PyOS.Emulator.py)
- **Class**: `PyOS_Emulator`
- **Facade Entry Points**:
  - `PyOS.Emulator()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EmulatorRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.EmulatorApi()` → `public static function Api(array $Payload, array $Context)`
  - `PyOS.EmulatorCron()` → `public static function Cron(array $Payload, array $Context)`
  - `PyOS.EmulatorWebhook()` → `public static function Webhook(array $Payload, array $Context)`
  - `PyOS.EmulatorReplay()` → `public static function Replay(array $Payload, array $Context)`
  - `PyOS.EmulatorReport()` → `public static function Report(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Emulator()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.EmulatorRun()` |
| Api | `public static function Api(array $Payload, array $Context)` | `PyOS.EmulatorApi()` |
| Cron | `public static function Cron(array $Payload, array $Context)` | `PyOS.EmulatorCron()` |
| Webhook | `public static function Webhook(array $Payload, array $Context)` | `PyOS.EmulatorWebhook()` |
| Replay | `public static function Replay(array $Payload, array $Context)` | `PyOS.EmulatorReplay()` |
| Report | `public static function Report(array $Payload, array $Context)` | `PyOS.EmulatorReport()` |
### <span id="PyOS-encryption"></span>PyOS.Encryption
- **Purpose**: Central cryptography engine for PyOS Encryption / Decryption (AEAD) Hashing & Password Hashing HMAC Sign / Verify Random / Nonce generation
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Encryption.py](PyOS/Py.26.00.00/Engine/PyOS.Encryption.py)
- **Class**: `PyOS_Encryption`
- **Facade Entry Points**:
  - `PyOS.Encryption()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EncryptionEncrypt()` → `public static function Encrypt(array $Payload, array $Context)`
  - `PyOS.EncryptionDecrypt()` → `public static function Decrypt(array $Payload, array $Context)`
  - `PyOS.EncryptionHash()` → `public static function Hash(array $Payload, array $Context)`
  - `PyOS.EncryptionPasswordHash()` → `public static function PasswordHash(array $Payload, array $Context)`
  - `PyOS.EncryptionPasswordVerify()` → `public static function PasswordVerify(array $Payload, array $Context)`
  - `PyOS.EncryptionSign()` → `public static function Sign(array $Payload, array $Context)`
  - `PyOS.EncryptionVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.EncryptionRandom()` → `public static function Random(array $Payload, array $Context)`
  - `PyOS.EncryptionNonce()` → `public static function Nonce(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Encryption()` |
| Encrypt | `public static function Encrypt(array $Payload, array $Context)` | `PyOS.EncryptionEncrypt()` |
| Decrypt | `public static function Decrypt(array $Payload, array $Context)` | `PyOS.EncryptionDecrypt()` |
| Hash | `public static function Hash(array $Payload, array $Context)` | `PyOS.EncryptionHash()` |
| PasswordHash | `public static function PasswordHash(array $Payload, array $Context)` | `PyOS.EncryptionPasswordHash()` |
| PasswordVerify | `public static function PasswordVerify(array $Payload, array $Context)` | `PyOS.EncryptionPasswordVerify()` |
| Sign | `public static function Sign(array $Payload, array $Context)` | `PyOS.EncryptionSign()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.EncryptionVerify()` |
| Random | `public static function Random(array $Payload, array $Context)` | `PyOS.EncryptionRandom()` |
| Nonce | `public static function Nonce(array $Payload, array $Context)` | `PyOS.EncryptionNonce()` |
### <span id="PyOS-env"></span>PyOS.Env
- **Purpose**: Purpose not documented in source.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Env.py](PyOS/Py.26.00.00/Engine/PyOS.Env.py)
- **Class**: Not declared
- **Facade Entry Points**: None documented
### <span id="PyOS-environment"></span>PyOS.Environment
- **Purpose**: Enterprise-grade Environment Control Plane for PyOS (Multi-App + Multi-Tenant) Single source of truth: Env Profile (perf + security + ops + log + flags) Deterministic precedence + safe fallbacks (production-safe by default) Designed to be called ONLY via PyOS.Loader + PyOS.EngineRouter (Static Facade)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Environment.py](PyOS/Py.26.00.00/Engine/PyOS.Environment.py)
- **Class**: `PyOS_Environment`
- **Facade Entry Points**:
  - `PyOS.Environment()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentName()` → `public static function Name(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentIs()` → `public static function Is(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentMode()` → `public static function Mode(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentApply()` → `public static function Apply(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentKey()` → `public static function Key(array $Payload = [], array $Context = [])`
  - `PyOS.EnvironmentHash()` → `public static function Hash(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Environment()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentGet()` |
| Name | `public static function Name(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentName()` |
| Is | `public static function Is(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentIs()` |
| Mode | `public static function Mode(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentMode()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentPolicy()` |
| Apply | `public static function Apply(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentApply()` |
| Key | `public static function Key(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentKey()` |
| Hash | `public static function Hash(array $Payload = [], array $Context = [])` | `PyOS.EnvironmentHash()` |
### <span id="PyOS-eventbus"></span>PyOS.EventBus
- **Purpose**: Central nervous system for PyOS Durable, multi-tenant, multi-app event orchestration High-performance publish / subscribe / consume
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.EventBus.py](PyOS/Py.26.00.00/Engine/PyOS.EventBus.py)
- **Class**: `PyOS_EventBus`
- **Facade Entry Points**:
  - `PyOS.EventBus()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.EventBusPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `PyOS.EventBusPublishBatch()` → `public static function PublishBatch(array $Payload, array $Context)`
  - `PyOS.EventBusConsume()` → `public static function Consume(array $Payload, array $Context)`
  - `PyOS.EventBusAck()` → `public static function Ack(array $Payload, array $Context)`
  - `PyOS.EventBusNack()` → `public static function Nack(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.EventBus()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.EventBusPublish()` |
| PublishBatch | `public static function PublishBatch(array $Payload, array $Context)` | `PyOS.EventBusPublishBatch()` |
| Consume | `public static function Consume(array $Payload, array $Context)` | `PyOS.EventBusConsume()` |
| Ack | `public static function Ack(array $Payload, array $Context)` | `PyOS.EventBusAck()` |
| Nack | `public static function Nack(array $Payload, array $Context)` | `PyOS.EventBusNack()` |
### <span id="PyOS-exam"></span>PyOS.Exam
- **Purpose**: Central Exam Engine for PyOS Works for LMS, HR, Certification, Medical, Compliance exams OS-level exam lifecycle management Highlights     : Loader + EngineRouter compatible Multi-App + Multi-Tenant safe State-driven exam lifecycle Audit, Cache, Lock, Event enabled Dependencies   : PyOS.Security PyOS.Auth PyOS.Role PyOS.Permission PyOS.Database (AliensCRUD) PyOS.Cache PyOS.Lock PyOS.Audit PyOS.Event =============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Exam.py](PyOS/Py.26.00.00/Engine/PyOS.Exam.py)
- **Class**: `PyOS_Exam`
- **Facade Entry Points**:
  - `PyOS.Exam()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ExamNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ExamPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `PyOS.ExamAttemptStart()` → `public static function AttemptStart(array $Payload, array $Context)`
  - `PyOS.ExamSubmit()` → `public static function Submit(array $Payload, array $Context)`
  - `PyOS.ExamResultGet()` → `public static function ResultGet(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Exam()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ExamNew()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.ExamPublish()` |
| AttemptStart | `public static function AttemptStart(array $Payload, array $Context)` | `PyOS.ExamAttemptStart()` |
| Submit | `public static function Submit(array $Payload, array $Context)` | `PyOS.ExamSubmit()` |
| ResultGet | `public static function ResultGet(array $Payload, array $Context)` | `PyOS.ExamResultGet()` |
### <span id="PyOS-executor"></span>PyOS.Executor
- **Purpose**: Central execution engine for all Units (API/Cron/Webhook/Asset/Component/Template) Boot files remain orchestration-only (no functions/
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Executor.py](PyOS/Py.26.00.00/Engine/PyOS.Executor.py)
- **Class**: `PyOS_Executor`
- **Facade Entry Points**:
  - `PyOS.Executor()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorApi()` → `public static function Api(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorApis()` → `public static function Apis(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorCron()` → `public static function Cron(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorWebhook()` → `public static function Webhook(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorAsset()` → `public static function Asset(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorComponent()` → `public static function Component(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorTemplate()` → `public static function Template(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorUnit()` → `public static function Unit(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `PyOS.ExecutorWrap()` → `public static function Wrap(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Executor()` |
| Api | `public static function Api(array $Payload = [], array $Context = [])` | `PyOS.ExecutorApi()` |
| Apis | `public static function Apis(array $Payload = [], array $Context = [])` | `PyOS.ExecutorApis()` |
| Cron | `public static function Cron(array $Payload = [], array $Context = [])` | `PyOS.ExecutorCron()` |
| Webhook | `public static function Webhook(array $Payload = [], array $Context = [])` | `PyOS.ExecutorWebhook()` |
| Asset | `public static function Asset(array $Payload = [], array $Context = [])` | `PyOS.ExecutorAsset()` |
| Component | `public static function Component(array $Payload = [], array $Context = [])` | `PyOS.ExecutorComponent()` |
| Template | `public static function Template(array $Payload = [], array $Context = [])` | `PyOS.ExecutorTemplate()` |
| Unit | `public static function Unit(array $Payload = [], array $Context = [])` | `PyOS.ExecutorUnit()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `PyOS.ExecutorPolicy()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `PyOS.ExecutorRun()` |
| Wrap | `public static function Wrap(array $Payload = [], array $Context = [])` | `PyOS.ExecutorWrap()` |
### <span id="PyOS-experiment"></span>PyOS.Experiment
- **Purpose**: Enterprise-grade Experimentation Engine for PyOS A/B Tests, Canary Releases, Progressive Rollouts Multi-App + Multi-Tenant safe by design Cache-first, ultra-fast runtime decisions Audit-ready, rollback-capable
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Experiment.py](PyOS/Py.26.00.00/Engine/PyOS.Experiment.py)
- **Class**: `PyOS_Experiment`
- **Facade Entry Points**:
  - `PyOS.Experiment()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ExperimentNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ExperimentGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ExperimentStart()` → `public static function Start(array $Payload, array $Context)`
  - `PyOS.ExperimentDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `PyOS.ExperimentTrack()` → `public static function Track(array $Payload, array $Context)`
  - `PyOS.ExperimentStop()` → `public static function Stop(array $Payload, array $Context)`
  - `PyOS.ExperimentKill()` → `public static function Kill(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Experiment()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ExperimentNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ExperimentGet()` |
| Start | `public static function Start(array $Payload, array $Context)` | `PyOS.ExperimentStart()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `PyOS.ExperimentDecide()` |
| Track | `public static function Track(array $Payload, array $Context)` | `PyOS.ExperimentTrack()` |
| Stop | `public static function Stop(array $Payload, array $Context)` | `PyOS.ExperimentStop()` |
| Kill | `public static function Kill(array $Payload, array $Context)` | `PyOS.ExperimentKill()` |
### <span id="PyOS-failover"></span>PyOS.Failover
- **Purpose**: Central failover decision & state engine for PyOS Policy-driven, multi-scope failover orchestration Supports Global / App / Tenant / Dependency scopes
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Failover.py](PyOS/Py.26.00.00/Engine/PyOS.Failover.py)
- **Class**: `PyOS_Failover`
- **Facade Entry Points**:
  - `PyOS.Failover()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FailoverDetect()` → `public static function Detect(array $Payload, array $Context)`
  - `PyOS.FailoverDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `PyOS.FailoverSwitch()` → `public static function Switch(array $Payload, array $Context)`
  - `PyOS.FailoverRecover()` → `public static function Recover(array $Payload, array $Context)`
  - `PyOS.FailoverStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Failover()` |
| Detect | `public static function Detect(array $Payload, array $Context)` | `PyOS.FailoverDetect()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `PyOS.FailoverDecide()` |
| Switch | `public static function Switch(array $Payload, array $Context)` | `PyOS.FailoverSwitch()` |
| Recover | `public static function Recover(array $Payload, array $Context)` | `PyOS.FailoverRecover()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.FailoverStatus()` |
### <span id="PyOS-feature"></span>PyOS.Feature
- **Purpose**: Enterprise-grade Feature Flag & Toggle Engine Multi-App, Multi-Tenant, Role/User scoped Deterministic rollout, variant & kill-switch support
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Feature.py](PyOS/Py.26.00.00/Engine/PyOS.Feature.py)
- **Class**: `PyOS_Feature`
- **Facade Entry Points**:
  - `PyOS.Feature()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FeatureIsEnabled()` → `public static function IsEnabled(array $Payload, array $Context)`
  - `PyOS.FeatureVariant()` → `public static function Variant(array $Payload, array $Context)`
  - `PyOS.FeatureConfig()` → `public static function Config(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Feature()` |
| IsEnabled | `public static function IsEnabled(array $Payload, array $Context)` | `PyOS.FeatureIsEnabled()` |
| Variant | `public static function Variant(array $Payload, array $Context)` | `PyOS.FeatureVariant()` |
| Config | `public static function Config(array $Payload, array $Context)` | `PyOS.FeatureConfig()` |
### <span id="PyOS-featureflag"></span>PyOS.FeatureFlag
- **Purpose**: OS-level Feature Flag system for PyOS Runtime feature enable/disable without deployment Multi-App + Multi-Tenant + Role/User aware Deterministic rollout (hash / percent / schedule) Ultra-fast evaluation with cache-first strategy
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.FeatureFlag.py](PyOS/Py.26.00.00/Engine/PyOS.FeatureFlag.py)
- **Class**: `PyOS_FeatureFlag`
- **Facade Entry Points**:
  - `PyOS.FeatureFlag()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FeatureFlagIsEnabled()` → `public static function IsEnabled(array $Payload, array $Context)`
  - `PyOS.FeatureFlagEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.FeatureFlag()` |
| IsEnabled | `public static function IsEnabled(array $Payload, array $Context)` | `PyOS.FeatureFlagIsEnabled()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `PyOS.FeatureFlagEvaluate()` |
### <span id="PyOS-feed"></span>PyOS.Feed
- **Purpose**: Universal Feed / Activity / Timeline Engine Works for Social, Enterprise, LMS, E-Commerce, System Feeds Multi-App + Multi-Tenant + High-Performance Push / Pull / Hybrid ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Feed.py](PyOS/Py.26.00.00/Engine/PyOS.Feed.py)
- **Class**: `PyOS_Feed`
- **Facade Entry Points**:
  - `PyOS.Feed()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FeedFeedPublish()` → `public static function FeedPublish(array $Payload, array $Context)`
  - `PyOS.FeedFeedGet()` → `public static function FeedGet(array $Payload, array $Context)`
  - `PyOS.FeedFeedCount()` → `public static function FeedCount(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Feed()` |
| FeedPublish | `public static function FeedPublish(array $Payload, array $Context)` | `PyOS.FeedFeedPublish()` |
| FeedGet | `public static function FeedGet(array $Payload, array $Context)` | `PyOS.FeedFeedGet()` |
| FeedCount | `public static function FeedCount(array $Payload, array $Context)` | `PyOS.FeedFeedCount()` |
### <span id="PyOS-filter"></span>PyOS.Filter
- **Purpose**: Universal, secure, high-performance filter engine One DSL → DB / Analytics / API ready Policy-driven, tenant-safe, cache-aware filtering
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Filter.py](PyOS/Py.26.00.00/Engine/PyOS.Filter.py)
- **Class**: `PyOS_Filter`
- **Facade Entry Points**:
  - `PyOS.Filter()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FilterParse()` → `public static function Parse(array $Payload, array $Context)`
  - `PyOS.FilterApply()` → `public static function Apply(array $Plan, array $Context)`
  - `PyOS.FilterExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Filter()` |
| Parse | `public static function Parse(array $Payload, array $Context)` | `PyOS.FilterParse()` |
| Apply | `public static function Apply(array $Plan, array $Context)` | `PyOS.FilterApply()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.FilterExplain()` |
### <span id="PyOS-firewall"></span>PyOS.Firewall
- **Purpose**: Central request firewall for PyOS Entry-layer protection for API / Webhook / Asset / Cron / Component Multi-App + Multi-Tenant aware High-performance, cache-driven, deny-by-default
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Firewall.py](PyOS/Py.26.00.00/Engine/PyOS.Firewall.py)
- **Class**: `PyOS_Firewall`
- **Facade Entry Points**:
  - `PyOS.Firewall()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FirewallCheck()` → `public static function Check(array $Payload, array $Context)`
  - `PyOS.FirewallEnforce()` → `public static function Enforce(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Firewall()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.FirewallCheck()` |
| Enforce | `public static function Enforce(array $Payload, array $Context)` | `PyOS.FirewallEnforce()` |
### <span id="PyOS-formula"></span>PyOS.Formula
- **Purpose**: Enterprise-grade Formula Engine for PyOS Backend OS Safe formula evaluation without eval() Multi-App + Multi-Tenant + Multi-Brand scoping via Context Compile → Cache → Eval (High-performance) Deterministic results, explain mode, dependency-ready design
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Formula.py](PyOS/Py.26.00.00/Engine/PyOS.Formula.py)
- **Class**: `PyOS_Formula`
- **Facade Entry Points**:
  - `PyOS.Formula()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FormulaEval()` → `public static function Eval(array $Payload, array $Context)`
  - `PyOS.FormulaValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.FormulaCompile()` → `public static function Compile(array $Payload, array $Context)`
  - `PyOS.FormulaExplain()` → `public static function Explain(array $Payload, array $Context)`
  - `PyOS.FormulaRegister()` → `public static function Register(array $Payload, array $Context)`
  - `PyOS.FormulaActivate()` → `public static function Activate(array $Payload, array $Context)`
  - `PyOS.FormulaRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `PyOS.FormulaGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.FormulaList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.FormulaDependency()` → `public static function Dependency(array $Payload, array $Context)`
  - `PyOS.FormulaBenchmark()` → `public static function Benchmark(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Formula()` |
| Eval | `public static function Eval(array $Payload, array $Context)` | `PyOS.FormulaEval()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.FormulaValidate()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `PyOS.FormulaCompile()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.FormulaExplain()` |
| Register | `public static function Register(array $Payload, array $Context)` | `PyOS.FormulaRegister()` |
| Activate | `public static function Activate(array $Payload, array $Context)` | `PyOS.FormulaActivate()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.FormulaRollback()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.FormulaGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.FormulaList()` |
| Dependency | `public static function Dependency(array $Payload, array $Context)` | `PyOS.FormulaDependency()` |
| Benchmark | `public static function Benchmark(array $Payload, array $Context)` | `PyOS.FormulaBenchmark()` |
### <span id="PyOS-friend"></span>PyOS.Friend
- **Purpose**: Enterprise-grade Friend / Relationship Engine Multi-Tenant + Multi-App compatible Symmetric friendship graph (A↔B) Privacy, abuse-safe, audit-ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Friend.py](PyOS/Py.26.00.00/Engine/PyOS.Friend.py)
- **Class**: `PyOS_Friend`
- **Facade Entry Points**:
  - `PyOS.Friend()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.FriendAdd()` → `public static function Add(array $Payload, array $Context)`
  - `PyOS.FriendAccept()` → `public static function Accept(array $Payload, array $Context)`
  - `PyOS.FriendRemove()` → `public static function Remove(array $Payload, array $Context)`
  - `PyOS.FriendStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Friend()` |
| Add | `public static function Add(array $Payload, array $Context)` | `PyOS.FriendAdd()` |
| Accept | `public static function Accept(array $Payload, array $Context)` | `PyOS.FriendAccept()` |
| Remove | `public static function Remove(array $Payload, array $Context)` | `PyOS.FriendRemove()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.FriendStatus()` |
### <span id="PyOS-generator"></span>PyOS.Generator
- **Purpose**: Enterprise-grade code, unit, app, module generator Backend factory for PyOS (OS-level capability) Deterministic, auditable, multi-layer aware generation
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Generator.py](PyOS/Py.26.00.00/Engine/PyOS.Generator.py)
- **Class**: `PyOS_Generator`
- **Facade Entry Points**:
  - `PyOS.Generator()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.GeneratorPreview()` → `public static function Preview(array $Payload, array $Context)`
  - `PyOS.GeneratorEngine()` → `public static function Engine(array $Payload, array $Context)`
  - `PyOS.GeneratorUnit()` → `public static function Unit(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Generator()` |
| Preview | `public static function Preview(array $Payload, array $Context)` | `PyOS.GeneratorPreview()` |
| Engine | `public static function Engine(array $Payload, array $Context)` | `PyOS.GeneratorEngine()` |
| Unit | `public static function Unit(array $Payload, array $Context)` | `PyOS.GeneratorUnit()` |
### <span id="PyOS-governor"></span>PyOS.Governor
- **Purpose**: Global control & protection layer for PyOS. Acts as: Resource Governor Policy Enforcer Quota & Limit Controller Circuit Breaker Gate Multi-Tenant Safety Layer
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Governor.py](PyOS/Py.26.00.00/Engine/PyOS.Governor.py)
- **Class**: `PyOS_Governor`
- **Facade Entry Points**:
  - `PyOS.Governor()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.GovernorAllow()` → `public static function Allow(array $Payload, array $Context)`
  - `PyOS.GovernorEnforce()` → `public static function Enforce(array $Payload, array $Context)`
  - `PyOS.GovernorSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Governor()` |
| Allow | `public static function Allow(array $Payload, array $Context)` | `PyOS.GovernorAllow()` |
| Enforce | `public static function Enforce(array $Payload, array $Context)` | `PyOS.GovernorEnforce()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `PyOS.GovernorSnapshot()` |
### <span id="PyOS-health"></span>PyOS.Health
- **Purpose**: Enterprise Health System for PyOS (Multi-App + Multi-Tenant) Liveness / Readiness probes for Load Balancers & Orchestrators Quick/Standard/Deep health reports with strict performance rules Dependency-aware probe execution with caching & safe fallbacks ---------------------------------------------------------------------------- AliensGrade Highlights: Loader/Router compatible (PyOS::HealthX routed by EngineRouter) Context-driven (no globals; uses $Context user/app/brand/ecosystem) Deny heavy work by default (Quick path is ultra-fast) Sanitized public output + admin-only deep details Caching with scope keys to prevent cross-tenant/app mixing ---------------------------------------------------------------------------- Dependencies (Soft): PyOS.Cache (CacheGet/CacheSet)  [Recommended] PyOS.Monitor (MonitorMain/Start/End)  [Optional] PyOS.Metric (MetricMain)  [Optional] PyOS.Trace  (TraceMain)   [Optional] PyOS.Log    (LogMain)     [Optional] PyOS.Database (DatabasePing/DatabaseMain) [Optional] PyOS.Queue / PyOS.Worker (QueueMain/WorkerMain) [Optional] PyOS.Security / PyOS.RateLimit (SecurityMain/RateLimitMain) [Optional] ---------------------------------------------------------------------------- Public API (via PyOS Loader): PyOS::Health()         -> Quick summary (safe for public) PyOS::HealthLive()     -> Liveness probe (process alive) PyOS::HealthReady()    -> Readiness probe (can accept traffic) PyOS::HealthScore()    -> Score only PyOS::HealthReport()   -> Standard report (sanitized) PyOS::HealthDeep()     -> Deep report (admin-only recommended) PyOS::HealthProbeList()-> Available probe names PyOS::HealthProbeRun() -> Run a single probe (admin-only recommended) ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Health.py](PyOS/Py.26.00.00/Engine/PyOS.Health.py)
- **Class**: `PyOS_Health`
- **Facade Entry Points**:
  - `PyOS.Health()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.HealthLive()` → `public static function Live(array $Payload = [], array $Context = [])`
  - `PyOS.HealthReady()` → `public static function Ready(array $Payload = [], array $Context = [])`
  - `PyOS.HealthScore()` → `public static function Score(array $Payload = [], array $Context = [])`
  - `PyOS.HealthReport()` → `public static function Report(array $Payload = [], array $Context = [])`
  - `PyOS.HealthDeep()` → `public static function Deep(array $Payload = [], array $Context = [])`
  - `PyOS.HealthProbeList()` → `public static function ProbeList(array $Payload = [], array $Context = [])`
  - `PyOS.HealthProbeRun()` → `public static function ProbeRun(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Health()` |
| Live | `public static function Live(array $Payload = [], array $Context = [])` | `PyOS.HealthLive()` |
| Ready | `public static function Ready(array $Payload = [], array $Context = [])` | `PyOS.HealthReady()` |
| Score | `public static function Score(array $Payload = [], array $Context = [])` | `PyOS.HealthScore()` |
| Report | `public static function Report(array $Payload = [], array $Context = [])` | `PyOS.HealthReport()` |
| Deep | `public static function Deep(array $Payload = [], array $Context = [])` | `PyOS.HealthDeep()` |
| ProbeList | `public static function ProbeList(array $Payload = [], array $Context = [])` | `PyOS.HealthProbeList()` |
| ProbeRun | `public static function ProbeRun(array $Payload = [], array $Context = [])` | `PyOS.HealthProbeRun()` |
### <span id="PyOS-healthmonitor"></span>PyOS.HealthMonitor
- **Purpose**: System, App, Tenant & Unit health monitoring Liveness, Readiness & Deep Health checks Metric ingestion with ultra-low overhead SLO breach detection & degrade signalling Acts as "Self-Awareness Kernel" of PyOS
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.HealthMonitor.py](PyOS/Py.26.00.00/Engine/PyOS.HealthMonitor.py)
- **Class**: `PyOS_HealthMonitor`
- **Facade Entry Points**:
  - `PyOS.HealthMonitor()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.HealthMonitorPing()` → `public static function Ping(array $Payload, array $Context)`
  - `PyOS.HealthMonitorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.HealthMonitorMetric()` → `public static function Metric(array $Payload, array $Context)`
  - `PyOS.HealthMonitorCheck()` → `public static function Check(array $Payload, array $Context)`
  - `PyOS.HealthMonitorSlo()` → `public static function Slo(array $Payload, array $Context)`
  - `PyOS.HealthMonitorDegrade()` → `public static function Degrade(array $Payload, array $Context)`
  - `PyOS.HealthMonitorReport()` → `public static function Report(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.HealthMonitor()` |
| Ping | `public static function Ping(array $Payload, array $Context)` | `PyOS.HealthMonitorPing()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.HealthMonitorStatus()` |
| Metric | `public static function Metric(array $Payload, array $Context)` | `PyOS.HealthMonitorMetric()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.HealthMonitorCheck()` |
| Slo | `public static function Slo(array $Payload, array $Context)` | `PyOS.HealthMonitorSlo()` |
| Degrade | `public static function Degrade(array $Payload, array $Context)` | `PyOS.HealthMonitorDegrade()` |
| Report | `public static function Report(array $Payload, array $Context)` | `PyOS.HealthMonitorReport()` |
### <span id="PyOS-http"></span>PyOS.HTTP
- **Purpose**: Enterprise-grade HTTP Client Engine for PyOS Secure, observable, multi-tenant outbound HTTP layer SSRF-safe by default Retry, timeout, cache, trace, log support
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.HTTP.py](PyOS/Py.26.00.00/Engine/PyOS.HTTP.py)
- **Class**: `PyOS_HTTP`
- **Facade Entry Points**:
  - `PyOS.HTTP()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.HTTPGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.HTTPPost()` → `public static function Post(array $Payload, array $Context)`
  - `PyOS.HTTPPut()` → `public static function Put(array $Payload, array $Context)`
  - `PyOS.HTTPPatch()` → `public static function Patch(array $Payload, array $Context)`
  - `PyOS.HTTPDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.HTTPHead()` → `public static function Head(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.HTTP()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.HTTPGet()` |
| Post | `public static function Post(array $Payload, array $Context)` | `PyOS.HTTPPost()` |
| Put | `public static function Put(array $Payload, array $Context)` | `PyOS.HTTPPut()` |
| Patch | `public static function Patch(array $Payload, array $Context)` | `PyOS.HTTPPatch()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.HTTPDelete()` |
| Head | `public static function Head(array $Payload, array $Context)` | `PyOS.HTTPHead()` |
### <span id="PyOS-identity"></span>PyOS.Identity
- **Purpose**: Enterprise-grade Identity Fabric for PyOS (Multi-App + Multi-Tenant) Login / Register / Session / Token / OTP orchestration via PyOS Engines Centralized policy: Auth, Role, Permission, Device Trust (future-ready) Safe caching + scoped keys to prevent data leak across users/roles/tenants
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Identity.py](PyOS/Py.26.00.00/Engine/PyOS.Identity.py)
- **Class**: `PyOS_Identity`
- **Facade Entry Points**:
  - `PyOS.Identity()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityScope()` → `public static function Scope(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityRegister()` → `public static function Register(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityLogin()` → `public static function Login(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityLogout()` → `public static function Logout(array $Payload = [], array $Context = [])`
  - `PyOS.IdentitySessionGet()` → `public static function SessionGet(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityTokenIssue()` → `public static function TokenIssue(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityTokenVerify()` → `public static function TokenVerify(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityOtpSend()` → `public static function OtpSend(array $Payload = [], array $Context = [])`
  - `PyOS.IdentityOtpVerify()` → `public static function OtpVerify(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Identity()` |
| Scope | `public static function Scope(array $Payload = [], array $Context = [])` | `PyOS.IdentityScope()` |
| Register | `public static function Register(array $Payload = [], array $Context = [])` | `PyOS.IdentityRegister()` |
| Login | `public static function Login(array $Payload = [], array $Context = [])` | `PyOS.IdentityLogin()` |
| Logout | `public static function Logout(array $Payload = [], array $Context = [])` | `PyOS.IdentityLogout()` |
| SessionGet | `public static function SessionGet(array $Payload = [], array $Context = [])` | `PyOS.IdentitySessionGet()` |
| TokenIssue | `public static function TokenIssue(array $Payload = [], array $Context = [])` | `PyOS.IdentityTokenIssue()` |
| TokenVerify | `public static function TokenVerify(array $Payload = [], array $Context = [])` | `PyOS.IdentityTokenVerify()` |
| OtpSend | `public static function OtpSend(array $Payload = [], array $Context = [])` | `PyOS.IdentityOtpSend()` |
| OtpVerify | `public static function OtpVerify(array $Payload = [], array $Context = [])` | `PyOS.IdentityOtpVerify()` |
### <span id="PyOS-index"></span>PyOS.Index
- **Purpose**: Central Index Intelligence Layer for PyOS Fast Unit Resolution (Exact + Wildcard) Cache Index & Scope Safety Index Registry, Health, and Audit
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Index.py](PyOS/Py.26.00.00/Engine/PyOS.Index.py)
- **Class**: `PyOS_Index`
- **Facade Entry Points**:
  - `PyOS.Index()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.IndexBuild()` → `public static function Build(array $Payload, array $Context)`
  - `PyOS.IndexRebuild()` → `public static function Rebuild(array $Payload, array $Context)`
  - `PyOS.IndexUnitResolveFast()` → `public static function UnitResolveFast(array $Payload, array $Context)`
  - `PyOS.IndexCachePurge()` → `public static function CachePurge(array $Payload, array $Context)`
  - `PyOS.IndexHealth()` → `public static function Health(array $Payload, array $Context)`
  - `PyOS.IndexStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Index()` |
| Build | `public static function Build(array $Payload, array $Context)` | `PyOS.IndexBuild()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `PyOS.IndexRebuild()` |
| UnitResolveFast | `public static function UnitResolveFast(array $Payload, array $Context)` | `PyOS.IndexUnitResolveFast()` |
| CachePurge | `public static function CachePurge(array $Payload, array $Context)` | `PyOS.IndexCachePurge()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.IndexHealth()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `PyOS.IndexStats()` |
### <span id="PyOS-indexer"></span>PyOS.Indexer
- **Purpose**: Universal indexing engine for all data types (Post, Page, Product, User…) Multi-App + Multi-Tenant safe indexing Incremental, idempotent, audit-ready indexing Search-backend agnostic (DB / External / Hybrid)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Indexer.py](PyOS/Py.26.00.00/Engine/PyOS.Indexer.py)
- **Class**: `PyOS_Indexer`
- **Facade Entry Points**:
  - `PyOS.Indexer()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.IndexerIndex()` → `public static function Index(array $Payload, array $Context)`
  - `PyOS.IndexerDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.IndexerRebuild()` → `public static function Rebuild(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Indexer()` |
| Index | `public static function Index(array $Payload, array $Context)` | `PyOS.IndexerIndex()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.IndexerDelete()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `PyOS.IndexerRebuild()` |
### <span id="PyOS-insurance"></span>PyOS.Insurance
- **Purpose**: Universal Insurance Engine for Multi-App + Multi-Tenant PyOS Covers: Policies, Plans, Claims, Payouts, Audit, Documents, Integrations Designed as "Coverage Contract OS Layer" (not just insurance module)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Insurance.py](PyOS/Py.26.00.00/Engine/PyOS.Insurance.py)
- **Class**: `PyOS_Insurance`
- **Facade Entry Points**:
  - `PyOS.Insurance()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.InsuranceProviderNew()` → `public static function ProviderNew(array $Payload, array $Context)`
  - `PyOS.InsuranceProductNew()` → `public static function ProductNew(array $Payload, array $Context)`
  - `PyOS.InsurancePlanNew()` → `public static function PlanNew(array $Payload, array $Context)`
  - `PyOS.InsurancePlanGet()` → `public static function PlanGet(array $Payload, array $Context)`
  - `PyOS.InsurancePolicyQuote()` → `public static function PolicyQuote(array $Payload, array $Context)`
  - `PyOS.InsurancePolicyIssue()` → `public static function PolicyIssue(array $Payload, array $Context)`
  - `PyOS.InsurancePolicyCancel()` → `public static function PolicyCancel(array $Payload, array $Context)`
  - `PyOS.InsuranceClaimNew()` → `public static function ClaimNew(array $Payload, array $Context)`
  - `PyOS.InsuranceClaimSubmit()` → `public static function ClaimSubmit(array $Payload, array $Context)`
  - `PyOS.InsuranceClaimApprove()` → `public static function ClaimApprove(array $Payload, array $Context)`
  - `PyOS.InsuranceClaimReject()` → `public static function ClaimReject(array $Payload, array $Context)`
  - `PyOS.InsuranceClaimPayout()` → `public static function ClaimPayout(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Insurance()` |
| ProviderNew | `public static function ProviderNew(array $Payload, array $Context)` | `PyOS.InsuranceProviderNew()` |
| ProductNew | `public static function ProductNew(array $Payload, array $Context)` | `PyOS.InsuranceProductNew()` |
| PlanNew | `public static function PlanNew(array $Payload, array $Context)` | `PyOS.InsurancePlanNew()` |
| PlanGet | `public static function PlanGet(array $Payload, array $Context)` | `PyOS.InsurancePlanGet()` |
| PolicyQuote | `public static function PolicyQuote(array $Payload, array $Context)` | `PyOS.InsurancePolicyQuote()` |
| PolicyIssue | `public static function PolicyIssue(array $Payload, array $Context)` | `PyOS.InsurancePolicyIssue()` |
| PolicyCancel | `public static function PolicyCancel(array $Payload, array $Context)` | `PyOS.InsurancePolicyCancel()` |
| ClaimNew | `public static function ClaimNew(array $Payload, array $Context)` | `PyOS.InsuranceClaimNew()` |
| ClaimSubmit | `public static function ClaimSubmit(array $Payload, array $Context)` | `PyOS.InsuranceClaimSubmit()` |
| ClaimApprove | `public static function ClaimApprove(array $Payload, array $Context)` | `PyOS.InsuranceClaimApprove()` |
| ClaimReject | `public static function ClaimReject(array $Payload, array $Context)` | `PyOS.InsuranceClaimReject()` |
| ClaimPayout | `public static function ClaimPayout(array $Payload, array $Context)` | `PyOS.InsuranceClaimPayout()` |
### <span id="PyOS-inventory"></span>PyOS.Inventory
- **Purpose**: Enterprise-grade Inventory Operating Engine (Multi-App, Multi-Tenant) Ledger-first design (truth = immutable ledger; balance = projection) Oversell protection via reserve/commit lifecycle Idempotent write operations (safe retries / webhooks) Fast reads via materialized balance + optional cache
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Inventory.py](PyOS/Py.26.00.00/Engine/PyOS.Inventory.py)
- **Class**: `PyOS_Inventory`
- **Facade Entry Points**:
  - `PyOS.Inventory()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.InventoryGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.InventorySummary()` → `public static function Summary(array $Payload, array $Context)`
  - `PyOS.InventoryLedger()` → `public static function Ledger(array $Payload, array $Context)`
  - `PyOS.InventoryAdjust()` → `public static function Adjust(array $Payload, array $Context)`
  - `PyOS.InventoryReserve()` → `public static function Reserve(array $Payload, array $Context)`
  - `PyOS.InventoryRelease()` → `public static function Release(array $Payload, array $Context)`
  - `PyOS.InventoryCommit()` → `public static function Commit(array $Payload, array $Context)`
  - `PyOS.InventoryTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `PyOS.InventoryReceive()` → `public static function Receive(array $Payload, array $Context)`
  - `PyOS.InventoryReconcile()` → `public static function Reconcile(array $Payload, array $Context)`
  - `PyOS.InventoryHealth()` → `public static function Health(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Inventory()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.InventoryGet()` |
| Summary | `public static function Summary(array $Payload, array $Context)` | `PyOS.InventorySummary()` |
| Ledger | `public static function Ledger(array $Payload, array $Context)` | `PyOS.InventoryLedger()` |
| Adjust | `public static function Adjust(array $Payload, array $Context)` | `PyOS.InventoryAdjust()` |
| Reserve | `public static function Reserve(array $Payload, array $Context)` | `PyOS.InventoryReserve()` |
| Release | `public static function Release(array $Payload, array $Context)` | `PyOS.InventoryRelease()` |
| Commit | `public static function Commit(array $Payload, array $Context)` | `PyOS.InventoryCommit()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `PyOS.InventoryTransfer()` |
| Receive | `public static function Receive(array $Payload, array $Context)` | `PyOS.InventoryReceive()` |
| Reconcile | `public static function Reconcile(array $Payload, array $Context)` | `PyOS.InventoryReconcile()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.InventoryHealth()` |
### <span id="PyOS-invoice"></span>PyOS.Invoice
- **Purpose**: Enterprise-grade Invoicing Operating Layer for PyOS Multi-App + Multi-Tenant + High-Performance + Audit-Proof Strict Loader/Router compatibility (PyOS::InvoiceCreate(), etc.) ---------------------------------------------------------------------------- DESIGN RULES (AliensGrade): This is an Engine (no boot logic) All methods are static Main() is mandatory fallback Uses PyOS Engines for cross-system work (DB/Cache/Security/Lock/etc.) Deny-by-default, strict validation, idempotency, audit trail ----------------------------------------------------------------------------
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Invoice.py](PyOS/Py.26.00.00/Engine/PyOS.Invoice.py)
- **Class**: `PyOS_Invoice`
- **Facade Entry Points**:
  - `PyOS.Invoice()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.InvoiceCreate()` → `public static function Create(array $Payload, array $Context)`
  - `PyOS.InvoiceUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.InvoiceIssue()` → `public static function Issue(array $Payload, array $Context)`
  - `PyOS.InvoiceSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.InvoicePay()` → `public static function Pay(array $Payload, array $Context)`
  - `PyOS.InvoiceVoid()` → `public static function Void(array $Payload, array $Context)`
  - `PyOS.InvoiceView()` → `public static function View(array $Payload, array $Context)`
  - `PyOS.InvoiceList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.InvoiceSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.InvoicePdf()` → `public static function Pdf(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Invoice()` |
| Create | `public static function Create(array $Payload, array $Context)` | `PyOS.InvoiceCreate()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.InvoiceUpdate()` |
| Issue | `public static function Issue(array $Payload, array $Context)` | `PyOS.InvoiceIssue()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.InvoiceSend()` |
| Pay | `public static function Pay(array $Payload, array $Context)` | `PyOS.InvoicePay()` |
| Void | `public static function Void(array $Payload, array $Context)` | `PyOS.InvoiceVoid()` |
| View | `public static function View(array $Payload, array $Context)` | `PyOS.InvoiceView()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.InvoiceList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.InvoiceSearch()` |
| Pdf | `public static function Pdf(array $Payload, array $Context)` | `PyOS.InvoicePdf()` |
### <span id="PyOS-knowledge"></span>PyOS.Knowledge
- **Purpose**: Central Knowledge Engine for PyOS Multi-App, Multi-Tenant, Enterprise Knowledge Layer Facts, Documents, Entities, Relations, Graph & AI-ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Knowledge.py](PyOS/Py.26.00.00/Engine/PyOS.Knowledge.py)
- **Class**: `PyOS_Knowledge`
- **Facade Entry Points**:
  - `PyOS.Knowledge()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.KnowledgePut()` → `public static function Put(array $Payload, array $Context)`
  - `PyOS.KnowledgeGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.KnowledgeFind()` → `public static function Find(array $Payload, array $Context)`
  - `PyOS.KnowledgeRelate()` → `public static function Relate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Knowledge()` |
| Put | `public static function Put(array $Payload, array $Context)` | `PyOS.KnowledgePut()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.KnowledgeGet()` |
| Find | `public static function Find(array $Payload, array $Context)` | `PyOS.KnowledgeFind()` |
| Relate | `public static function Relate(array $Payload, array $Context)` | `PyOS.KnowledgeRelate()` |
### <span id="PyOS-knowledgegraph"></span>PyOS.KnowledgeGraph
- **Purpose**: Enterprise-grade Knowledge Graph Engine for PyOS Multi-App + Multi-Tenant semantic data layer Acts as intelligence backbone for Search, AI, Recommendation, Automation
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.KnowledgeGraph.py](PyOS/Py.26.00.00/Engine/PyOS.KnowledgeGraph.py)
- **Class**: `PyOS_KnowledgeGraph`
- **Facade Entry Points**:
  - `PyOS.KnowledgeGraph()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.KnowledgeGraphNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.KnowledgeGraphUpsert()` → `public static function Upsert(array $Payload, array $Context)`
  - `PyOS.KnowledgeGraphLink()` → `public static function Link(array $Payload, array $Context)`
  - `PyOS.KnowledgeGraphNeighbors()` → `public static function Neighbors(array $Payload, array $Context)`
  - `PyOS.KnowledgeGraphExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.KnowledgeGraph()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.KnowledgeGraphNew()` |
| Upsert | `public static function Upsert(array $Payload, array $Context)` | `PyOS.KnowledgeGraphUpsert()` |
| Link | `public static function Link(array $Payload, array $Context)` | `PyOS.KnowledgeGraphLink()` |
| Neighbors | `public static function Neighbors(array $Payload, array $Context)` | `PyOS.KnowledgeGraphNeighbors()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.KnowledgeGraphExplain()` |
### <span id="PyOS-labreport"></span>PyOS.LabReport
- **Purpose**: Enterprise-grade Lab Report lifecycle engine Multi-App + Multi-Tenant + High-Security medical data handling Works as core backend module for Hospital / Lab / Health platforms
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.LabReport.py](PyOS/Py.26.00.00/Engine/PyOS.LabReport.py)
- **Class**: `PyOS_LabReport`
- **Facade Entry Points**:
  - `PyOS.LabReport()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LabReportNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.LabReportGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.LabReportVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.LabReportPublish()` → `public static function Publish(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.LabReport()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.LabReportNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.LabReportGet()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.LabReportVerify()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.LabReportPublish()` |
### <span id="PyOS-learning"></span>PyOS.Learning
- **Purpose**: PyOS ka OS-level Learning Engine (Observe → Learn → Suggest/Act → Verify) Multi-App + Multi-Tenant compatible (Context based scoping) High-performance, async-first design (Queue/Worker friendly) Deterministic + Auditable learning (Audit + Model Registry + Versioning)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Learning.py](PyOS/Py.26.00.00/Engine/PyOS.Learning.py)
- **Class**: `PyOS_Learning`
- **Facade Entry Points**:
  - `PyOS.Learning()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.LearningIngest()` → `public static function Ingest(array $Payload = [], array $Context = [])`
  - `PyOS.LearningTrain()` → `public static function Train(array $Payload = [], array $Context = [])`
  - `PyOS.LearningPredict()` → `public static function Predict(array $Payload = [], array $Context = [])`
  - `PyOS.LearningPredictCompute()` → `public static function PredictCompute(array $Payload = [], array $Context = [])`
  - `PyOS.LearningRecommend()` → `public static function Recommend(array $Payload = [], array $Context = [])`
  - `PyOS.LearningRecommendCompute()` → `public static function RecommendCompute(array $Payload = [], array $Context = [])`
  - `PyOS.LearningAnomaly()` → `public static function Anomaly(array $Payload = [], array $Context = [])`
  - `PyOS.LearningOptimize()` → `public static function Optimize(array $Payload = [], array $Context = [])`
  - `PyOS.LearningPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `PyOS.LearningStatus()` → `public static function Status(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Learning()` |
| Ingest | `public static function Ingest(array $Payload = [], array $Context = [])` | `PyOS.LearningIngest()` |
| Train | `public static function Train(array $Payload = [], array $Context = [])` | `PyOS.LearningTrain()` |
| Predict | `public static function Predict(array $Payload = [], array $Context = [])` | `PyOS.LearningPredict()` |
| PredictCompute | `public static function PredictCompute(array $Payload = [], array $Context = [])` | `PyOS.LearningPredictCompute()` |
| Recommend | `public static function Recommend(array $Payload = [], array $Context = [])` | `PyOS.LearningRecommend()` |
| RecommendCompute | `public static function RecommendCompute(array $Payload = [], array $Context = [])` | `PyOS.LearningRecommendCompute()` |
| Anomaly | `public static function Anomaly(array $Payload = [], array $Context = [])` | `PyOS.LearningAnomaly()` |
| Optimize | `public static function Optimize(array $Payload = [], array $Context = [])` | `PyOS.LearningOptimize()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `PyOS.LearningPolicy()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `PyOS.LearningStatus()` |
### <span id="PyOS-leave"></span>PyOS.Leave
- **Purpose**: Enterprise-grade Leave Management Engine Multi-App, Multi-Tenant, Policy-driven Fully compatible with PyOS.Loader & EngineRouter
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Leave.py](PyOS/Py.26.00.00/Engine/PyOS.Leave.py)
- **Class**: `PyOS_Leave`
- **Facade Entry Points**:
  - `PyOS.Leave()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.LeaveApply()` → `public static function Apply(array $Payload = [], array $Context = [])`
  - `PyOS.LeaveApprove()` → `public static function Approve(array $Payload = [], array $Context = [])`
  - `PyOS.LeaveReject()` → `public static function Reject(array $Payload = [], array $Context = [])`
  - `PyOS.LeaveBalance()` → `public static function Balance(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Leave()` |
| Apply | `public static function Apply(array $Payload = [], array $Context = [])` | `PyOS.LeaveApply()` |
| Approve | `public static function Approve(array $Payload = [], array $Context = [])` | `PyOS.LeaveApprove()` |
| Reject | `public static function Reject(array $Payload = [], array $Context = [])` | `PyOS.LeaveReject()` |
| Balance | `public static function Balance(array $Payload = [], array $Context = [])` | `PyOS.LeaveBalance()` |
### <span id="PyOS-ledger"></span>PyOS.Ledger
- **Purpose**: Immutable, enterprise-grade ledger engine Multi-App + Multi-Tenant financial journal Append-only, idempotent, audit-ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Ledger.py](PyOS/Py.26.00.00/Engine/PyOS.Ledger.py)
- **Class**: `PyOS_Ledger`
- **Facade Entry Points**:
  - `PyOS.Ledger()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LedgerPost()` → `public static function Post(array $Payload, array $Context)`
  - `PyOS.LedgerQuery()` → `public static function Query(array $Payload, array $Context)`
  - `PyOS.LedgerBalance()` → `public static function Balance(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Ledger()` |
| Post | `public static function Post(array $Payload, array $Context)` | `PyOS.LedgerPost()` |
| Query | `public static function Query(array $Payload, array $Context)` | `PyOS.LedgerQuery()` |
| Balance | `public static function Balance(array $Payload, array $Context)` | `PyOS.LedgerBalance()` |
### <span id="PyOS-lesson"></span>PyOS.Lesson
- **Purpose**: Universal Lesson Engine for PyOS OS-level Lesson primitive (not LMS-only) Multi-App + Multi-Tenant + Multi-Brand compatible
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Lesson.py](PyOS/Py.26.00.00/Engine/PyOS.Lesson.py)
- **Class**: `PyOS_Lesson`
- **Facade Entry Points**:
  - `PyOS.Lesson()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LessonNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.LessonGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.LessonList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.LessonPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `PyOS.LessonProgressSet()` → `public static function ProgressSet(array $Payload, array $Context)`
  - `PyOS.LessonDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Lesson()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.LessonNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.LessonGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.LessonList()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.LessonPublish()` |
| ProgressSet | `public static function ProgressSet(array $Payload, array $Context)` | `PyOS.LessonProgressSet()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.LessonDelete()` |
### <span id="PyOS-like"></span>PyOS.Like
- **Purpose**: Universal Like / Unlike / Reaction engine Works with ANY entity (Post, Comment, Product, Video, Profile, etc.) Multi-App + Multi-Tenant + Enterprise Grade
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Like.py](PyOS/Py.26.00.00/Engine/PyOS.Like.py)
- **Class**: `PyOS_Like`
- **Facade Entry Points**:
  - `PyOS.Like()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Like()` |
### <span id="PyOS-linter"></span>PyOS.Linter
- **Purpose**: Enterprise-grade static code analysis engine for PyOS Enforces AliensStyle + AliensGrade contracts Validates Engine, Boot, Unit, SDK, App, Brand code Acts as Quality Firewall before Release
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Linter.py](PyOS/Py.26.00.00/Engine/PyOS.Linter.py)
- **Class**: `PyOS_Linter`
- **Facade Entry Points**:
  - `PyOS.Linter()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LinterScan()` → `public static function Scan(array $Payload, array $Context)`
  - `PyOS.LinterFile()` → `public static function File(array $Payload, array $Context)`
  - `PyOS.LinterEngine()` → `public static function Engine(array $Payload, array $Context)`
  - `PyOS.LinterReport()` → `public static function Report(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Linter()` |
| Scan | `public static function Scan(array $Payload, array $Context)` | `PyOS.LinterScan()` |
| File | `public static function File(array $Payload, array $Context)` | `PyOS.LinterFile()` |
| Engine | `public static function Engine(array $Payload, array $Context)` | `PyOS.LinterEngine()` |
| Report | `public static function Report(array $Payload, array $Context)` | `PyOS.LinterReport()` |
### <span id="PyOS-loadbalancer"></span>PyOS.LoadBalancer
- **Purpose**: Logical load balancing for PyOS backend OS Multi-App, Multi-Tenant, Multi-Cluster routing High-performance decision engine (app-level LB) Zero-downtime deployments (Canary, Blue/Green)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.LoadBalancer.py](PyOS/Py.26.00.00/Engine/PyOS.LoadBalancer.py)
- **Class**: `PyOS_LoadBalancer`
- **Facade Entry Points**:
  - `PyOS.LoadBalancer()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.LoadBalancerDecide()` → `public static function Decide(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.LoadBalancer()` |
| Decide | `public static function Decide(array $Payload = [], array $Context = [])` | `PyOS.LoadBalancerDecide()` |
### <span id="PyOS-locale"></span>PyOS.Locale
- **Purpose**: Enterprise-grade Locale Operating System layer for PyOS Multi-App + Multi-Brand + Multi-Tenant locale resolution + formatting Safe, deterministic, cached locale context resolution Translation dictionaries (file-first) with strict fallback chain
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Locale.py](PyOS/Py.26.00.00/Engine/PyOS.Locale.py)
- **Class**: `PyOS_Locale`
- **Facade Entry Points**:
  - `PyOS.Locale()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleTranslate()` → `public static function Translate(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleFormatDate()` → `public static function FormatDate(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleFormatNumber()` → `public static function FormatNumber(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleSupported()` → `public static function Supported(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleNormalize()` → `public static function Normalize(array $Payload = [], array $Context = [])`
  - `PyOS.LocaleFlush()` → `public static function Flush(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Locale()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.LocaleResolve()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.LocaleGet()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `PyOS.LocaleSet()` |
| Translate | `public static function Translate(array $Payload = [], array $Context = [])` | `PyOS.LocaleTranslate()` |
| FormatDate | `public static function FormatDate(array $Payload = [], array $Context = [])` | `PyOS.LocaleFormatDate()` |
| FormatNumber | `public static function FormatNumber(array $Payload = [], array $Context = [])` | `PyOS.LocaleFormatNumber()` |
| Supported | `public static function Supported(array $Payload = [], array $Context = [])` | `PyOS.LocaleSupported()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `PyOS.LocaleValidate()` |
| Normalize | `public static function Normalize(array $Payload = [], array $Context = [])` | `PyOS.LocaleNormalize()` |
| Flush | `public static function Flush(array $Payload = [], array $Context = [])` | `PyOS.LocaleFlush()` |
### <span id="PyOS-location"></span>PyOS.Location
- **Purpose**: Enterprise-grade Location & Geo-Intelligence Engine Unified location resolution for API, Web, Mobile, SaaS Multi-App + Multi-Tenant aware Security, Privacy & Performance first
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Location.py](PyOS/Py.26.00.00/Engine/PyOS.Location.py)
- **Class**: `PyOS_Location`
- **Facade Entry Points**:
  - `PyOS.Location()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LocationGet()` → `public static function Get(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Location()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.LocationGet()` |
### <span id="PyOS-lock"></span>PyOS.Lock
- **Purpose**: Distributed Lock Engine for PyOS Kernel Prevent race conditions, double execution, data corruption Support API, Cron, Queue, Worker, Task, EventBus
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Lock.py](PyOS/Py.26.00.00/Engine/PyOS.Lock.py)
- **Class**: `PyOS_Lock`
- **Facade Entry Points**:
  - `PyOS.Lock()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LockAcquire()` → `public static function Acquire(array $Payload, array $Context)`
  - `PyOS.LockTry()` → `public static function Try(array $Payload, array $Context)`
  - `PyOS.LockRelease()` → `public static function Release(array $Payload, array $Context)`
  - `PyOS.LockExtend()` → `public static function Extend(array $Payload, array $Context)`
  - `PyOS.LockStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.LockForceRelease()` → `public static function ForceRelease(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Lock()` |
| Acquire | `public static function Acquire(array $Payload, array $Context)` | `PyOS.LockAcquire()` |
| Try | `public static function Try(array $Payload, array $Context)` | `PyOS.LockTry()` |
| Release | `public static function Release(array $Payload, array $Context)` | `PyOS.LockRelease()` |
| Extend | `public static function Extend(array $Payload, array $Context)` | `PyOS.LockExtend()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.LockStatus()` |
| ForceRelease | `public static function ForceRelease(array $Payload, array $Context)` | `PyOS.LockForceRelease()` |
### <span id="PyOS-log"></span>PyOS.Log
- **Purpose**: Enterprise-grade structured logging backbone for PyOS Multi-App + Multi-Tenant safe (App/Brand/Ecosystem/User/RequestId) High-performance buffered writes with safe flush JSONL file sink by default (fast + index-friendly) PII/Secret redaction (tokens/password/cookie/etc.) Audit/Security channels, request start/end timing support
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Log.py](PyOS/Py.26.00.00/Engine/PyOS.Log.py)
- **Class**: `PyOS_Log`
- **Facade Entry Points**:
  - `PyOS.Log()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LogWrite()` → `public static function Write(array $Payload, array $Context)`
  - `PyOS.LogDebug()` → `public static function Debug(array $Payload, array $Context)`
  - `PyOS.LogInfo()` → `public static function Info(array $Payload, array $Context)`
  - `PyOS.LogNotice()` → `public static function Notice(array $Payload, array $Context)`
  - `PyOS.LogWarning()` → `public static function Warning(array $Payload, array $Context)`
  - `PyOS.LogError()` → `public static function Error(array $Payload, array $Context)`
  - `PyOS.LogCritical()` → `public static function Critical(array $Payload, array $Context)`
  - `PyOS.LogAlert()` → `public static function Alert(array $Payload, array $Context)`
  - `PyOS.LogEmergency()` → `public static function Emergency(array $Payload, array $Context)`
  - `PyOS.LogSecurity()` → `public static function Security(array $Payload, array $Context)`
  - `PyOS.LogAudit()` → `public static function Audit(array $Payload, array $Context)`
  - `PyOS.LogRequestStart()` → `public static function RequestStart(array $Payload, array $Context)`
  - `PyOS.LogRequestEnd()` → `public static function RequestEnd(array $Payload, array $Context)`
  - `PyOS.LogFlush()` → `public static function Flush(array $Payload, array $Context)`
  - `PyOS.LogConfigGet()` → `public static function ConfigGet(array $Payload, array $Context)`
  - `PyOS.LogConfigSet()` → `public static function ConfigSet(array $Payload, array $Context)`
  - `PyOS.LogScope()` → `public static function Scope(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Log()` |
| Write | `public static function Write(array $Payload, array $Context)` | `PyOS.LogWrite()` |
| Debug | `public static function Debug(array $Payload, array $Context)` | `PyOS.LogDebug()` |
| Info | `public static function Info(array $Payload, array $Context)` | `PyOS.LogInfo()` |
| Notice | `public static function Notice(array $Payload, array $Context)` | `PyOS.LogNotice()` |
| Warning | `public static function Warning(array $Payload, array $Context)` | `PyOS.LogWarning()` |
| Error | `public static function Error(array $Payload, array $Context)` | `PyOS.LogError()` |
| Critical | `public static function Critical(array $Payload, array $Context)` | `PyOS.LogCritical()` |
| Alert | `public static function Alert(array $Payload, array $Context)` | `PyOS.LogAlert()` |
| Emergency | `public static function Emergency(array $Payload, array $Context)` | `PyOS.LogEmergency()` |
| Security | `public static function Security(array $Payload, array $Context)` | `PyOS.LogSecurity()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `PyOS.LogAudit()` |
| RequestStart | `public static function RequestStart(array $Payload, array $Context)` | `PyOS.LogRequestStart()` |
| RequestEnd | `public static function RequestEnd(array $Payload, array $Context)` | `PyOS.LogRequestEnd()` |
| Flush | `public static function Flush(array $Payload, array $Context)` | `PyOS.LogFlush()` |
| ConfigGet | `public static function ConfigGet(array $Payload, array $Context)` | `PyOS.LogConfigGet()` |
| ConfigSet | `public static function ConfigSet(array $Payload, array $Context)` | `PyOS.LogConfigSet()` |
| Scope | `public static function Scope(array $Payload, array $Context)` | `PyOS.LogScope()` |
### <span id="PyOS-login"></span>PyOS.Login
- **Purpose**: Enterprise-grade Login Engine for PyOS Multi-App, Multi-Tenant, Secure Authentication Gateway Acts as Identity Entry Point (Password-based v1)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Login.py](PyOS/Py.26.00.00/Engine/PyOS.Login.py)
- **Class**: `PyOS_Login`
- **Facade Entry Points**:
  - `PyOS.Login()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.LoginStart()` → `public static function Start(array $Payload, array $Context)`
  - `PyOS.LoginPassword()` → `public static function Password(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Login()` |
| Start | `public static function Start(array $Payload, array $Context)` | `PyOS.LoginStart()` |
| Password | `public static function Password(array $Payload, array $Context)` | `PyOS.LoginPassword()` |
### <span id="PyOS-logout"></span>PyOS.Logout
- **Purpose**: Enterprise-grade Logout & Access Revocation Engine Multi-App, Multi-Tenant, Multi-Device compatible Token, Session, Cache & Permission invalidation Policy, Security & Audit integrated
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Logout.py](PyOS/Py.26.00.00/Engine/PyOS.Logout.py)
- **Class**: `PyOS_Logout`
- **Facade Entry Points**:
  - `PyOS.Logout()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.LogoutNow()` → `public static function Now(array $Payload, array $Context)`
  - `PyOS.LogoutAll()` → `public static function All(array $Payload, array $Context)`
  - `PyOS.LogoutDevice()` → `public static function Device(array $Payload, array $Context)`
  - `PyOS.LogoutTenant()` → `public static function Tenant(array $Payload, array $Context)`
  - `PyOS.LogoutApp()` → `public static function App(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Logout()` |
| Now | `public static function Now(array $Payload, array $Context)` | `PyOS.LogoutNow()` |
| All | `public static function All(array $Payload, array $Context)` | `PyOS.LogoutAll()` |
| Device | `public static function Device(array $Payload, array $Context)` | `PyOS.LogoutDevice()` |
| Tenant | `public static function Tenant(array $Payload, array $Context)` | `PyOS.LogoutTenant()` |
| App | `public static function App(array $Payload, array $Context)` | `PyOS.LogoutApp()` |
### <span id="PyOS-manifest"></span>PyOS.Manifest
- **Purpose**: Single Source Of Truth (SSOT) for all configuration Enterprise-grade Manifest engine for: Multi-App Multi-Brand Multi-Tenant Multi-Version PyOS Load + Merge + Validate config layers: Global → PyOS → Brand → App → Module → Tenant → Override High-Performance with local + Redis cache support
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Manifest.py](PyOS/Py.26.00.00/Engine/PyOS.Manifest.py)
- **Class**: `PyOS_Manifest`
- **Facade Entry Points**:
  - `PyOS.Manifest()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ManifestGet()` → `public static function Get(array $Payload = [], array $Context = []): array`
  - `PyOS.ManifestValue()` → `public static function Value(array $Payload = [], array $Context = [])`
  - `PyOS.ManifestReload()` → `public static function Reload(array $Payload = [], array $Context = []): bool`
  - `PyOS.ManifestDescribe()` → `public static function Describe(array $Payload = [], array $Context = []): array`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Manifest()` |
| Get | `public static function Get(array $Payload = [], array $Context = []): array` | `PyOS.ManifestGet()` |
| Value | `public static function Value(array $Payload = [], array $Context = [])` | `PyOS.ManifestValue()` |
| Reload | `public static function Reload(array $Payload = [], array $Context = []): bool` | `PyOS.ManifestReload()` |
| Describe | `public static function Describe(array $Payload = [], array $Context = []): array` | `PyOS.ManifestDescribe()` |
### <span id="PyOS-media"></span>PyOS.Media
- **Purpose**: Unified Media Engine for PyOS Backend OS Upload, Store, Secure, Deliver, Audit media assets Multi-App + Multi-Tenant safe by design
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Media.py](PyOS/Py.26.00.00/Engine/PyOS.Media.py)
- **Class**: `PyOS_Media`
- **Facade Entry Points**:
  - `PyOS.Media()` → `public static function Main($Payload, $Context)`
  - `PyOS.MediaNew()` → `public static function New($Payload, $Context)`
  - `PyOS.MediaGet()` → `public static function Get($Payload, $Context)`
  - `PyOS.MediaDelete()` → `public static function Delete($Payload, $Context)`
  - `PyOS.MediaURL()` → `public static function URL($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.Media()` |
| New | `public static function New($Payload, $Context)` | `PyOS.MediaNew()` |
| Get | `public static function Get($Payload, $Context)` | `PyOS.MediaGet()` |
| Delete | `public static function Delete($Payload, $Context)` | `PyOS.MediaDelete()` |
| URL | `public static function URL($Payload, $Context)` | `PyOS.MediaURL()` |
### <span id="PyOS-medicalrecord"></span>PyOS.MedicalRecord
- **Purpose**: Universal Medical Record Engine for PyOS Multi-App + Multi-Tenant + Enterprise Grade Versioned, Audited, Secure Medical Records
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.MedicalRecord.py](PyOS/Py.26.00.00/Engine/PyOS.MedicalRecord.py)
- **Class**: `PyOS_MedicalRecord`
- **Facade Entry Points**:
  - `PyOS.MedicalRecord()` → `public static function Main($Payload, $Context)`
  - `PyOS.MedicalRecordNew()` → `public static function New($Payload, $Context)`
  - `PyOS.MedicalRecordGet()` → `public static function Get($Payload, $Context)`
  - `PyOS.MedicalRecordUpdate()` → `public static function Update($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.MedicalRecord()` |
| New | `public static function New($Payload, $Context)` | `PyOS.MedicalRecordNew()` |
| Get | `public static function Get($Payload, $Context)` | `PyOS.MedicalRecordGet()` |
| Update | `public static function Update($Payload, $Context)` | `PyOS.MedicalRecordUpdate()` |
### <span id="PyOS-memory"></span>PyOS.Memory
- **Purpose**: Unified system-level memory engine for PyOS Tier-0 (Request Memory) + Tier-2 (Shared Cache) Strict Tenant + App + Scope isolation Safe, fast, auditable memory substrate for all engines
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Memory.py](PyOS/Py.26.00.00/Engine/PyOS.Memory.py)
- **Class**: `PyOS_Memory`
- **Facade Entry Points**:
  - `PyOS.Memory()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.MemoryGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.MemorySet()` → `public static function Set(array $Payload, array $Context)`
  - `PyOS.MemoryHas()` → `public static function Has(array $Payload, array $Context)`
  - `PyOS.MemoryDel()` → `public static function Del(array $Payload, array $Context)`
  - `PyOS.MemoryInc()` → `public static function Inc(array $Payload, array $Context)`
  - `PyOS.MemoryDec()` → `public static function Dec(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Memory()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.MemoryGet()` |
| Set | `public static function Set(array $Payload, array $Context)` | `PyOS.MemorySet()` |
| Has | `public static function Has(array $Payload, array $Context)` | `PyOS.MemoryHas()` |
| Del | `public static function Del(array $Payload, array $Context)` | `PyOS.MemoryDel()` |
| Inc | `public static function Inc(array $Payload, array $Context)` | `PyOS.MemoryInc()` |
| Dec | `public static function Dec(array $Payload, array $Context)` | `PyOS.MemoryDec()` |
### <span id="PyOS-message"></span>PyOS.Message
- **Purpose**: Universal Messaging Engine for PyOS Supports chat, system messages, notifications, workflows Multi-Tenant + Multi-App safe High-performance, async-ready, audit-friendly
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Message.py](PyOS/Py.26.00.00/Engine/PyOS.Message.py)
- **Class**: `PyOS_Message`
- **Facade Entry Points**:
  - `PyOS.Message()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.MessageThreadNew()` → `public static function ThreadNew(array $Payload, array $Context)`
  - `PyOS.MessageSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.MessageList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.MessageMarkRead()` → `public static function MarkRead(array $Payload, array $Context)`
  - `PyOS.MessageThreadList()` → `public static function ThreadList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Message()` |
| ThreadNew | `public static function ThreadNew(array $Payload, array $Context)` | `PyOS.MessageThreadNew()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.MessageSend()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.MessageList()` |
| MarkRead | `public static function MarkRead(array $Payload, array $Context)` | `PyOS.MessageMarkRead()` |
| ThreadList | `public static function ThreadList(array $Payload, array $Context)` | `PyOS.MessageThreadList()` |
### <span id="PyOS-metric"></span>PyOS.Metric
- **Purpose**: Enterprise-grade Metrics Engine for PyOS Ultra low-overhead counters, gauges, histograms Multi-App + Multi-Tenant safe instrumentation High-cardinality guard + sampling Async-first flush (Queue/Cache ready)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Metric.py](PyOS/Py.26.00.00/Engine/PyOS.Metric.py)
- **Class**: `PyOS_Metric`
- **Facade Entry Points**:
  - `PyOS.Metric()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.MetricInc()` → `public static function Inc(array $Payload, array $Context)`
  - `PyOS.MetricGauge()` → `public static function Gauge(array $Payload, array $Context)`
  - `PyOS.MetricObs()` → `public static function Obs(array $Payload, array $Context)`
  - `PyOS.MetricEvent()` → `public static function Event(array $Payload, array $Context)`
  - `PyOS.MetricFlush()` → `public static function Flush(array $Payload, array $Context)`
  - `PyOS.MetricExport()` → `public static function Export(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Metric()` |
| Inc | `public static function Inc(array $Payload, array $Context)` | `PyOS.MetricInc()` |
| Gauge | `public static function Gauge(array $Payload, array $Context)` | `PyOS.MetricGauge()` |
| Obs | `public static function Obs(array $Payload, array $Context)` | `PyOS.MetricObs()` |
| Event | `public static function Event(array $Payload, array $Context)` | `PyOS.MetricEvent()` |
| Flush | `public static function Flush(array $Payload, array $Context)` | `PyOS.MetricFlush()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.MetricExport()` |
### <span id="PyOS-migration"></span>PyOS.Migration
- **Purpose**: Enterprise-grade Migration Engine for PyOS Multi-App + Multi-Tenant schema migration system Deterministic, Idempotent, Audited migrations Loader + EngineRouter compatible
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Migration.py](PyOS/Py.26.00.00/Engine/PyOS.Migration.py)
- **Class**: `PyOS_Migration`
- **Facade Entry Points**:
  - `PyOS.Migration()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.MigrationStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.MigrationPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.MigrationApply()` → `public static function Apply(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Migration()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.MigrationStatus()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.MigrationPlan()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `PyOS.MigrationApply()` |
### <span id="PyOS-mock"></span>PyOS.Mock
- **Purpose**: Enterprise-grade Mock Engine for PyOS API / HTTP / DB / Email / SMS / Webhook mocking Multi-App + Multi-Tenant safe mock resolution Deterministic + Chaos testing support
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Mock.py](PyOS/Py.26.00.00/Engine/PyOS.Mock.py)
- **Class**: `PyOS_Mock`
- **Facade Entry Points**:
  - `PyOS.Mock()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.MockIsOn()` → `public static function IsOn(array $Payload, array $Context)`
  - `PyOS.MockProfileGet()` → `public static function ProfileGet(array $Payload, array $Context)`
  - `PyOS.MockProfileSet()` → `public static function ProfileSet(array $Payload, array $Context)`
  - `PyOS.MockScope()` → `public static function Scope(array $Payload, array $Context)`
  - `PyOS.MockIntercept()` → `public static function Intercept(array $Payload, array $Context)`
  - `PyOS.MockRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.MockSet()` → `public static function Set(array $Payload, array $Context)`
  - `PyOS.MockDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Mock()` |
| IsOn | `public static function IsOn(array $Payload, array $Context)` | `PyOS.MockIsOn()` |
| ProfileGet | `public static function ProfileGet(array $Payload, array $Context)` | `PyOS.MockProfileGet()` |
| ProfileSet | `public static function ProfileSet(array $Payload, array $Context)` | `PyOS.MockProfileSet()` |
| Scope | `public static function Scope(array $Payload, array $Context)` | `PyOS.MockScope()` |
| Intercept | `public static function Intercept(array $Payload, array $Context)` | `PyOS.MockIntercept()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.MockRun()` |
| Set | `public static function Set(array $Payload, array $Context)` | `PyOS.MockSet()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.MockDelete()` |
### <span id="PyOS-module"></span>PyOS.Module
- **Purpose**: [] PyOS ke liye OS-level Module Registry + ModuleMap builder Multi-App + Multi-Tenant module enable/disable, bind/unbind Ultra-fast Resolve + Units/Map cache (Boot + Executor ko turbo) Dependency-aware (Phase-2 ready), audit-ready, deterministic
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Module.py](PyOS/Py.26.00.00/Engine/PyOS.Module.py)
- **Class**: `PyOS_Module`
- **Facade Entry Points**:
  - `PyOS.Module()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleList()` → `public static function List(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleEnable()` → `public static function Enable(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleDisable()` → `public static function Disable(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleMap()` → `public static function Map(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleUnits()` → `public static function Units(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleInvalidate()` → `public static function Invalidate(array $Payload = [], array $Context = [])`
  - `PyOS.ModuleWarm()` → `public static function Warm(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Module()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.ModuleGet()` |
| List | `public static function List(array $Payload = [], array $Context = [])` | `PyOS.ModuleList()` |
| Enable | `public static function Enable(array $Payload = [], array $Context = [])` | `PyOS.ModuleEnable()` |
| Disable | `public static function Disable(array $Payload = [], array $Context = [])` | `PyOS.ModuleDisable()` |
| Map | `public static function Map(array $Payload = [], array $Context = [])` | `PyOS.ModuleMap()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.ModuleResolve()` |
| Units | `public static function Units(array $Payload = [], array $Context = [])` | `PyOS.ModuleUnits()` |
| Invalidate | `public static function Invalidate(array $Payload = [], array $Context = [])` | `PyOS.ModuleInvalidate()` |
| Warm | `public static function Warm(array $Payload = [], array $Context = [])` | `PyOS.ModuleWarm()` |
### <span id="PyOS-monitor"></span>PyOS.Monitor
- **Purpose**: Enterprise-grade observability engine for PyOS Monitor API / Cron / Webhook / Asset / Component lifecycle Measure execution time, memory usage, result state Multi-App + Multi-Tenant aware Ultra-low overhead, fail-safe
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Monitor.py](PyOS/Py.26.00.00/Engine/PyOS.Monitor.py)
- **Class**: `PyOS_Monitor`
- **Facade Entry Points**:
  - `PyOS.Monitor()` → `public static function Main(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Monitor()` |
### <span id="PyOS-node"></span>PyOS.Node
- **Purpose**: Distributed Node Management Engine for PyOS Represents a physical / virtual runtime instance (Node) Supports Multi-App + Multi-Tenant execution Provides Node Registration, Heartbeat, Health, Selection Acts as PyOS Runtime Control Plane
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Node.py](PyOS/Py.26.00.00/Engine/PyOS.Node.py)
- **Class**: `PyOS_Node`
- **Facade Entry Points**:
  - `PyOS.Node()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.NodeRegister()` → `public static function Register(array $Payload, array $Context)`
  - `PyOS.NodeHeartbeat()` → `public static function Heartbeat(array $Payload, array $Context)`
  - `PyOS.NodeHealth()` → `public static function Health(array $Payload, array $Context)`
  - `PyOS.NodePick()` → `public static function Pick(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Node()` |
| Register | `public static function Register(array $Payload, array $Context)` | `PyOS.NodeRegister()` |
| Heartbeat | `public static function Heartbeat(array $Payload, array $Context)` | `PyOS.NodeHeartbeat()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.NodeHealth()` |
| Pick | `public static function Pick(array $Payload, array $Context)` | `PyOS.NodePick()` |
### <span id="PyOS-oauth"></span>PyOS.OAuth
- **Purpose**: OAuth2 Authorization Server + Client Engine for PyOS Central identity authorization fabric Supports Code Flow + PKCE + Refresh + Introspection Fully Loader + EngineRouter compatible
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.OAuth.py](PyOS/Py.26.00.00/Engine/PyOS.OAuth.py)
- **Class**: `PyOS_OAuth`
- **Facade Entry Points**:
  - `PyOS.OAuth()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.OAuthAuthorize()` → `public static function Authorize(array $Payload, array $Context)`
  - `PyOS.OAuthToken()` → `public static function Token(array $Payload, array $Context)`
  - `PyOS.OAuthIntrospect()` → `public static function Introspect(array $Payload, array $Context)`
  - `PyOS.OAuthRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `PyOS.OAuthJwks()` → `public static function Jwks(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.OAuth()` |
| Authorize | `public static function Authorize(array $Payload, array $Context)` | `PyOS.OAuthAuthorize()` |
| Token | `public static function Token(array $Payload, array $Context)` | `PyOS.OAuthToken()` |
| Introspect | `public static function Introspect(array $Payload, array $Context)` | `PyOS.OAuthIntrospect()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `PyOS.OAuthRevoke()` |
| Jwks | `public static function Jwks(array $Payload, array $Context)` | `PyOS.OAuthJwks()` |
### <span id="PyOS-optimize"></span>PyOS.Optimize
- **Purpose**: Self-optimization engine for PyOS Performance, cache, DB, concurrency, degradation planning Multi-App + Multi-Tenant aware optimization
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Optimize.py](PyOS/Py.26.00.00/Engine/PyOS.Optimize.py)
- **Class**: `PyOS_Optimize`
- **Facade Entry Points**:
  - `PyOS.Optimize()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.OptimizePlan()` → `public static function Plan(array $Payload = [], array $Context = [])`
  - `PyOS.OptimizeBudget()` → `public static function Budget(array $Plan = [], array $Context = [])`
  - `PyOS.OptimizeCachePolicy()` → `public static function CachePolicy(array $Payload = [], array $Context = [])`
  - `PyOS.OptimizeReport()` → `public static function Report(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Optimize()` |
| Plan | `public static function Plan(array $Payload = [], array $Context = [])` | `PyOS.OptimizePlan()` |
| Budget | `public static function Budget(array $Plan = [], array $Context = [])` | `PyOS.OptimizeBudget()` |
| CachePolicy | `public static function CachePolicy(array $Payload = [], array $Context = [])` | `PyOS.OptimizeCachePolicy()` |
| Report | `public static function Report(array $Payload = [], array $Context = [])` | `PyOS.OptimizeReport()` |
### <span id="PyOS-orchestrator"></span>PyOS.Orchestrator
- **Purpose**: Engine-of-Engines: universal execution orchestration for PyOS Sync + Async dispatch (Run / Enqueue / Dispatch) Pipeline / Workflow runner (step-based orchestration) Enterprise controls: idempotency, locking, retry, policy gates Multi-App + Multi-Tenant safe scoping (Context-driven)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Orchestrator.py](PyOS/Py.26.00.00/Engine/PyOS.Orchestrator.py)
- **Class**: `PyOS_Orchestrator`
- **Facade Entry Points**:
  - `PyOS.Orchestrator()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.OrchestratorRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.OrchestratorDispatch()` → `public static function Dispatch(array $Payload, array $Context)`
  - `PyOS.OrchestratorEnqueue()` → `public static function Enqueue(array $Payload, array $Context)`
  - `PyOS.OrchestratorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.OrchestratorCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.OrchestratorRetry()` → `public static function Retry(array $Payload, array $Context)`
  - `PyOS.OrchestratorPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.OrchestratorPipeline()` → `public static function Pipeline(array $Payload, array $Context)`
  - `PyOS.OrchestratorWorkflow()` → `public static function Workflow(array $Payload, array $Context)`
  - `PyOS.OrchestratorMetrics()` → `public static function Metrics(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Orchestrator()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.OrchestratorRun()` |
| Dispatch | `public static function Dispatch(array $Payload, array $Context)` | `PyOS.OrchestratorDispatch()` |
| Enqueue | `public static function Enqueue(array $Payload, array $Context)` | `PyOS.OrchestratorEnqueue()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.OrchestratorStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.OrchestratorCancel()` |
| Retry | `public static function Retry(array $Payload, array $Context)` | `PyOS.OrchestratorRetry()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.OrchestratorPlan()` |
| Pipeline | `public static function Pipeline(array $Payload, array $Context)` | `PyOS.OrchestratorPipeline()` |
| Workflow | `public static function Workflow(array $Payload, array $Context)` | `PyOS.OrchestratorWorkflow()` |
| Metrics | `public static function Metrics(array $Payload, array $Context)` | `PyOS.OrchestratorMetrics()` |
### <span id="PyOS-order"></span>PyOS.Order
- **Purpose**: Enterprise Grade Order Operating Engine (Multi-App + Multi-Tenant) Full Order Lifecycle: New, Get, List, Update, Confirm, Cancel, Pay, Fulfill, Ship, Deliver, Refund, Timeline, Recalc, Export, Import Idempotency + Concurrency + Audit/Event timeline
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Order.py](PyOS/Py.26.00.00/Engine/PyOS.Order.py)
- **Class**: `PyOS_Order`
- **Facade Entry Points**:
  - `PyOS.Order()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.OrderNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.OrderGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.OrderList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.OrderUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.OrderConfirm()` → `public static function Confirm(array $Payload, array $Context)`
  - `PyOS.OrderCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.OrderPay()` → `public static function Pay(array $Payload, array $Context)`
  - `PyOS.OrderPaymentCallback()` → `public static function PaymentCallback(array $Payload, array $Context)`
  - `PyOS.OrderFulfill()` → `public static function Fulfill(array $Payload, array $Context)`
  - `PyOS.OrderShip()` → `public static function Ship(array $Payload, array $Context)`
  - `PyOS.OrderDeliver()` → `public static function Deliver(array $Payload, array $Context)`
  - `PyOS.OrderRefund()` → `public static function Refund(array $Payload, array $Context)`
  - `PyOS.OrderTimeline()` → `public static function Timeline(array $Payload, array $Context)`
  - `PyOS.OrderRecalc()` → `public static function Recalc(array $Payload, array $Context)`
  - `PyOS.OrderLock()` → `public static function Lock(array $Payload, array $Context)`
  - `PyOS.OrderUnlock()` → `public static function Unlock(array $Payload, array $Context)`
  - `PyOS.OrderExport()` → `public static function Export(array $Payload, array $Context)`
  - `PyOS.OrderImport()` → `public static function Import(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Order()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.OrderNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.OrderGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.OrderList()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.OrderUpdate()` |
| Confirm | `public static function Confirm(array $Payload, array $Context)` | `PyOS.OrderConfirm()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.OrderCancel()` |
| Pay | `public static function Pay(array $Payload, array $Context)` | `PyOS.OrderPay()` |
| PaymentCallback | `public static function PaymentCallback(array $Payload, array $Context)` | `PyOS.OrderPaymentCallback()` |
| Fulfill | `public static function Fulfill(array $Payload, array $Context)` | `PyOS.OrderFulfill()` |
| Ship | `public static function Ship(array $Payload, array $Context)` | `PyOS.OrderShip()` |
| Deliver | `public static function Deliver(array $Payload, array $Context)` | `PyOS.OrderDeliver()` |
| Refund | `public static function Refund(array $Payload, array $Context)` | `PyOS.OrderRefund()` |
| Timeline | `public static function Timeline(array $Payload, array $Context)` | `PyOS.OrderTimeline()` |
| Recalc | `public static function Recalc(array $Payload, array $Context)` | `PyOS.OrderRecalc()` |
| Lock | `public static function Lock(array $Payload, array $Context)` | `PyOS.OrderLock()` |
| Unlock | `public static function Unlock(array $Payload, array $Context)` | `PyOS.OrderUnlock()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.OrderExport()` |
| Import | `public static function Import(array $Payload, array $Context)` | `PyOS.OrderImport()` |
### <span id="PyOS-organization"></span>PyOS.Organization
- **Purpose**: Enterprise-grade Organization (Tenant) Engine for PyOS Multi-App + Multi-Tenant organization lifecycle, membership, roles, units, settings, policies, audit trails
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Organization.py](PyOS/Py.26.00.00/Engine/PyOS.Organization.py)
- **Class**: `PyOS_Organization`
- **Facade Entry Points**:
  - `PyOS.Organization()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.OrganizationNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.OrganizationGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.OrganizationUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.OrganizationDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.OrganizationList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.OrganizationSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.OrganizationSwitch()` → `public static function Switch(array $Payload, array $Context)`
  - `PyOS.OrganizationMembers()` → `public static function Members(array $Payload, array $Context)`
  - `PyOS.OrganizationInvite()` → `public static function Invite(array $Payload, array $Context)`
  - `PyOS.OrganizationAcceptInvite()` → `public static function AcceptInvite(array $Payload, array $Context)`
  - `PyOS.OrganizationRemoveMember()` → `public static function RemoveMember(array $Payload, array $Context)`
  - `PyOS.OrganizationChangeRole()` → `public static function ChangeRole(array $Payload, array $Context)`
  - `PyOS.OrganizationUnitNew()` → `public static function UnitNew(array $Payload, array $Context)`
  - `PyOS.OrganizationUnitList()` → `public static function UnitList(array $Payload, array $Context)`
  - `PyOS.OrganizationSettingSet()` → `public static function SettingSet(array $Payload, array $Context)`
  - `PyOS.OrganizationSettingGet()` → `public static function SettingGet(array $Payload, array $Context)`
  - `PyOS.OrganizationPolicySet()` → `public static function PolicySet(array $Payload, array $Context)`
  - `PyOS.OrganizationPolicyGet()` → `public static function PolicyGet(array $Payload, array $Context)`
  - `PyOS.OrganizationAudit()` → `public static function Audit(array $Payload, array $Context)`
  - `PyOS.Organization()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.OrganizationNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.OrganizationGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.OrganizationUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.OrganizationDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.OrganizationList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.OrganizationSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.OrganizationSwitch()` → `public static function Switch(array $Payload, array $Context)`
  - `PyOS.OrganizationMembers()` → `public static function Members(array $Payload, array $Context)`
  - `PyOS.OrganizationInvite()` → `public static function Invite(array $Payload, array $Context)`
  - `PyOS.OrganizationAcceptInvite()` → `public static function AcceptInvite(array $Payload, array $Context)`
  - `PyOS.OrganizationRemoveMember()` → `public static function RemoveMember(array $Payload, array $Context)`
  - `PyOS.OrganizationChangeRole()` → `public static function ChangeRole(array $Payload, array $Context)`
  - `PyOS.OrganizationUnitNew()` → `public static function UnitNew(array $Payload, array $Context)`
  - `PyOS.OrganizationUnitList()` → `public static function UnitList(array $Payload, array $Context)`
  - `PyOS.OrganizationSettingSet()` → `public static function SettingSet(array $Payload, array $Context)`
  - `PyOS.OrganizationSettingGet()` → `public static function SettingGet(array $Payload, array $Context)`
  - `PyOS.OrganizationPolicySet()` → `public static function PolicySet(array $Payload, array $Context)`
  - `PyOS.OrganizationPolicyGet()` → `public static function PolicyGet(array $Payload, array $Context)`
  - `PyOS.OrganizationAudit()` → `public static function Audit(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Organization()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.OrganizationNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.OrganizationGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.OrganizationUpdate()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.OrganizationDelete()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.OrganizationList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.OrganizationSearch()` |
| Switch | `public static function Switch(array $Payload, array $Context)` | `PyOS.OrganizationSwitch()` |
| Members | `public static function Members(array $Payload, array $Context)` | `PyOS.OrganizationMembers()` |
| Invite | `public static function Invite(array $Payload, array $Context)` | `PyOS.OrganizationInvite()` |
| AcceptInvite | `public static function AcceptInvite(array $Payload, array $Context)` | `PyOS.OrganizationAcceptInvite()` |
| RemoveMember | `public static function RemoveMember(array $Payload, array $Context)` | `PyOS.OrganizationRemoveMember()` |
| ChangeRole | `public static function ChangeRole(array $Payload, array $Context)` | `PyOS.OrganizationChangeRole()` |
| UnitNew | `public static function UnitNew(array $Payload, array $Context)` | `PyOS.OrganizationUnitNew()` |
| UnitList | `public static function UnitList(array $Payload, array $Context)` | `PyOS.OrganizationUnitList()` |
| SettingSet | `public static function SettingSet(array $Payload, array $Context)` | `PyOS.OrganizationSettingSet()` |
| SettingGet | `public static function SettingGet(array $Payload, array $Context)` | `PyOS.OrganizationSettingGet()` |
| PolicySet | `public static function PolicySet(array $Payload, array $Context)` | `PyOS.OrganizationPolicySet()` |
| PolicyGet | `public static function PolicyGet(array $Payload, array $Context)` | `PyOS.OrganizationPolicyGet()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `PyOS.OrganizationAudit()` |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Organization()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.OrganizationNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.OrganizationGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.OrganizationUpdate()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.OrganizationDelete()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.OrganizationList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.OrganizationSearch()` |
| Switch | `public static function Switch(array $Payload, array $Context)` | `PyOS.OrganizationSwitch()` |
| Members | `public static function Members(array $Payload, array $Context)` | `PyOS.OrganizationMembers()` |
| Invite | `public static function Invite(array $Payload, array $Context)` | `PyOS.OrganizationInvite()` |
| AcceptInvite | `public static function AcceptInvite(array $Payload, array $Context)` | `PyOS.OrganizationAcceptInvite()` |
| RemoveMember | `public static function RemoveMember(array $Payload, array $Context)` | `PyOS.OrganizationRemoveMember()` |
| ChangeRole | `public static function ChangeRole(array $Payload, array $Context)` | `PyOS.OrganizationChangeRole()` |
| UnitNew | `public static function UnitNew(array $Payload, array $Context)` | `PyOS.OrganizationUnitNew()` |
| UnitList | `public static function UnitList(array $Payload, array $Context)` | `PyOS.OrganizationUnitList()` |
| SettingSet | `public static function SettingSet(array $Payload, array $Context)` | `PyOS.OrganizationSettingSet()` |
| SettingGet | `public static function SettingGet(array $Payload, array $Context)` | `PyOS.OrganizationSettingGet()` |
| PolicySet | `public static function PolicySet(array $Payload, array $Context)` | `PyOS.OrganizationPolicySet()` |
| PolicyGet | `public static function PolicyGet(array $Payload, array $Context)` | `PyOS.OrganizationPolicyGet()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `PyOS.OrganizationAudit()` |
### <span id="PyOS-page"></span>PyOS.Page
- **Purpose**: Page Operating Engine for PyOS Multi-App + Multi-Brand + Multi-Tenant Page Resolution Security-aware, Cache-aware, AI-ready Page Backend
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Page.py](PyOS/Py.26.00.00/Engine/PyOS.Page.py)
- **Class**: `PyOS_Page`
- **Facade Entry Points**:
  - `PyOS.Page()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Page()` |
### <span id="PyOS-partition"></span>PyOS.Partition
- **Purpose**: High-performance data partitioning OS layer Multi-App + Multi-Tenant aware Time / Tenant / Policy driven partition resolution Zero-downtime rotation & routing Read/Write partition routing brain for PyOS
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Partition.py](PyOS/Py.26.00.00/Engine/PyOS.Partition.py)
- **Class**: `PyOS_Partition`
- **Facade Entry Points**:
  - `PyOS.Partition()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PartitionResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.PartitionRouteWrite()` → `public static function RouteWrite(array $Payload, array $Context)`
  - `PyOS.PartitionRouteRead()` → `public static function RouteRead(array $Payload, array $Context)`
  - `PyOS.PartitionEnsure()` → `public static function Ensure(array $Payload, array $Context)`
  - `PyOS.PartitionRotate()` → `public static function Rotate(array $Payload, array $Context)`
  - `PyOS.PartitionStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Partition()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.PartitionResolve()` |
| RouteWrite | `public static function RouteWrite(array $Payload, array $Context)` | `PyOS.PartitionRouteWrite()` |
| RouteRead | `public static function RouteRead(array $Payload, array $Context)` | `PyOS.PartitionRouteRead()` |
| Ensure | `public static function Ensure(array $Payload, array $Context)` | `PyOS.PartitionEnsure()` |
| Rotate | `public static function Rotate(array $Payload, array $Context)` | `PyOS.PartitionRotate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.PartitionStatus()` |
### <span id="PyOS-patient"></span>PyOS.Patient
- **Purpose**: Enterprise Grade Patient Engine (Multi-App + Multi-Tenant) OS-level entity management: Patient identity, lifecycle, privacy-ready High-performance patterns: strict scoping, safe outputs, audit-ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Patient.py](PyOS/Py.26.00.00/Engine/PyOS.Patient.py)
- **Class**: `PyOS_Patient`
- **Facade Entry Points**:
  - `PyOS.Patient()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PatientNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.PatientGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.PatientUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.PatientStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.PatientSearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.PatientConsentGrant()` → `public static function ConsentGrant(array $Payload, array $Context)`
  - `PyOS.PatientConsentRevoke()` → `public static function ConsentRevoke(array $Payload, array $Context)`
  - `PyOS.PatientExternalIdSet()` → `public static function ExternalIdSet(array $Payload, array $Context)`
  - `PyOS.PatientExternalIdGet()` → `public static function ExternalIdGet(array $Payload, array $Context)`
  - `PyOS.PatientReindex()` → `public static function Reindex(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Patient()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.PatientNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.PatientGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.PatientUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.PatientStatus()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.PatientSearch()` |
| ConsentGrant | `public static function ConsentGrant(array $Payload, array $Context)` | `PyOS.PatientConsentGrant()` |
| ConsentRevoke | `public static function ConsentRevoke(array $Payload, array $Context)` | `PyOS.PatientConsentRevoke()` |
| ExternalIdSet | `public static function ExternalIdSet(array $Payload, array $Context)` | `PyOS.PatientExternalIdSet()` |
| ExternalIdGet | `public static function ExternalIdGet(array $Payload, array $Context)` | `PyOS.PatientExternalIdGet()` |
| Reindex | `public static function Reindex(array $Payload, array $Context)` | `PyOS.PatientReindex()` |
### <span id="PyOS-payment"></span>PyOS.Payment
- **Purpose**: Unified payment orchestration engine for PyOS Multi-App, Multi-Tenant, Multi-Provider payment backbone Gateway-agnostic normalized payment lifecycle
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Payment.py](PyOS/Py.26.00.00/Engine/PyOS.Payment.py)
- **Class**: `PyOS_Payment`
- **Facade Entry Points**:
  - `PyOS.Payment()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.PaymentNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `PyOS.PaymentStatus()` → `public static function Status(array $Payload = [], array $Context = [])`
  - `PyOS.PaymentWebhook()` → `public static function Webhook(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Payment()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `PyOS.PaymentNew()` |
| Status | `public static function Status(array $Payload = [], array $Context = [])` | `PyOS.PaymentStatus()` |
| Webhook | `public static function Webhook(array $Payload = [], array $Context = [])` | `PyOS.PaymentWebhook()` |
### <span id="PyOS-payout"></span>PyOS.Payout
- **Purpose**: Unified outgoing funds engine (Payout / Withdraw / Settlement) Multi-App, Multi-Tenant, Multi-Gateway Async-first, Idempotent, Audit-proof Highlights     : Loader + EngineRouter v7.0 compatible No direct IO, DB, Gateway calls Uses OS Engines for Security, Wallet, Ledger, Queue Dependencies   : PyOS.Security PyOS.Auth PyOS.Role PyOS.Tenant PyOS.Wallet PyOS.Balance PyOS.Ledger PyOS.Transaction PyOS.Lock PyOS.Queue PyOS.Audit PyOS.Log PyOS.Monitor Usage: PyOS::PayoutRequest([ ... ]) PyOS::PayoutStatus([ 'PayoutId' => '...' ]) ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Payout.py](PyOS/Py.26.00.00/Engine/PyOS.Payout.py)
- **Class**: `PyOS_Payout`
- **Facade Entry Points**:
  - `PyOS.Payout()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PayoutRequest()` → `public static function Request(array $Payload, array $Context)`
  - `PyOS.PayoutStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.PayoutCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.PayoutRetry()` → `public static function Retry(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Payout()` |
| Request | `public static function Request(array $Payload, array $Context)` | `PyOS.PayoutRequest()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.PayoutStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.PayoutCancel()` |
| Retry | `public static function Retry(array $Payload, array $Context)` | `PyOS.PayoutRetry()` |
### <span id="PyOS-payroll"></span>PyOS.Payroll
- **Purpose**: Enterprise-grade Payroll Operating Engine Multi-App, Multi-Tenant, Policy-Driven Payroll Deterministic, Auditable, High-Performance
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Payroll.py](PyOS/Py.26.00.00/Engine/PyOS.Payroll.py)
- **Class**: `PyOS_Payroll`
- **Facade Entry Points**:
  - `PyOS.Payroll()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PayrollStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.PayrollSetup()` → `public static function Setup(array $Payload, array $Context)`
  - `PyOS.PayrollRunNew()` → `public static function RunNew(array $Payload, array $Context)`
  - `PyOS.PayrollRunCompute()` → `public static function RunCompute(array $Payload, array $Context)`
  - `PyOS.PayrollRunLock()` → `public static function RunLock(array $Payload, array $Context)`
  - `PyOS.PayrollPayslipGenerate()` → `public static function PayslipGenerate(array $Payload, array $Context)`
  - `PyOS.PayrollPayoutCreate()` → `public static function PayoutCreate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Payroll()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.PayrollStatus()` |
| Setup | `public static function Setup(array $Payload, array $Context)` | `PyOS.PayrollSetup()` |
| RunNew | `public static function RunNew(array $Payload, array $Context)` | `PyOS.PayrollRunNew()` |
| RunCompute | `public static function RunCompute(array $Payload, array $Context)` | `PyOS.PayrollRunCompute()` |
| RunLock | `public static function RunLock(array $Payload, array $Context)` | `PyOS.PayrollRunLock()` |
| PayslipGenerate | `public static function PayslipGenerate(array $Payload, array $Context)` | `PyOS.PayrollPayslipGenerate()` |
| PayoutCreate | `public static function PayoutCreate(array $Payload, array $Context)` | `PyOS.PayrollPayoutCreate()` |
### <span id="PyOS-performance"></span>PyOS.Performance
- **Purpose**: PyOS High-Performance Operating System Engine Measure, track, score and optimize runtime performance Request → Unit → Span → Metrics lifecycle
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Performance.py](PyOS/Py.26.00.00/Engine/PyOS.Performance.py)
- **Class**: `PyOS_Performance`
- **Facade Entry Points**:
  - `PyOS.Performance()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PerformanceStart()` → `public static function Start(array $Payload, array $Context)`
  - `PyOS.PerformanceEnd()` → `public static function End(array $Payload, array $Context)`
  - `PyOS.PerformanceSpanStart()` → `public static function SpanStart(array $Payload, array $Context)`
  - `PyOS.PerformanceSpanEnd()` → `public static function SpanEnd(array $Payload, array $Context)`
  - `PyOS.PerformanceCounterInc()` → `public static function CounterInc(array $Payload, array $Context)`
  - `PyOS.PerformanceGaugeSet()` → `public static function GaugeSet(array $Payload, array $Context)`
  - `PyOS.PerformanceSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`
  - `PyOS.PerformanceScore()` → `public static function Score(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Performance()` |
| Start | `public static function Start(array $Payload, array $Context)` | `PyOS.PerformanceStart()` |
| End | `public static function End(array $Payload, array $Context)` | `PyOS.PerformanceEnd()` |
| SpanStart | `public static function SpanStart(array $Payload, array $Context)` | `PyOS.PerformanceSpanStart()` |
| SpanEnd | `public static function SpanEnd(array $Payload, array $Context)` | `PyOS.PerformanceSpanEnd()` |
| CounterInc | `public static function CounterInc(array $Payload, array $Context)` | `PyOS.PerformanceCounterInc()` |
| GaugeSet | `public static function GaugeSet(array $Payload, array $Context)` | `PyOS.PerformanceGaugeSet()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `PyOS.PerformanceSnapshot()` |
| Score | `public static function Score(array $Payload, array $Context)` | `PyOS.PerformanceScore()` |
### <span id="PyOS-permission"></span>PyOS.Permission
- **Purpose**: Enterprise Grade Permission Engine for PyOS (Multi-App + Multi-Tenant) Central permission gate for API/Cron/Webhook/Assets and business actions RBAC-first (Role Based) with ABAC/Policy hooks-ready architecture High-performance: cached permission matrix, deterministic decisions
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Permission.py](PyOS/Py.26.00.00/Engine/PyOS.Permission.py)
- **Class**: `PyOS_Permission`
- **Facade Entry Points**:
  - `PyOS.Permission()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PermissionCheck()` → `public static function Check(array $Payload, array $Context)`
  - `PyOS.PermissionCan()` → `public static function Can(array $Payload, array $Context)`
  - `PyOS.PermissionAssert()` → `public static function Assert(array $Payload, array $Context)`
  - `PyOS.PermissionMatrix()` → `public static function Matrix(array $Payload, array $Context)`
  - `PyOS.PermissionWarmUp()` → `public static function WarmUp(array $Payload, array $Context)`
  - `PyOS.PermissionInvalidate()` → `public static function Invalidate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Permission()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.PermissionCheck()` |
| Can | `public static function Can(array $Payload, array $Context)` | `PyOS.PermissionCan()` |
| Assert | `public static function Assert(array $Payload, array $Context)` | `PyOS.PermissionAssert()` |
| Matrix | `public static function Matrix(array $Payload, array $Context)` | `PyOS.PermissionMatrix()` |
| WarmUp | `public static function WarmUp(array $Payload, array $Context)` | `PyOS.PermissionWarmUp()` |
| Invalidate | `public static function Invalidate(array $Payload, array $Context)` | `PyOS.PermissionInvalidate()` |
### <span id="PyOS-pipeline"></span>PyOS.Pipeline
- **Purpose**: OS-level Pipeline / Workflow Engine Deterministic, idempotent, resumable execution Multi-Tenant + Multi-App safe Sync + Async (Queue/Worker) execution Enterprise observability (Log, Audit, Trace, Metric)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Pipeline.py](PyOS/Py.26.00.00/Engine/PyOS.Pipeline.py)
- **Class**: `PyOS_Pipeline`
- **Facade Entry Points**:
  - `PyOS.Pipeline()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PipelineDefine()` → `public static function Define(array $Payload, array $Context)`
  - `PyOS.PipelineRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.PipelineResume()` → `public static function Resume(array $Payload, array $Context)`
  - `PyOS.PipelineStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.PipelineCancel()` → `public static function Cancel(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Pipeline()` |
| Define | `public static function Define(array $Payload, array $Context)` | `PyOS.PipelineDefine()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.PipelineRun()` |
| Resume | `public static function Resume(array $Payload, array $Context)` | `PyOS.PipelineResume()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.PipelineStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.PipelineCancel()` |
### <span id="PyOS-planner"></span>PyOS.Planner
- **Purpose**: Convert Intent → Deterministic Plan (execution-ready blueprint) Act as OS-level orchestration brain for PyOS Support Multi-App, Multi-Brand, Multi-Tenant planning Enforce Security, Policy, Role, Audit at planning-time
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Planner.py](PyOS/Py.26.00.00/Engine/PyOS.Planner.py)
- **Class**: `PyOS_Planner`
- **Facade Entry Points**:
  - `PyOS.Planner()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PlannerNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.PlannerBuild()` → `public static function Build(array $Payload, array $Context)`
  - `PyOS.PlannerValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.PlannerExport()` → `public static function Export(array $Payload, array $Context)`
  - `PyOS.PlannerStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Planner()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.PlannerNew()` |
| Build | `public static function Build(array $Payload, array $Context)` | `PyOS.PlannerBuild()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.PlannerValidate()` |
| Export | `public static function Export(array $Payload, array $Context)` | `PyOS.PlannerExport()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.PlannerStatus()` |
### <span id="PyOS-policy"></span>PyOS.Policy
- **Purpose**: Central Policy Engine for PyOS (System Constitution) Access, Security, Rate, Cache, Feature, Compliance policies Multi-Tenant + Multi-App + Multi-Brand aware Deterministic, Explainable, Auditable decisions
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Policy.py](PyOS/Py.26.00.00/Engine/PyOS.Policy.py)
- **Class**: `PyOS_Policy`
- **Facade Entry Points**:
  - `PyOS.Policy()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PolicyEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `PyOS.PolicyEnforce()` → `public static function Enforce(array $Payload, array $Context)`
  - `PyOS.PolicyExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Policy()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `PyOS.PolicyEvaluate()` |
| Enforce | `public static function Enforce(array $Payload, array $Context)` | `PyOS.PolicyEnforce()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.PolicyExplain()` |
### <span id="PyOS-policyai"></span>PyOS.PolicyAI
- **Purpose**: Central Policy + Decision Engine for PyOS Deterministic + AI-assisted policy evaluation Multi-App, Multi-Brand, Multi-Tenant safe decisions
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.PolicyAI.py](PyOS/Py.26.00.00/Engine/PyOS.PolicyAI.py)
- **Class**: `PyOS_PolicyAI`
- **Facade Entry Points**:
  - `PyOS.PolicyAI()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PolicyAIEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `PyOS.PolicyAICompile()` → `public static function Compile(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.PolicyAI()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `PyOS.PolicyAIEvaluate()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `PyOS.PolicyAICompile()` |
### <span id="PyOS-post"></span>PyOS.Post
- **Purpose**: Universal Content / Post Engine for PyOS Supports Blog, News, Docs, Articles, CMS, Headless APIs Enterprise Grade + Multi-App + Multi-Tenant safe
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Post.py](PyOS/Py.26.00.00/Engine/PyOS.Post.py)
- **Class**: `PyOS_Post`
- **Facade Entry Points**:
  - `PyOS.Post()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PostNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.PostGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.PostList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.PostPublish()` → `public static function Publish(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Post()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.PostNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.PostGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.PostList()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.PostPublish()` |
### <span id="PyOS-predict"></span>PyOS.Predict
- **Purpose**: Enterprise-grade prediction engine for PyOS Works across all Apps, Tenants, Brands Deterministic, auditable, cache-safe predictions Designed for AI / ML / Statistical hybrid models
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Predict.py](PyOS/Py.26.00.00/Engine/PyOS.Predict.py)
- **Class**: `PyOS_Predict`
- **Facade Entry Points**:
  - `PyOS.Predict()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.PredictRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `PyOS.PredictBatch()` → `public static function Batch(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Predict()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `PyOS.PredictRun()` |
| Batch | `public static function Batch(array $Payload = [], array $Context = [])` | `PyOS.PredictBatch()` |
### <span id="PyOS-prescription"></span>PyOS.Prescription
- **Purpose**: Enterprise-grade Prescription Management Engine Multi-App, Multi-Tenant, Multi-Role compatible Secure, auditable, high-performance medical workflow core
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Prescription.py](PyOS/Py.26.00.00/Engine/PyOS.Prescription.py)
- **Class**: `PyOS_Prescription`
- **Facade Entry Points**:
  - `PyOS.Prescription()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PrescriptionNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.PrescriptionUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.PrescriptionGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.PrescriptionList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.PrescriptionIssue()` → `public static function Issue(array $Payload, array $Context)`
  - `PyOS.PrescriptionSign()` → `public static function Sign(array $Payload, array $Context)`
  - `PyOS.PrescriptionCancel()` → `public static function Cancel(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Prescription()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.PrescriptionNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.PrescriptionUpdate()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.PrescriptionGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.PrescriptionList()` |
| Issue | `public static function Issue(array $Payload, array $Context)` | `PyOS.PrescriptionIssue()` |
| Sign | `public static function Sign(array $Payload, array $Context)` | `PyOS.PrescriptionSign()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.PrescriptionCancel()` |
### <span id="PyOS-product"></span>PyOS.Product
- **Purpose**: Universal Product / Catalog Engine for PyOS Multi-App + Multi-Tenant + Enterprise-Grade Supports Products, Variants, Pricing, Inventory, Media, SEO Backend Operating System level abstraction (not CRUD)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Product.py](PyOS/Py.26.00.00/Engine/PyOS.Product.py)
- **Class**: `PyOS_Product`
- **Facade Entry Points**:
  - `PyOS.Product()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ProductNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ProductGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ProductUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.ProductStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Product()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ProductNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ProductGet()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.ProductUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.ProductStatus()` |
### <span id="PyOS-profile"></span>PyOS.Profile
- **Purpose**: Universal Profile Engine for PyOS Multi-App + Multi-Tenant + Multi-Brand user identity layer Public / Private / Admin profile isolation Schema-driven, cache-safe, audit-ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Profile.py](PyOS/Py.26.00.00/Engine/PyOS.Profile.py)
- **Class**: `PyOS_Profile`
- **Facade Entry Points**:
  - `PyOS.Profile()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ProfileGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ProfileSet()` → `public static function Set(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Profile()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ProfileGet()` |
| Set | `public static function Set(array $Payload, array $Context)` | `PyOS.ProfileSet()` |
### <span id="PyOS-profiler"></span>PyOS.Profiler
- **Purpose**: High-performance profiling engine for PyOS Measure execution time, memory, spans, violations Feed data to Monitor, Metric, Trace, Log engines Multi-Tenant + Multi-App safe Dependencies   : PyOS.Loader (static facade) PyOS.EngineRouter v7.0 Engines: Security, Cache, Monitor, Metric, Log, Queue Notes: Safe-mode always ON (never breaks production) Sampling + buffering to reduce overhead ============================================================================
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Profiler.py](PyOS/Py.26.00.00/Engine/PyOS.Profiler.py)
- **Class**: `PyOS_Profiler`
- **Facade Entry Points**:
  - `PyOS.Profiler()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ProfilerStart()` → `public static function Start(array $Payload, array $Context)`
  - `PyOS.ProfilerStop()` → `public static function Stop(array $Payload, array $Context)`
  - `PyOS.ProfilerSpanStart()` → `public static function SpanStart(array $Payload, array $Context)`
  - `PyOS.ProfilerSpanStop()` → `public static function SpanStop(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Profiler()` |
| Start | `public static function Start(array $Payload, array $Context)` | `PyOS.ProfilerStart()` |
| Stop | `public static function Stop(array $Payload, array $Context)` | `PyOS.ProfilerStop()` |
| SpanStart | `public static function SpanStart(array $Payload, array $Context)` | `PyOS.ProfilerSpanStart()` |
| SpanStop | `public static function SpanStop(array $Payload, array $Context)` | `PyOS.ProfilerSpanStop()` |
### <span id="PyOS-push"></span>PyOS.Push
- **Purpose**: Unified Push Notification Engine for PyOS Supports Multi-App, Multi-Tenant architecture OS-grade abstraction over push delivery Queue-ready, Provider-agnostic, Secure by design
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Push.py](PyOS/Py.26.00.00/Engine/PyOS.Push.py)
- **Class**: `PyOS_Push`
- **Facade Entry Points**:
  - `PyOS.Push()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.PushRegisterDevice()` → `public static function RegisterDevice(array $Payload, array $Context)`
  - `PyOS.PushUnregisterDevice()` → `public static function UnregisterDevice(array $Payload, array $Context)`
  - `PyOS.PushSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.PushStats()` → `public static function Stats(array $Payload, array $Context)`
  - `PyOS.PushHealth()` → `public static function Health(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Push()` |
| RegisterDevice | `public static function RegisterDevice(array $Payload, array $Context)` | `PyOS.PushRegisterDevice()` |
| UnregisterDevice | `public static function UnregisterDevice(array $Payload, array $Context)` | `PyOS.PushUnregisterDevice()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.PushSend()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `PyOS.PushStats()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.PushHealth()` |
### <span id="PyOS-quarantine"></span>PyOS.Quarantine
- **Purpose**: Central isolation engine for risky entities Multi-App + Multi-Tenant safe quarantine system Works as ACTION layer after detection engines
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Quarantine.py](PyOS/Py.26.00.00/Engine/PyOS.Quarantine.py)
- **Class**: `PyOS_Quarantine`
- **Facade Entry Points**:
  - `PyOS.Quarantine()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.QuarantineCheck()` → `public static function Check(array $Payload, array $Context)`
  - `PyOS.QuarantinePut()` → `public static function Put(array $Payload, array $Context)`
  - `PyOS.QuarantineRelease()` → `public static function Release(array $Payload, array $Context)`
  - `PyOS.QuarantineHit()` → `public static function Hit(array $Entity, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Quarantine()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.QuarantineCheck()` |
| Put | `public static function Put(array $Payload, array $Context)` | `PyOS.QuarantinePut()` |
| Release | `public static function Release(array $Payload, array $Context)` | `PyOS.QuarantineRelease()` |
| Hit | `public static function Hit(array $Entity, array $Context)` | `PyOS.QuarantineHit()` |
### <span id="PyOS-query"></span>PyOS.Query
- **Purpose**: Secure, Multi-Tenant, Multi-App Query Operating System SQL abstraction with strict safety & performance rules Zero raw SQL by default Prepared statements only Automatic Tenant enforcement Read / Write discipline
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Query.py](PyOS/Py.26.00.00/Engine/PyOS.Query.py)
- **Class**: `PyOS_Query`
- **Facade Entry Points**:
  - `PyOS.Query()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.QuerySelect()` → `public static function Select(array $Payload, array $Context)`
  - `PyOS.QueryInsert()` → `public static function Insert(array $Payload, array $Context)`
  - `PyOS.QueryUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.QueryDelete()` → `public static function Delete(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Query()` |
| Select | `public static function Select(array $Payload, array $Context)` | `PyOS.QuerySelect()` |
| Insert | `public static function Insert(array $Payload, array $Context)` | `PyOS.QueryInsert()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.QueryUpdate()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.QueryDelete()` |
### <span id="PyOS-queryai"></span>PyOS.QueryAI
- **Purpose**: Intelligent Query Engine for PyOS Natural Language → Secure Query Plan → Retrieval → Answer Multi-App + Multi-Tenant + Permission-Aware by default
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.QueryAI.py](PyOS/Py.26.00.00/Engine/PyOS.QueryAI.py)
- **Class**: `PyOS_QueryAI`
- **Facade Entry Points**:
  - `PyOS.QueryAI()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.QueryAIPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.QueryAIAsk()` → `public static function Ask(array $Payload, array $Context)`
  - `PyOS.QueryAIExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.QueryAI()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.QueryAIPlan()` |
| Ask | `public static function Ask(array $Payload, array $Context)` | `PyOS.QueryAIAsk()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.QueryAIExplain()` |
### <span id="PyOS-queue"></span>PyOS.Queue
- **Purpose**: Enterprise-grade Queue Engine for PyOS (Multi-App + Multi-Tenant) High-performance job enqueue, reserve/lease, ack, fail, retry, DLQ OS-level async backbone for APIs, Cron, Webhooks, Pipelines, AI tasks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Queue.py](PyOS/Py.26.00.00/Engine/PyOS.Queue.py)
- **Class**: `PyOS_Queue`
- **Facade Entry Points**:
  - `PyOS.Queue()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.QueuePush()` → `public static function Push($Payload = [], $Context = [])`
  - `PyOS.QueueLater()` → `public static function Later($Payload = [], $Context = [])`
  - `PyOS.QueueSchedule()` → `public static function Schedule($Payload = [], $Context = [])`
  - `PyOS.QueueBatch()` → `public static function Batch($Payload = [], $Context = [])`
  - `PyOS.QueueReserve()` → `public static function Reserve($Payload = [], $Context = [])`
  - `PyOS.QueueAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `PyOS.QueueFail()` → `public static function Fail($Payload = [], $Context = [])`
  - `PyOS.QueueRetry()` → `public static function Retry($Payload = [], $Context = [])`
  - `PyOS.QueueCancel()` → `public static function Cancel($Payload = [], $Context = [])`
  - `PyOS.QueueStatus()` → `public static function Status($Payload = [], $Context = [])`
  - `PyOS.QueueStats()` → `public static function Stats($Payload = [], $Context = [])`
  - `PyOS.QueueDLQ()` → `public static function DLQ($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Queue()` |
| Push | `public static function Push($Payload = [], $Context = [])` | `PyOS.QueuePush()` |
| Later | `public static function Later($Payload = [], $Context = [])` | `PyOS.QueueLater()` |
| Schedule | `public static function Schedule($Payload = [], $Context = [])` | `PyOS.QueueSchedule()` |
| Batch | `public static function Batch($Payload = [], $Context = [])` | `PyOS.QueueBatch()` |
| Reserve | `public static function Reserve($Payload = [], $Context = [])` | `PyOS.QueueReserve()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `PyOS.QueueAck()` |
| Fail | `public static function Fail($Payload = [], $Context = [])` | `PyOS.QueueFail()` |
| Retry | `public static function Retry($Payload = [], $Context = [])` | `PyOS.QueueRetry()` |
| Cancel | `public static function Cancel($Payload = [], $Context = [])` | `PyOS.QueueCancel()` |
| Status | `public static function Status($Payload = [], $Context = [])` | `PyOS.QueueStatus()` |
| Stats | `public static function Stats($Payload = [], $Context = [])` | `PyOS.QueueStats()` |
| DLQ | `public static function DLQ($Payload = [], $Context = [])` | `PyOS.QueueDLQ()` |
### <span id="PyOS-ratelimit"></span>PyOS.RateLimit
- **Purpose**: Enterprise-grade Rate Limiting for PyOS Units (API/Webhook/Cron/etc.) Multi-App + Multi-Tenant isolation via Context-aware keys High-performance counters with safe fallbacks Loader-only usage: callable exclusively via PyOS::RateLimit*()
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.RateLimit.py](PyOS/Py.26.00.00/Engine/PyOS.RateLimit.py)
- **Class**: `PyOS_RateLimit`
- **Facade Entry Points**:
  - `PyOS.RateLimit()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.RateLimitAllow()` → `public static function Allow(array $Payload = [], array $Context = [])`
  - `PyOS.RateLimitHit()` → `public static function Hit(array $Payload = [], array $Context = [])`
  - `PyOS.RateLimitInfo()` → `public static function Info(array $Payload = [], array $Context = [])`
  - `PyOS.RateLimitReset()` → `public static function Reset(array $Payload = [], array $Context = [])`
  - `PyOS.RateLimitPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `PyOS.RateLimitKey()` → `public static function Key(array $Payload = [], array $Context = [], array $Policy = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.RateLimit()` |
| Allow | `public static function Allow(array $Payload = [], array $Context = [])` | `PyOS.RateLimitAllow()` |
| Hit | `public static function Hit(array $Payload = [], array $Context = [])` | `PyOS.RateLimitHit()` |
| Info | `public static function Info(array $Payload = [], array $Context = [])` | `PyOS.RateLimitInfo()` |
| Reset | `public static function Reset(array $Payload = [], array $Context = [])` | `PyOS.RateLimitReset()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `PyOS.RateLimitPolicy()` |
| Key | `public static function Key(array $Payload = [], array $Context = [], array $Policy = [])` | `PyOS.RateLimitKey()` |
### <span id="PyOS-recommend"></span>PyOS.Recommend
- **Purpose**: Universal Recommendation Engine for PyOS (Multi-App + Multi-Tenant) Provide "Suggest / Track / Profile / Explain" as a backend OS capability Safe, deterministic, high-performance defaults (cache-first, DB-optional)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Recommend.py](PyOS/Py.26.00.00/Engine/PyOS.Recommend.py)
- **Class**: `PyOS_Recommend`
- **Facade Entry Points**:
  - `PyOS.Recommend()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.RecommendSuggest()` → `public static function Suggest($Payload = [], $Context = [])`
  - `PyOS.RecommendTrack()` → `public static function Track($Payload = [], $Context = [])`
  - `PyOS.RecommendProfile()` → `public static function Profile($Payload = [], $Context = [])`
  - `PyOS.RecommendExplain()` → `public static function Explain($Payload = [], $Context = [])`
  - `PyOS.RecommendWarm()` → `public static function Warm($Payload = [], $Context = [])`
  - `PyOS.RecommendInvalidate()` → `public static function Invalidate($Payload = [], $Context = [])`
  - `PyOS.RecommendModel()` → `public static function Model($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Recommend()` |
| Suggest | `public static function Suggest($Payload = [], $Context = [])` | `PyOS.RecommendSuggest()` |
| Track | `public static function Track($Payload = [], $Context = [])` | `PyOS.RecommendTrack()` |
| Profile | `public static function Profile($Payload = [], $Context = [])` | `PyOS.RecommendProfile()` |
| Explain | `public static function Explain($Payload = [], $Context = [])` | `PyOS.RecommendExplain()` |
| Warm | `public static function Warm($Payload = [], $Context = [])` | `PyOS.RecommendWarm()` |
| Invalidate | `public static function Invalidate($Payload = [], $Context = [])` | `PyOS.RecommendInvalidate()` |
| Model | `public static function Model($Payload = [], $Context = [])` | `PyOS.RecommendModel()` |
### <span id="PyOS-recovery"></span>PyOS.Recovery
- **Purpose**: Enterprise-grade Account Recovery Engine Password reset, access regain, emergency lockdown Multi-App + Multi-Tenant + High-Security recovery workflows
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Recovery.py](PyOS/Py.26.00.00/Engine/PyOS.Recovery.py)
- **Class**: `PyOS_Recovery`
- **Facade Entry Points**:
  - `PyOS.Recovery()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RecoveryRequest()` → `public static function Request(array $Payload, array $Context)`
  - `PyOS.RecoveryVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.RecoveryConsume()` → `public static function Consume(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Recovery()` |
| Request | `public static function Request(array $Payload, array $Context)` | `PyOS.RecoveryRequest()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.RecoveryVerify()` |
| Consume | `public static function Consume(array $Payload, array $Context)` | `PyOS.RecoveryConsume()` |
### <span id="PyOS-recruitment"></span>PyOS.Recruitment
- **Purpose**: Enterprise-grade Recruitment / ATS backend engine Multi-Tenant + Multi-App + Multi-Org safe Works as a core OS-level backend service Fully compatible with PyOS.Loader + EngineRouter v7.0
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Recruitment.py](PyOS/Py.26.00.00/Engine/PyOS.Recruitment.py)
- **Class**: `PyOS_Recruitment`
- **Facade Entry Points**:
  - `PyOS.Recruitment()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RecruitmentRequisitionNew()` → `public static function RequisitionNew(array $Payload, array $Context)`
  - `PyOS.RecruitmentJobPublish()` → `public static function JobPublish(array $Payload, array $Context)`
  - `PyOS.RecruitmentCandidateNew()` → `public static function CandidateNew(array $Payload, array $Context)`
  - `PyOS.RecruitmentApplicationStageSet()` → `public static function ApplicationStageSet(array $Payload, array $Context)`
  - `PyOS.RecruitmentDashboardGet()` → `public static function DashboardGet(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Recruitment()` |
| RequisitionNew | `public static function RequisitionNew(array $Payload, array $Context)` | `PyOS.RecruitmentRequisitionNew()` |
| JobPublish | `public static function JobPublish(array $Payload, array $Context)` | `PyOS.RecruitmentJobPublish()` |
| CandidateNew | `public static function CandidateNew(array $Payload, array $Context)` | `PyOS.RecruitmentCandidateNew()` |
| ApplicationStageSet | `public static function ApplicationStageSet(array $Payload, array $Context)` | `PyOS.RecruitmentApplicationStageSet()` |
| DashboardGet | `public static function DashboardGet(array $Payload, array $Context)` | `PyOS.RecruitmentDashboardGet()` |
### <span id="PyOS-refund"></span>PyOS.Refund
- **Purpose**: Enterprise Grade Refund Management Engine Multi-App + Multi-Tenant safe by Context High-performance state machine + audit timeline Idempotent request support (RefundLock) Provider-ready (Gateway/Wallet/StoreCredit routing)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Refund.py](PyOS/Py.26.00.00/Engine/PyOS.Refund.py)
- **Class**: `PyOS_Refund`
- **Facade Entry Points**:
  - `PyOS.Refund()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RefundHelp()` → `public static function Help(array $Payload, array $Context)`
  - `PyOS.RefundNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.RefundGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.RefundList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.RefundUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.RefundApprove()` → `public static function Approve(array $Payload, array $Context)`
  - `PyOS.RefundReject()` → `public static function Reject(array $Payload, array $Context)`
  - `PyOS.RefundCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.RefundExecute()` → `public static function Execute(array $Payload, array $Context)`
  - `PyOS.RefundValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.RefundCalculate()` → `public static function Calculate(array $Payload, array $Context)`
  - `PyOS.RefundTimeline()` → `public static function Timeline(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Refund()` |
| Help | `public static function Help(array $Payload, array $Context)` | `PyOS.RefundHelp()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.RefundNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.RefundGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.RefundList()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.RefundUpdate()` |
| Approve | `public static function Approve(array $Payload, array $Context)` | `PyOS.RefundApprove()` |
| Reject | `public static function Reject(array $Payload, array $Context)` | `PyOS.RefundReject()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.RefundCancel()` |
| Execute | `public static function Execute(array $Payload, array $Context)` | `PyOS.RefundExecute()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.RefundValidate()` |
| Calculate | `public static function Calculate(array $Payload, array $Context)` | `PyOS.RefundCalculate()` |
| Timeline | `public static function Timeline(array $Payload, array $Context)` | `PyOS.RefundTimeline()` |
### <span id="PyOS-region"></span>PyOS.Region
- **Purpose**: Region / Geo Intelligence as OS-level capability (Multi-App + Multi-Tenant) Canonical region resolution (Country / Subdivision / City / ZIP) Policy overlay merge (App → Brand → Tenant → SDK → Core) Safe caching + compiled indexes for ultra performance
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Region.py](PyOS/Py.26.00.00/Engine/PyOS.Region.py)
- **Class**: `PyOS_Region`
- **Facade Entry Points**:
  - `PyOS.Region()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.RegionGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.RegionResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.RegionDetect()` → `public static function Detect(array $Payload = [], array $Context = [])`
  - `PyOS.RegionPolicy()` → `public static function Policy(array $Payload = [], array $Context = [])`
  - `PyOS.RegionIsAllowed()` → `public static function IsAllowed(array $Payload = [], array $Context = [])`
  - `PyOS.RegionIndexBuild()` → `public static function IndexBuild(array $Payload = [], array $Context = [])`
  - `PyOS.RegionIndexClear()` → `public static function IndexClear(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Region()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.RegionGet()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.RegionResolve()` |
| Detect | `public static function Detect(array $Payload = [], array $Context = [])` | `PyOS.RegionDetect()` |
| Policy | `public static function Policy(array $Payload = [], array $Context = [])` | `PyOS.RegionPolicy()` |
| IsAllowed | `public static function IsAllowed(array $Payload = [], array $Context = [])` | `PyOS.RegionIsAllowed()` |
| IndexBuild | `public static function IndexBuild(array $Payload = [], array $Context = [])` | `PyOS.RegionIndexBuild()` |
| IndexClear | `public static function IndexClear(array $Payload = [], array $Context = [])` | `PyOS.RegionIndexClear()` |
### <span id="PyOS-release"></span>PyOS.Release
- **Purpose**: Enterprise-grade Release Lifecycle Engine for PyOS Multi-App + Multi-Tenant + Multi-Strategy Releases Deterministic, Auditable, Rollbackable OS-level Releases
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Release.py](PyOS/Py.26.00.00/Engine/PyOS.Release.py)
- **Class**: `PyOS_Release`
- **Facade Entry Points**:
  - `PyOS.Release()` → `public static function Main($Payload, $Context)`
  - `PyOS.ReleaseNew()` → `public static function New($Payload, $Context)`
  - `PyOS.ReleasePlan()` → `public static function Plan($Payload, $Context)`
  - `PyOS.ReleaseApprove()` → `public static function Approve($Payload, $Context)`
  - `PyOS.ReleaseDeploy()` → `public static function Deploy($Payload, $Context)`
  - `PyOS.ReleaseVerify()` → `public static function Verify($Payload, $Context)`
  - `PyOS.ReleaseRollback()` → `public static function Rollback($Payload, $Context)`
  - `PyOS.ReleaseStatus()` → `public static function Status($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.Release()` |
| New | `public static function New($Payload, $Context)` | `PyOS.ReleaseNew()` |
| Plan | `public static function Plan($Payload, $Context)` | `PyOS.ReleasePlan()` |
| Approve | `public static function Approve($Payload, $Context)` | `PyOS.ReleaseApprove()` |
| Deploy | `public static function Deploy($Payload, $Context)` | `PyOS.ReleaseDeploy()` |
| Verify | `public static function Verify($Payload, $Context)` | `PyOS.ReleaseVerify()` |
| Rollback | `public static function Rollback($Payload, $Context)` | `PyOS.ReleaseRollback()` |
| Status | `public static function Status($Payload, $Context)` | `PyOS.ReleaseStatus()` |
### <span id="PyOS-relevance"></span>PyOS.Relevance
- **Purpose**: Enterprise-grade relevance scoring engine Deterministic + explainable relevance for any entity Multi-App, Multi-Tenant, Multi-Mode (search/feed/recommend) Works as core intelligence layer for Search, Discovery, Ranking
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Relevance.py](PyOS/Py.26.00.00/Engine/PyOS.Relevance.py)
- **Class**: `PyOS_Relevance`
- **Facade Entry Points**:
  - `PyOS.Relevance()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.RelevanceSignals()` → `public static function Signals(array $Input)`
  - `PyOS.RelevanceWeights()` → `public static function Weights(array $Input)`
  - `PyOS.RelevanceScore()` → `public static function Score(array $Signals, array $Weights, array $Input)`
  - `PyOS.RelevanceCacheKey()` → `public static function CacheKey(array $Input)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Relevance()` |
| Signals | `public static function Signals(array $Input)` | `PyOS.RelevanceSignals()` |
| Weights | `public static function Weights(array $Input)` | `PyOS.RelevanceWeights()` |
| Score | `public static function Score(array $Signals, array $Weights, array $Input)` | `PyOS.RelevanceScore()` |
| CacheKey | `public static function CacheKey(array $Input)` | `PyOS.RelevanceCacheKey()` |
### <span id="PyOS-replica"></span>PyOS.Replica
- **Purpose**: Read / Write DB replica routing for PyOS Multi-App + Multi-Tenant aware Lag-aware, policy-driven replica selection Fail-safe primary fallback
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Replica.py](PyOS/Py.26.00.00/Engine/PyOS.Replica.py)
- **Class**: `PyOS_Replica`
- **Facade Entry Points**:
  - `PyOS.Replica()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ReplicaSelect()` → `public static function Select(array $Payload, array $Context)`
  - `PyOS.ReplicaPrimary()` → `public static function Primary(array $Payload, array $Context)`
  - `PyOS.ReplicaReplica()` → `public static function Replica(array $Payload, array $Context)`
  - `PyOS.ReplicaAfterWrite()` → `public static function AfterWrite(array $Payload, array $Context)`
  - `PyOS.ReplicaHealthCheck()` → `public static function HealthCheck(array $Payload, array $Context)`
  - `PyOS.ReplicaStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Replica()` |
| Select | `public static function Select(array $Payload, array $Context)` | `PyOS.ReplicaSelect()` |
| Primary | `public static function Primary(array $Payload, array $Context)` | `PyOS.ReplicaPrimary()` |
| Replica | `public static function Replica(array $Payload, array $Context)` | `PyOS.ReplicaReplica()` |
| AfterWrite | `public static function AfterWrite(array $Payload, array $Context)` | `PyOS.ReplicaAfterWrite()` |
| HealthCheck | `public static function HealthCheck(array $Payload, array $Context)` | `PyOS.ReplicaHealthCheck()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `PyOS.ReplicaStats()` |
### <span id="PyOS-report"></span>PyOS.Report
- **Purpose**: Universal Reporting Engine for PyOS Multi-App, Multi-Tenant, Enterprise-Grade Reporting OS Layer Live + Async + Materialized report execution orchestration
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Report.py](PyOS/Py.26.00.00/Engine/PyOS.Report.py)
- **Class**: `PyOS_Report`
- **Facade Entry Points**:
  - `PyOS.Report()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ReportReportSpecNew()` → `public static function ReportSpecNew(array $Payload, array $Context)`
  - `PyOS.ReportReportSpecGet()` → `public static function ReportSpecGet(array $Payload, array $Context)`
  - `PyOS.ReportReportRun()` → `public static function ReportRun(array $Payload, array $Context)`
  - `PyOS.ReportReportExport()` → `public static function ReportExport(array $Payload, array $Context)`
  - `PyOS.ReportReportScheduleNew()` → `public static function ReportScheduleNew(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Report()` |
| ReportSpecNew | `public static function ReportSpecNew(array $Payload, array $Context)` | `PyOS.ReportReportSpecNew()` |
| ReportSpecGet | `public static function ReportSpecGet(array $Payload, array $Context)` | `PyOS.ReportReportSpecGet()` |
| ReportRun | `public static function ReportRun(array $Payload, array $Context)` | `PyOS.ReportReportRun()` |
| ReportExport | `public static function ReportExport(array $Payload, array $Context)` | `PyOS.ReportReportExport()` |
| ReportScheduleNew | `public static function ReportScheduleNew(array $Payload, array $Context)` | `PyOS.ReportReportScheduleNew()` |
### <span id="PyOS-restore"></span>PyOS.Restore
- **Purpose**: Enterprise-grade Restore Orchestration Engine Multi-App, Multi-Tenant, Safe & Auditable Restore Snapshot / PITR / Partial Restore Support Zero-downtime capable via Quarantine + Cutover
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Restore.py](PyOS/Py.26.00.00/Engine/PyOS.Restore.py)
- **Class**: `PyOS_Restore`
- **Facade Entry Points**:
  - `PyOS.Restore()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RestorePlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.RestoreRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.RestoreDryRun()` → `public static function DryRun(array $Payload, array $Context)`
  - `PyOS.RestoreVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.RestoreCutover()` → `public static function Cutover(array $Payload, array $Context)`
  - `PyOS.RestoreRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `PyOS.RestoreStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.RestoreCancel()` → `public static function Cancel(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Restore()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.RestorePlan()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.RestoreRun()` |
| DryRun | `public static function DryRun(array $Payload, array $Context)` | `PyOS.RestoreDryRun()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.RestoreVerify()` |
| Cutover | `public static function Cutover(array $Payload, array $Context)` | `PyOS.RestoreCutover()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.RestoreRollback()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.RestoreStatus()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.RestoreCancel()` |
### <span id="PyOS-result"></span>PyOS.Result
- **Purpose**: Enterprise-grade Result Management Engine Multi-App + Multi-Tenant safe High-performance, audit-ready, reproducible result system
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Result.py](PyOS/Py.26.00.00/Engine/PyOS.Result.py)
- **Class**: `PyOS_Result`
- **Facade Entry Points**:
  - `PyOS.Result()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ResultGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ResultNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ResultCalculate()` → `public static function Calculate(array $Payload, array $Context)`
  - `PyOS.ResultPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `PyOS.ResultLock()` → `public static function Lock(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Result()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ResultGet()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ResultNew()` |
| Calculate | `public static function Calculate(array $Payload, array $Context)` | `PyOS.ResultCalculate()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.ResultPublish()` |
| Lock | `public static function Lock(array $Payload, array $Context)` | `PyOS.ResultLock()` |
### <span id="PyOS-retry"></span>PyOS.Retry
- **Purpose**: Deterministic, policy-driven retry system Supports Inline, Deferred, Scheduled retry modes Enforces retry budget, idempotency, and safety Multi-App + Multi-Tenant safe
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Retry.py](PyOS/Py.26.00.00/Engine/PyOS.Retry.py)
- **Class**: `PyOS_Retry`
- **Facade Entry Points**:
  - `PyOS.Retry()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.RetryDo()` → `public static function Do(array $Payload = [], array $Context = [])`
  - `PyOS.RetryLater()` → `public static function Later(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Retry()` |
| Do | `public static function Do(array $Payload = [], array $Context = [])` | `PyOS.RetryDo()` |
| Later | `public static function Later(array $Payload = [], array $Context = [])` | `PyOS.RetryLater()` |
### <span id="PyOS-role"></span>PyOS.Role
- **Purpose**: Enterprise-grade Authorization Engine (Role + Optional Permission Gate) Multi-App + Multi-Tenant safe role resolution and checks Strict, deterministic, auditable decisions (Deny-by-default) Backward compatible with CSV roles: "Admin,Manager,Accountant"
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Role.py](PyOS/Py.26.00.00/Engine/PyOS.Role.py)
- **Class**: `PyOS_Role`
- **Facade Entry Points**:
  - `PyOS.Role()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.Role()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.RoleAllow()` → `public static function Allow(array $Payload = [], array $Context = [])`
  - `PyOS.RoleHas()` → `public static function Has(array $Payload = [], array $Context = [])`
  - `PyOS.RoleNormalize()` → `public static function Normalize(array $Payload = [], array $Context = [])`
  - `PyOS.RoleResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.RolePermission()` → `public static function Permission(array $Payload = [], array $Context = [])`
  - `PyOS.RoleExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`
  - `PyOS.RoleScope()` → `public static function Scope(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Role()` |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Role()` |
| Allow | `public static function Allow(array $Payload = [], array $Context = [])` | `PyOS.RoleAllow()` |
| Has | `public static function Has(array $Payload = [], array $Context = [])` | `PyOS.RoleHas()` |
| Normalize | `public static function Normalize(array $Payload = [], array $Context = [])` | `PyOS.RoleNormalize()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.RoleResolve()` |
| Permission | `public static function Permission(array $Payload = [], array $Context = [])` | `PyOS.RolePermission()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `PyOS.RoleExplain()` |
| Scope | `public static function Scope(array $Payload = [], array $Context = [])` | `PyOS.RoleScope()` |
### <span id="PyOS-rolehr"></span>PyOS.RoleHR
- **Purpose**: Enterprise-grade HR Role Management Engine Multi-Tenant + Multi-App + Multi-Scope role orchestration Authoritative HR role source for Permission & Auth engines
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.RoleHR.py](PyOS/Py.26.00.00/Engine/PyOS.RoleHR.py)
- **Class**: `PyOS_RoleHR`
- **Facade Entry Points**:
  - `PyOS.RoleHR()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RoleHRAssign()` → `public static function Assign(array $Payload, array $Context)`
  - `PyOS.RoleHRRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `PyOS.RoleHRCheck()` → `public static function Check(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.RoleHR()` |
| Assign | `public static function Assign(array $Payload, array $Context)` | `PyOS.RoleHRAssign()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `PyOS.RoleHRRevoke()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.RoleHRCheck()` |
### <span id="PyOS-root"></span>PyOS.Root
- **Purpose**: Global Root Orchestrator for PyOS Single entry governance layer for all Units (API, Cron, Webhook, etc.) Builds RootContext (Tenant, App, User, Policy, Unit) Applies security, policy, limits, cache rules Delegates execution to Executor Engine
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Root.py](PyOS/Py.26.00.00/Engine/PyOS.Root.py)
- **Class**: `PyOS_Root`
- **Facade Entry Points**:
  - `PyOS.Root()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RootDispatch()` → `public static function Dispatch(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Root()` |
| Dispatch | `public static function Dispatch(array $Payload, array $Context)` | `PyOS.RootDispatch()` |
### <span id="PyOS-row"></span>PyOS.Row
- **Purpose**: [] PyOS ka core CRUD engine. Enterprise-grade, multi-app, multi-tenant projects ke liye high-performance Row / Rows / Insert / Update / Status / Delete / Exists / Count / Sum / Transaction operations.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Row.py](PyOS/Py.26.00.00/Engine/PyOS.Row.py)
- **Class**: `PyOS_Row`
- **Facade Entry Points**: None documented
### <span id="PyOS-ruleengine"></span>PyOS.RuleEngine
- **Purpose**: Central Decision & Rule Evaluation Engine for PyOS Guard, Compute, Route, Policy & Workflow Rules Multi-App, Multi-Tenant, Multi-Brand aware High-performance, cache-first execution
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.RuleEngine.py](PyOS/Py.26.00.00/Engine/PyOS.RuleEngine.py)
- **Class**: `PyOS_RuleEngine`
- **Facade Entry Points**:
  - `PyOS.RuleEngine()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.RuleEngineEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `PyOS.RuleEngineGuard()` → `public static function Guard(array $Payload, array $Context)`
  - `PyOS.RuleEngineCompute()` → `public static function Compute(array $Payload, array $Context)`
  - `PyOS.RuleEngineExplain()` → `public static function Explain(array $Payload, array $Context)`
  - `PyOS.RuleEngineCompile()` → `public static function Compile(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.RuleEngine()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `PyOS.RuleEngineEvaluate()` |
| Guard | `public static function Guard(array $Payload, array $Context)` | `PyOS.RuleEngineGuard()` |
| Compute | `public static function Compute(array $Payload, array $Context)` | `PyOS.RuleEngineCompute()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.RuleEngineExplain()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `PyOS.RuleEngineCompile()` |
### <span id="PyOS-sandbox"></span>PyOS.Sandbox
- **Purpose**: Secure execution envelope for Units / APIs / Hooks / Plugins Enforce policy-based isolation (Multi-App + Multi-Tenant) Runtime guards: time, memory, output, scope Central sandbox for enterprise-grade backend OS
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Sandbox.py](PyOS/Py.26.00.00/Engine/PyOS.Sandbox.py)
- **Class**: `PyOS_Sandbox`
- **Facade Entry Points**:
  - `PyOS.Sandbox()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.SandboxRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `PyOS.SandboxPolicy()` → `public static function Policy(array $Payload, array $Context, string $Scope)`
  - `PyOS.SandboxAllow()` → `public static function Allow(array $Payload, array $Context, array $Policy)`
  - `PyOS.SandboxGuard()` → `public static function Guard(array $Policy)`
  - `PyOS.SandboxScope()` → `public static function Scope(array $Payload, array $Context)`
  - `PyOS.SandboxReport()` → `public static function Report(array $Payload, array $Context, array $Policy, $Result, string $Scope)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Sandbox()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `PyOS.SandboxRun()` |
| Policy | `public static function Policy(array $Payload, array $Context, string $Scope)` | `PyOS.SandboxPolicy()` |
| Allow | `public static function Allow(array $Payload, array $Context, array $Policy)` | `PyOS.SandboxAllow()` |
| Guard | `public static function Guard(array $Policy)` | `PyOS.SandboxGuard()` |
| Scope | `public static function Scope(array $Payload, array $Context)` | `PyOS.SandboxScope()` |
| Report | `public static function Report(array $Payload, array $Context, array $Policy, $Result, string $Scope)` | `PyOS.SandboxReport()` |
### <span id="PyOS-scaling"></span>PyOS.Scaling
- **Purpose**: OS-level adaptive scaling brain for PyOS Multi-App, Multi-Tenant, Enterprise-grade performance control Policy-driven, metric-aware, safe auto-scaling
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Scaling.py](PyOS/Py.26.00.00/Engine/PyOS.Scaling.py)
- **Class**: `PyOS_Scaling`
- **Facade Entry Points**:
  - `PyOS.Scaling()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ScalingObserve()` → `public static function Observe(array $Payload, array $Context)`
  - `PyOS.ScalingDecide()` → `public static function Decide(array $Payload, array $Context)`
  - `PyOS.ScalingApply()` → `public static function Apply(array $Payload, array $Context)`
  - `PyOS.ScalingStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.ScalingSimulate()` → `public static function Simulate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Scaling()` |
| Observe | `public static function Observe(array $Payload, array $Context)` | `PyOS.ScalingObserve()` |
| Decide | `public static function Decide(array $Payload, array $Context)` | `PyOS.ScalingDecide()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `PyOS.ScalingApply()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.ScalingStatus()` |
| Simulate | `public static function Simulate(array $Payload, array $Context)` | `PyOS.ScalingSimulate()` |
### <span id="PyOS-schema"></span>PyOS.Schema
- **Purpose**: Treat Database Schema as OS-Level Asset Schema Registry (Source of Truth) Live DB Introspection Drift Detection (Registry vs Reality) Safe Migration Planning (Zero-Downtime Ready) Multi-App + Multi-Tenant Aware
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Schema.py](PyOS/Py.26.00.00/Engine/PyOS.Schema.py)
- **Class**: `PyOS_Schema`
- **Facade Entry Points**:
  - `PyOS.Schema()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SchemaGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SchemaIntrospect()` → `public static function Introspect(array $Payload, array $Context)`
  - `PyOS.SchemaDiff()` → `public static function Diff(array $Payload, array $Context)`
  - `PyOS.SchemaValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.SchemaMigratePlan()` → `public static function MigratePlan(array $Payload, array $Context)`
  - `PyOS.SchemaMigrateRun()` → `public static function MigrateRun(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Schema()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SchemaGet()` |
| Introspect | `public static function Introspect(array $Payload, array $Context)` | `PyOS.SchemaIntrospect()` |
| Diff | `public static function Diff(array $Payload, array $Context)` | `PyOS.SchemaDiff()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.SchemaValidate()` |
| MigratePlan | `public static function MigratePlan(array $Payload, array $Context)` | `PyOS.SchemaMigratePlan()` |
| MigrateRun | `public static function MigrateRun(array $Payload, array $Context)` | `PyOS.SchemaMigrateRun()` |
### <span id="PyOS-sdk"></span>PyOS.SDK
- **Purpose**: Enterprise SDK Orchestrator for PyOS (Multi-App + Multi-Tenant) Deterministic SDK Module/Provider resolution with layered overrides Registry-driven SDK discovery (no runtime scanning in hot paths) Config layering: Tenant > App > Brand > Default Provider replaceability without code changes (future-proof contracts) ----------------------------------------------------------------------------
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SDK.py](PyOS/Py.26.00.00/Engine/PyOS.SDK.py)
- **Class**: `PyOS_SDK`
- **Facade Entry Points**:
  - `PyOS.SDK()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SDKResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.SDKUse()` → `public static function Use(array $Payload, array $Context)`
  - `PyOS.SDKList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.SDKVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.SDKHealth()` → `public static function Health(array $Payload, array $Context)`
  - `PyOS.SDKRegister()` → `public static function Register(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SDK()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.SDKResolve()` |
| Use | `public static function Use(array $Payload, array $Context)` | `PyOS.SDKUse()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.SDKList()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.SDKVerify()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.SDKHealth()` |
| Register | `public static function Register(array $Payload, array $Context)` | `PyOS.SDKRegister()` |
### <span id="PyOS-search"></span>PyOS.Search
- **Purpose**: Enterprise-grade Search Engine for Multi-App + Multi-Tenant PyOS Query, Suggest, Index (hooks-ready), Health, Explain Permission-aware + Cache-safe + High-performance patterns
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Search.py](PyOS/Py.26.00.00/Engine/PyOS.Search.py)
- **Class**: `PyOS_Search`
- **Facade Entry Points**:
  - `PyOS.Search()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.SearchQuery()` → `public static function Query($Payload = [], $Context = [])`
  - `PyOS.SearchSuggest()` → `public static function Suggest($Payload = [], $Context = [])`
  - `PyOS.SearchIndex()` → `public static function Index($Payload = [], $Context = [])`
  - `PyOS.SearchHealth()` → `public static function Health($Payload = [], $Context = [])`
  - `PyOS.SearchExplain()` → `public static function Explain($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Search()` |
| Query | `public static function Query($Payload = [], $Context = [])` | `PyOS.SearchQuery()` |
| Suggest | `public static function Suggest($Payload = [], $Context = [])` | `PyOS.SearchSuggest()` |
| Index | `public static function Index($Payload = [], $Context = [])` | `PyOS.SearchIndex()` |
| Health | `public static function Health($Payload = [], $Context = [])` | `PyOS.SearchHealth()` |
| Explain | `public static function Explain($Payload = [], $Context = [])` | `PyOS.SearchExplain()` |
### <span id="PyOS-secrets"></span>PyOS.Secrets
- **Purpose**: Centralized, secure, enterprise-grade Secrets Management Engine Multi-Tenant + Multi-App + Multi-Brand aware Supports versioning, rotation, policies, audit Zero secret leakage by design
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Secrets.py](PyOS/Py.26.00.00/Engine/PyOS.Secrets.py)
- **Class**: `PyOS_Secrets`
- **Facade Entry Points**:
  - `PyOS.Secrets()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SecretsGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SecretsSet()` → `public static function Set(array $Payload, array $Context)`
  - `PyOS.SecretsRotate()` → `public static function Rotate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Secrets()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SecretsGet()` |
| Set | `public static function Set(array $Payload, array $Context)` | `PyOS.SecretsSet()` |
| Rotate | `public static function Rotate(array $Payload, array $Context)` | `PyOS.SecretsRotate()` |
### <span id="PyOS-secruleurity"></span>PyOS.SecRuleurity
- **Purpose**: PyOS OS-Level Rule Engine (Enterprise Grade) Multi-App + Multi-Tenant Policy / Decision Layer Data-driven rules (no hard-coded if-else across apps) Fast evaluation with compile + cache Explainability (audit-grade traces) ---------------------------------------------------------------------------- Core Guarantees (AliensGrade): Loader + EngineRouter v7.0 compatible (Main() mandatory) No functions/
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SecRuleurity.py](PyOS/Py.26.00.00/Engine/PyOS.SecRuleurity.py)
- **Class**: `PyOS_Rule`
- **Facade Entry Points**:
  - `PyOS.SecRuleurity()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SecRuleurityEvaluate()` → `public static function Evaluate(array $Payload, array $Context)`
  - `PyOS.SecRuleurityMatch()` → `public static function Match(array $Payload, array $Context)`
  - `PyOS.SecRuleurityExecute()` → `public static function Execute(array $Payload, array $Context)`
  - `PyOS.SecRuleurityExplain()` → `public static function Explain(array $Payload, array $Context)`
  - `PyOS.SecRuleurityValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.SecRuleurityCompile()` → `public static function Compile(array $Payload, array $Context)`
  - `PyOS.SecRuleurityCacheWarm()` → `public static function CacheWarm(array $Payload, array $Context)`
  - `PyOS.SecRuleurityGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SecRuleurityList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.SecRuleurityNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.SecRuleurityUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.SecRuleurityStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.SecRuleurityVersion()` → `public static function Version(array $Payload, array $Context)`
  - `PyOS.SecRuleurityRollback()` → `public static function Rollback(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SecRuleurity()` |
| Evaluate | `public static function Evaluate(array $Payload, array $Context)` | `PyOS.SecRuleurityEvaluate()` |
| Match | `public static function Match(array $Payload, array $Context)` | `PyOS.SecRuleurityMatch()` |
| Execute | `public static function Execute(array $Payload, array $Context)` | `PyOS.SecRuleurityExecute()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.SecRuleurityExplain()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.SecRuleurityValidate()` |
| Compile | `public static function Compile(array $Payload, array $Context)` | `PyOS.SecRuleurityCompile()` |
| CacheWarm | `public static function CacheWarm(array $Payload, array $Context)` | `PyOS.SecRuleurityCacheWarm()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SecRuleurityGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.SecRuleurityList()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.SecRuleurityNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.SecRuleurityUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.SecRuleurityStatus()` |
| Version | `public static function Version(array $Payload, array $Context)` | `PyOS.SecRuleurityVersion()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.SecRuleurityRollback()` |
### <span id="PyOS-security"></span>PyOS.Security
- **Purpose**: Universal Category / Taxonomy Engine for PyOS (OS-Layer, not module) Multi-App + Multi-Tenant safe operations Tree-ready structure with optional graph extensions High-performance read patterns with cache + selective invalidation Enterprise governance: validation, policy checks, audit hooks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Security.py](PyOS/Py.26.00.00/Engine/PyOS.Security.py)
- **Class**: `PyOS_Category`
- **Facade Entry Points**:
  - `PyOS.Security()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SecurityGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SecurityList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.SecuritySearch()` → `public static function Search(array $Payload, array $Context)`
  - `PyOS.SecurityNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.SecurityUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.SecurityStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.SecurityDelete()` → `public static function Delete(array $Payload, array $Context)`
  - `PyOS.SecurityTree()` → `public static function Tree(array $Payload, array $Context)`
  - `PyOS.SecurityChildren()` → `public static function Children(array $Payload, array $Context)`
  - `PyOS.SecurityBreadcrumb()` → `public static function Breadcrumb(array $Payload, array $Context)`
  - `PyOS.SecurityMove()` → `public static function Move(array $Payload, array $Context)`
  - `PyOS.SecurityRebuild()` → `public static function Rebuild(array $Payload, array $Context)`
  - `PyOS.SecurityMapAdd()` → `public static function MapAdd(array $Payload, array $Context)`
  - `PyOS.SecurityMapRemove()` → `public static function MapRemove(array $Payload, array $Context)`
  - `PyOS.SecurityMapList()` → `public static function MapList(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Security()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SecurityGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.SecurityList()` |
| Search | `public static function Search(array $Payload, array $Context)` | `PyOS.SecuritySearch()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.SecurityNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.SecurityUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.SecurityStatus()` |
| Delete | `public static function Delete(array $Payload, array $Context)` | `PyOS.SecurityDelete()` |
| Tree | `public static function Tree(array $Payload, array $Context)` | `PyOS.SecurityTree()` |
| Children | `public static function Children(array $Payload, array $Context)` | `PyOS.SecurityChildren()` |
| Breadcrumb | `public static function Breadcrumb(array $Payload, array $Context)` | `PyOS.SecurityBreadcrumb()` |
| Move | `public static function Move(array $Payload, array $Context)` | `PyOS.SecurityMove()` |
| Rebuild | `public static function Rebuild(array $Payload, array $Context)` | `PyOS.SecurityRebuild()` |
| MapAdd | `public static function MapAdd(array $Payload, array $Context)` | `PyOS.SecurityMapAdd()` |
| MapRemove | `public static function MapRemove(array $Payload, array $Context)` | `PyOS.SecurityMapRemove()` |
| MapList | `public static function MapList(array $Payload, array $Context)` | `PyOS.SecurityMapList()` |
### <span id="PyOS-securityscan"></span>PyOS.SecurityScan
- **Purpose**: Enterprise-grade static & policy security scanner for PyOS Multi-App + Multi-Tenant aware Release gate + audit-ready security reports PyOS-specific rule enforcement (Boot, Engine, Unit, Cache safety) ----------------------------------------------------------------------------
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SecurityScan.py](PyOS/Py.26.00.00/Engine/PyOS.SecurityScan.py)
- **Class**: `PyOS_SecurityScan`
- **Facade Entry Points**:
  - `PyOS.SecurityScan()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SecurityScanRun()` → `public static function Run(array $Payload, array $Context)`
  - `PyOS.SecurityScanGate()` → `public static function Gate(array $Payload, array $Context)`
  - `PyOS.SecurityScanSummary()` → `public static function Summary(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SecurityScan()` |
| Run | `public static function Run(array $Payload, array $Context)` | `PyOS.SecurityScanRun()` |
| Gate | `public static function Gate(array $Payload, array $Context)` | `PyOS.SecurityScanGate()` |
| Summary | `public static function Summary(array $Payload, array $Context)` | `PyOS.SecurityScanSummary()` |
### <span id="PyOS-selftest"></span>PyOS.SelfTest
- **Purpose**: Self-healing engine for PyOS Detect, plan, apply and rollback system repairs Multi-App + Multi-Tenant safe Policy driven, audit ready, zero-downtime friendly
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SelfTest.py](PyOS/Py.26.00.00/Engine/PyOS.SelfTest.py)
- **Class**: `PyOS_SelfRepair`
- **Facade Entry Points**:
  - `PyOS.SelfTest()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SelfTestStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.SelfTestScan()` → `public static function Scan(array $Payload, array $Context)`
  - `PyOS.SelfTestPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.SelfTestApply()` → `public static function Apply(array $Payload, array $Context)`
  - `PyOS.SelfTestRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `PyOS.SelfTestHistory()` → `public static function History(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SelfTest()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.SelfTestStatus()` |
| Scan | `public static function Scan(array $Payload, array $Context)` | `PyOS.SelfTestScan()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.SelfTestPlan()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `PyOS.SelfTestApply()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.SelfTestRollback()` |
| History | `public static function History(array $Payload, array $Context)` | `PyOS.SelfTestHistory()` |
### <span id="PyOS-semantic"></span>PyOS.Semantic
- **Purpose**: Provide semantic intelligence layer for PyOS Meaning-based search, indexing, embeddings & ranking Multi-App, Multi-Tenant, Enterprise-grade isolation
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Semantic.py](PyOS/Py.26.00.00/Engine/PyOS.Semantic.py)
- **Class**: `PyOS_Semantic`
- **Facade Entry Points**:
  - `PyOS.Semantic()` → `public static function Main(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Semantic()` |
### <span id="PyOS-server"></span>PyOS.Server
- **Purpose**: Server-level Operating System engine for PyOS Provides node identity, health, capacity, modes & cluster signals Works across Single Server → Multi-Server → Cluster Multi-App + Multi-Tenant aware
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Server.py](PyOS/Py.26.00.00/Engine/PyOS.Server.py)
- **Class**: `PyOS_Server`
- **Facade Entry Points**:
  - `PyOS.Server()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ServerInfo()` → `public static function Info(array $Payload, array $Context)`
  - `PyOS.ServerServerId()` → `public static function ServerId(array $Payload, array $Context)`
  - `PyOS.ServerHealth()` → `public static function Health(array $Payload, array $Context)`
  - `PyOS.ServerHealthDeep()` → `public static function HealthDeep(array $Payload, array $Context)`
  - `PyOS.ServerCapacity()` → `public static function Capacity(array $Payload, array $Context)`
  - `PyOS.ServerMaintenanceOn()` → `public static function MaintenanceOn(array $Payload, array $Context)`
  - `PyOS.ServerMaintenanceOff()` → `public static function MaintenanceOff(array $Payload, array $Context)`
  - `PyOS.ServerRegister()` → `public static function Register(array $Payload, array $Context)`
  - `PyOS.ServerHeartbeat()` → `public static function Heartbeat(array $Payload, array $Context)`
  - `PyOS.ServerSnapshot()` → `public static function Snapshot(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Server()` |
| Info | `public static function Info(array $Payload, array $Context)` | `PyOS.ServerInfo()` |
| ServerId | `public static function ServerId(array $Payload, array $Context)` | `PyOS.ServerServerId()` |
| Health | `public static function Health(array $Payload, array $Context)` | `PyOS.ServerHealth()` |
| HealthDeep | `public static function HealthDeep(array $Payload, array $Context)` | `PyOS.ServerHealthDeep()` |
| Capacity | `public static function Capacity(array $Payload, array $Context)` | `PyOS.ServerCapacity()` |
| MaintenanceOn | `public static function MaintenanceOn(array $Payload, array $Context)` | `PyOS.ServerMaintenanceOn()` |
| MaintenanceOff | `public static function MaintenanceOff(array $Payload, array $Context)` | `PyOS.ServerMaintenanceOff()` |
| Register | `public static function Register(array $Payload, array $Context)` | `PyOS.ServerRegister()` |
| Heartbeat | `public static function Heartbeat(array $Payload, array $Context)` | `PyOS.ServerHeartbeat()` |
| Snapshot | `public static function Snapshot(array $Payload, array $Context)` | `PyOS.ServerSnapshot()` |
### <span id="PyOS-session"></span>PyOS.Session
- **Purpose**: Enterprise-grade Session Operating Layer for PyOS (Multi-App, Multi-Tenant) Supports Browser Cookie Sessions + API Bearer Sessions + Refresh Tokens High-performance Validate() hot-path with Cache-first strategy Force revoke, revoke-all, rotation, touch throttling, device binding hooks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Session.py](PyOS/Py.26.00.00/Engine/PyOS.Session.py)
- **Class**: `PyOS_Session`
- **Facade Entry Points**:
  - `PyOS.Session()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SessionStart()` → `public static function Start(array $Payload, array $Context)`
  - `PyOS.SessionValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.SessionGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SessionTouch()` → `public static function Touch(array $Payload, array $Context)`
  - `PyOS.SessionRefresh()` → `public static function Refresh(array $Payload, array $Context)`
  - `PyOS.SessionRotate()` → `public static function Rotate(array $Payload, array $Context)`
  - `PyOS.SessionRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `PyOS.SessionRevokeAll()` → `public static function RevokeAll(array $Payload, array $Context)`
  - `PyOS.SessionList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.SessionDestroy()` → `public static function Destroy(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Session()` |
| Start | `public static function Start(array $Payload, array $Context)` | `PyOS.SessionStart()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.SessionValidate()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SessionGet()` |
| Touch | `public static function Touch(array $Payload, array $Context)` | `PyOS.SessionTouch()` |
| Refresh | `public static function Refresh(array $Payload, array $Context)` | `PyOS.SessionRefresh()` |
| Rotate | `public static function Rotate(array $Payload, array $Context)` | `PyOS.SessionRotate()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `PyOS.SessionRevoke()` |
| RevokeAll | `public static function RevokeAll(array $Payload, array $Context)` | `PyOS.SessionRevokeAll()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.SessionList()` |
| Destroy | `public static function Destroy(array $Payload, array $Context)` | `PyOS.SessionDestroy()` |
### <span id="PyOS-shard"></span>PyOS.Shard
- **Purpose**: Enterprise-grade Sharding Engine for PyOS (Multi-App + Multi-Tenant) Deterministic shard routing (tenant/user/table/module) Directory-based shard map with cache-first hot-path Read/Write split awareness + policy hooks Admin controls: MapSet, NodeRegister, NodeStatus, KeyMove (RBAC-ready) Observability: Explain() + structured debug meta
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Shard.py](PyOS/Py.26.00.00/Engine/PyOS.Shard.py)
- **Class**: `PyOS_Shard`
- **Facade Entry Points**:
  - `PyOS.Shard()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ShardResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.ShardResolveBatch()` → `public static function ResolveBatch(array $Payload, array $Context)`
  - `PyOS.ShardMapGet()` → `public static function MapGet(array $Payload, array $Context)`
  - `PyOS.ShardMapSet()` → `public static function MapSet(array $Payload, array $Context)`
  - `PyOS.ShardNodeRegister()` → `public static function NodeRegister(array $Payload, array $Context)`
  - `PyOS.ShardNodeStatus()` → `public static function NodeStatus(array $Payload, array $Context)`
  - `PyOS.ShardKeyMove()` → `public static function KeyMove(array $Payload, array $Context)`
  - `PyOS.ShardPlanRebalance()` → `public static function PlanRebalance(array $Payload, array $Context)`
  - `PyOS.ShardExecuteRebalance()` → `public static function ExecuteRebalance(array $Payload, array $Context)`
  - `PyOS.ShardExplain()` → `public static function Explain(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Shard()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.ShardResolve()` |
| ResolveBatch | `public static function ResolveBatch(array $Payload, array $Context)` | `PyOS.ShardResolveBatch()` |
| MapGet | `public static function MapGet(array $Payload, array $Context)` | `PyOS.ShardMapGet()` |
| MapSet | `public static function MapSet(array $Payload, array $Context)` | `PyOS.ShardMapSet()` |
| NodeRegister | `public static function NodeRegister(array $Payload, array $Context)` | `PyOS.ShardNodeRegister()` |
| NodeStatus | `public static function NodeStatus(array $Payload, array $Context)` | `PyOS.ShardNodeStatus()` |
| KeyMove | `public static function KeyMove(array $Payload, array $Context)` | `PyOS.ShardKeyMove()` |
| PlanRebalance | `public static function PlanRebalance(array $Payload, array $Context)` | `PyOS.ShardPlanRebalance()` |
| ExecuteRebalance | `public static function ExecuteRebalance(array $Payload, array $Context)` | `PyOS.ShardExecuteRebalance()` |
| Explain | `public static function Explain(array $Payload, array $Context)` | `PyOS.ShardExplain()` |
### <span id="PyOS-share"></span>PyOS.Share
- **Purpose**: Enterprise Grade Share System (Multi-App + Multi-Tenant) Create secure share links (tokenized, scoped, revoke-able) Validate/Open share (auth/role/invite/expiry/max_uses) High-performance: cache-friendly, audit-ready, abuse-ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Share.py](PyOS/Py.26.00.00/Engine/PyOS.Share.py)
- **Class**: `PyOS_Share`
- **Facade Entry Points**:
  - `PyOS.Share()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ShareNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.ShareGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ShareOpen()` → `public static function Open(array $Payload, array $Context)`
  - `PyOS.ShareRevoke()` → `public static function Revoke(array $Payload, array $Context)`
  - `PyOS.ShareValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.ShareResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.ShareList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.ShareStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Share()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.ShareNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ShareGet()` |
| Open | `public static function Open(array $Payload, array $Context)` | `PyOS.ShareOpen()` |
| Revoke | `public static function Revoke(array $Payload, array $Context)` | `PyOS.ShareRevoke()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.ShareValidate()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.ShareResolve()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.ShareList()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `PyOS.ShareStats()` |
### <span id="PyOS-shipping"></span>PyOS.Shipping
- **Purpose**: Enterprise-grade Shipping & Logistics Engine Multi-App + Multi-Tenant + OS-Level abstraction Carrier-agnostic, Rule-driven, Auditable shipping system
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Shipping.py](PyOS/Py.26.00.00/Engine/PyOS.Shipping.py)
- **Class**: `PyOS_Shipping`
- **Facade Entry Points**:
  - `PyOS.Shipping()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ShippingQuote()` → `public static function Quote(array $Payload, array $Context)`
  - `PyOS.ShippingCreate()` → `public static function Create(array $Payload, array $Context)`
  - `PyOS.ShippingLabelGenerate()` → `public static function LabelGenerate(array $Payload, array $Context)`
  - `PyOS.ShippingTrack()` → `public static function Track(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Shipping()` |
| Quote | `public static function Quote(array $Payload, array $Context)` | `PyOS.ShippingQuote()` |
| Create | `public static function Create(array $Payload, array $Context)` | `PyOS.ShippingCreate()` |
| LabelGenerate | `public static function LabelGenerate(array $Payload, array $Context)` | `PyOS.ShippingLabelGenerate()` |
| Track | `public static function Track(array $Payload, array $Context)` | `PyOS.ShippingTrack()` |
### <span id="PyOS-signal"></span>PyOS.Signal
- **Purpose**: PyOS Control-Plane Signaling Engine (OS-Level Signals) Multi-App + Multi-Tenant safe signaling (Scope enforced) High-performance signals using adapters (Cache default; DB/Redis ready) Supports Broadcast/Unicast/Multicast, TTL, Priority, Ack, Retry metadata Boot files call only: PyOS::SignalSend(), PyOS::SignalListen(), etc.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Signal.py](PyOS/Py.26.00.00/Engine/PyOS.Signal.py)
- **Class**: `PyOS_Signal`
- **Facade Entry Points**:
  - `PyOS.Signal()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.SignalSend()` → `public static function Send($Payload = [], $Context = [])`
  - `PyOS.SignalEmit()` → `public static function Emit($Payload = [], $Context = [])`
  - `PyOS.SignalBroadcast()` → `public static function Broadcast($Payload = [], $Context = [])`
  - `PyOS.SignalSendMany()` → `public static function SendMany($Payload = [], $Context = [])`
  - `PyOS.SignalListen()` → `public static function Listen($Payload = [], $Context = [])`
  - `PyOS.SignalAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `PyOS.SignalAckMany()` → `public static function AckMany($Payload = [], $Context = [])`
  - `PyOS.SignalGet()` → `public static function Get($Payload = [], $Context = [])`
  - `PyOS.SignalPeek()` → `public static function Peek($Payload = [], $Context = [])`
  - `PyOS.SignalPurge()` → `public static function Purge($Payload = [], $Context = [])`
  - `PyOS.SignalStats()` → `public static function Stats($Payload = [], $Context = [])`
  - `PyOS.SignalConfig()` → `public static function Config($Payload = [], $Context = [])`
  - `PyOS.SignalLock()` → `public static function Lock($Payload = [], $Context = [])`
  - `PyOS.SignalUnlock()` → `public static function Unlock($Payload = [], $Context = [])`
  - `PyOS.SignalDeadLetter()` → `public static function DeadLetter($Payload = [], $Context = [])`
  - `PyOS.SignalReplay()` → `public static function Replay($Payload = [], $Context = [])`
  - `PyOS.SignalTrace()` → `public static function Trace($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Signal()` |
| Send | `public static function Send($Payload = [], $Context = [])` | `PyOS.SignalSend()` |
| Emit | `public static function Emit($Payload = [], $Context = [])` | `PyOS.SignalEmit()` |
| Broadcast | `public static function Broadcast($Payload = [], $Context = [])` | `PyOS.SignalBroadcast()` |
| SendMany | `public static function SendMany($Payload = [], $Context = [])` | `PyOS.SignalSendMany()` |
| Listen | `public static function Listen($Payload = [], $Context = [])` | `PyOS.SignalListen()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `PyOS.SignalAck()` |
| AckMany | `public static function AckMany($Payload = [], $Context = [])` | `PyOS.SignalAckMany()` |
| Get | `public static function Get($Payload = [], $Context = [])` | `PyOS.SignalGet()` |
| Peek | `public static function Peek($Payload = [], $Context = [])` | `PyOS.SignalPeek()` |
| Purge | `public static function Purge($Payload = [], $Context = [])` | `PyOS.SignalPurge()` |
| Stats | `public static function Stats($Payload = [], $Context = [])` | `PyOS.SignalStats()` |
| Config | `public static function Config($Payload = [], $Context = [])` | `PyOS.SignalConfig()` |
| Lock | `public static function Lock($Payload = [], $Context = [])` | `PyOS.SignalLock()` |
| Unlock | `public static function Unlock($Payload = [], $Context = [])` | `PyOS.SignalUnlock()` |
| DeadLetter | `public static function DeadLetter($Payload = [], $Context = [])` | `PyOS.SignalDeadLetter()` |
| Replay | `public static function Replay($Payload = [], $Context = [])` | `PyOS.SignalReplay()` |
| Trace | `public static function Trace($Payload = [], $Context = [])` | `PyOS.SignalTrace()` |
### <span id="PyOS-signature"></span>PyOS.Signature
- **Purpose**: Enterprise-grade request signing & verification engine Protect APIs, Webhooks, internal service calls Prevent tampering, replay attacks, cross-tenant abuse
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Signature.py](PyOS/Py.26.00.00/Engine/PyOS.Signature.py)
- **Class**: `PyOS_Signature`
- **Facade Entry Points**:
  - `PyOS.Signature()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SignatureSign()` → `public static function Sign(array $Payload, array $Context)`
  - `PyOS.SignatureVerify()` → `public static function Verify(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Signature()` |
| Sign | `public static function Sign(array $Payload, array $Context)` | `PyOS.SignatureSign()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.SignatureVerify()` |
### <span id="PyOS-simulation"></span>PyOS.Simulation
- **Purpose**: Enterprise-grade Simulation Engine for PyOS (Multi-App + Multi-Tenant) Run realistic sandbox simulations of API/Cron/Webhook/Engine scenarios Deterministic runs (seed-based), policy-driven safety, audit-ready reports
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Simulation.py](PyOS/Py.26.00.00/Engine/PyOS.Simulation.py)
- **Class**: `as`
- **Facade Entry Points**:
  - `PyOS.Simulation()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationScenarioNew()` → `public static function ScenarioNew(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationScenarioGet()` → `public static function ScenarioGet(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationCompile()` → `public static function Compile(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationScenarioRun()` → `public static function ScenarioRun(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationRunGet()` → `public static function RunGet(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationRunList()` → `public static function RunList(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationAssert()` → `public static function Assert(array $Payload = [], array $Context = [])`
  - `PyOS.SimulationReport()` → `public static function Report(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Simulation()` |
| ScenarioNew | `public static function ScenarioNew(array $Payload = [], array $Context = [])` | `PyOS.SimulationScenarioNew()` |
| ScenarioGet | `public static function ScenarioGet(array $Payload = [], array $Context = [])` | `PyOS.SimulationScenarioGet()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `PyOS.SimulationValidate()` |
| Compile | `public static function Compile(array $Payload = [], array $Context = [])` | `PyOS.SimulationCompile()` |
| ScenarioRun | `public static function ScenarioRun(array $Payload = [], array $Context = [])` | `PyOS.SimulationScenarioRun()` |
| RunGet | `public static function RunGet(array $Payload = [], array $Context = [])` | `PyOS.SimulationRunGet()` |
| RunList | `public static function RunList(array $Payload = [], array $Context = [])` | `PyOS.SimulationRunList()` |
| Assert | `public static function Assert(array $Payload = [], array $Context = [])` | `PyOS.SimulationAssert()` |
| Report | `public static function Report(array $Payload = [], array $Context = [])` | `PyOS.SimulationReport()` |
### <span id="PyOS-smartcontract"></span>PyOS.SmartContract
- **Purpose**: Smart Contract Operating System Layer (Registry + Deploy + Call + Send) Multi-App + Multi-Tenant compatible (Context enforced) High-Performance, Cache-first, Queue-first (write paths async) Policy + RBAC gates (via PyOS engines) ----------------------------------------------------------------------------
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SmartContract.py](PyOS/Py.26.00.00/Engine/PyOS.SmartContract.py)
- **Class**: `PyOS_SmartContract`
- **Facade Entry Points**:
  - `PyOS.SmartContract()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SmartContractNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.SmartContractGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SmartContractList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.SmartContractDeploy()` → `public static function Deploy(array $Payload, array $Context)`
  - `PyOS.SmartContractSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.SmartContractCall()` → `public static function Call(array $Payload, array $Context)`
  - `PyOS.SmartContractVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.SmartContractAudit()` → `public static function Audit(array $Payload, array $Context)`
  - `PyOS.SmartContractEvents()` → `public static function Events(array $Payload, array $Context)`
  - `PyOS.SmartContractGetFresh()` → `public static function GetFresh($TenantId, $AppCode, $Name, $ChainId)`
  - `PyOS.SmartContractCallFresh()` → `public static function CallFresh(array $Scope, array $Context, array $Contract, string $Method, $Args)`
  - `PyOS.SmartContractResolveActiveAddressFresh()` → `public static function ResolveActiveAddressFresh($TenantId, $AppCode, $Name, $ChainId)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SmartContract()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.SmartContractNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SmartContractGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.SmartContractList()` |
| Deploy | `public static function Deploy(array $Payload, array $Context)` | `PyOS.SmartContractDeploy()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.SmartContractSend()` |
| Call | `public static function Call(array $Payload, array $Context)` | `PyOS.SmartContractCall()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.SmartContractVerify()` |
| Audit | `public static function Audit(array $Payload, array $Context)` | `PyOS.SmartContractAudit()` |
| Events | `public static function Events(array $Payload, array $Context)` | `PyOS.SmartContractEvents()` |
| GetFresh | `public static function GetFresh($TenantId, $AppCode, $Name, $ChainId)` | `PyOS.SmartContractGetFresh()` |
| CallFresh | `public static function CallFresh(array $Scope, array $Context, array $Contract, string $Method, $Args)` | `PyOS.SmartContractCallFresh()` |
| ResolveActiveAddressFresh | `public static function ResolveActiveAddressFresh($TenantId, $AppCode, $Name, $ChainId)` | `PyOS.SmartContractResolveActiveAddressFresh()` |
### <span id="PyOS-sms"></span>PyOS.SMS
- **Purpose**: Enterprise-grade SMS orchestration layer for PyOS Backend OS Multi-App + Multi-Tenant provider-agnostic SMS sending OTP suite (Send OTP + Verify) with anti-abuse + idempotency hooks Delivery tracking hooks + callback processing hooks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SMS.py](PyOS/Py.26.00.00/Engine/PyOS.SMS.py)
- **Class**: `PyOS_SMS`
- **Facade Entry Points**:
  - `PyOS.SMS()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SMSSend()` → `public static function Send(array $Payload, array $Context)`
  - `PyOS.SMSOTP()` → `public static function OTP(array $Payload, array $Context)`
  - `PyOS.SMSVerify()` → `public static function Verify(array $Payload, array $Context)`
  - `PyOS.SMSStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.SMSCallback()` → `public static function Callback(array $Payload, array $Context)`
  - `PyOS.SMSProviderSet()` → `public static function ProviderSet(array $Payload, array $Context)`
  - `PyOS.SMSProviderTest()` → `public static function ProviderTest(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SMS()` |
| Send | `public static function Send(array $Payload, array $Context)` | `PyOS.SMSSend()` |
| OTP | `public static function OTP(array $Payload, array $Context)` | `PyOS.SMSOTP()` |
| Verify | `public static function Verify(array $Payload, array $Context)` | `PyOS.SMSVerify()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.SMSStatus()` |
| Callback | `public static function Callback(array $Payload, array $Context)` | `PyOS.SMSCallback()` |
| ProviderSet | `public static function ProviderSet(array $Payload, array $Context)` | `PyOS.SMSProviderSet()` |
| ProviderTest | `public static function ProviderTest(array $Payload, array $Context)` | `PyOS.SMSProviderTest()` |
### <span id="PyOS-socialgraph"></span>PyOS.SocialGraph
- **Purpose**: Enterprise-grade Social Graph kernel for Multi-App + Multi-Tenant PyOS Generic Node↔Node relationships (FOLLOW, FRIEND, BLOCK, MUTE, MEMBER...) High-performance reads via counters + cache + pagination Deterministic scoping via Context (tenant/app/brand/ecosystem) Idempotent writes + audit events + cache invalidation hooks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.SocialGraph.py](PyOS/Py.26.00.00/Engine/PyOS.SocialGraph.py)
- **Class**: `PyOS_SocialGraph`
- **Facade Entry Points**:
  - `PyOS.SocialGraph()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SocialGraphLink()` → `public static function Link(array $Payload, array $Context)`
  - `PyOS.SocialGraphUnlink()` → `public static function Unlink(array $Payload, array $Context)`
  - `PyOS.SocialGraphFollow()` → `public static function Follow(array $Payload, array $Context)`
  - `PyOS.SocialGraphUnfollow()` → `public static function Unfollow(array $Payload, array $Context)`
  - `PyOS.SocialGraphBlock()` → `public static function Block(array $Payload, array $Context)`
  - `PyOS.SocialGraphUnblock()` → `public static function Unblock(array $Payload, array $Context)`
  - `PyOS.SocialGraphIsLinked()` → `public static function IsLinked(array $Payload, array $Context)`
  - `PyOS.SocialGraphGetFollowers()` → `public static function GetFollowers(array $Payload, array $Context)`
  - `PyOS.SocialGraphGetFollowing()` → `public static function GetFollowing(array $Payload, array $Context)`
  - `PyOS.SocialGraphCount()` → `public static function Count(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.SocialGraph()` |
| Link | `public static function Link(array $Payload, array $Context)` | `PyOS.SocialGraphLink()` |
| Unlink | `public static function Unlink(array $Payload, array $Context)` | `PyOS.SocialGraphUnlink()` |
| Follow | `public static function Follow(array $Payload, array $Context)` | `PyOS.SocialGraphFollow()` |
| Unfollow | `public static function Unfollow(array $Payload, array $Context)` | `PyOS.SocialGraphUnfollow()` |
| Block | `public static function Block(array $Payload, array $Context)` | `PyOS.SocialGraphBlock()` |
| Unblock | `public static function Unblock(array $Payload, array $Context)` | `PyOS.SocialGraphUnblock()` |
| IsLinked | `public static function IsLinked(array $Payload, array $Context)` | `PyOS.SocialGraphIsLinked()` |
| GetFollowers | `public static function GetFollowers(array $Payload, array $Context)` | `PyOS.SocialGraphGetFollowers()` |
| GetFollowing | `public static function GetFollowing(array $Payload, array $Context)` | `PyOS.SocialGraphGetFollowing()` |
| Count | `public static function Count(array $Payload, array $Context)` | `PyOS.SocialGraphCount()` |
### <span id="PyOS-socket"></span>PyOS.Socket
- **Purpose**: PyOS real-time backend backbone (WS/SSE/Long-Poll orchestration) Gateway + Backplane driven architecture (Python = policy + routing + publish) Multi-App + Multi-Tenant isolation (Topic scope) Auth + Role/Permission enforcement (deny-by-default) Presence management (Redis/Cache-first), with audit-friendly payloads
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Socket.py](PyOS/Py.26.00.00/Engine/PyOS.Socket.py)
- **Class**: `PyOS_Socket`
- **Facade Entry Points**:
  - `PyOS.Socket()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.SocketConfigGet()` → `public static function ConfigGet($Payload = [], $Context = [])`
  - `PyOS.SocketTopicBuild()` → `public static function TopicBuild($Payload = [], $Context = [])`
  - `PyOS.SocketTopicParse()` → `public static function TopicParse($Payload = [], $Context = [])`
  - `PyOS.SocketHandshake()` → `public static function Handshake($Payload = [], $Context = [])`
  - `PyOS.SocketAllow()` → `public static function Allow($Payload = [], $Context = [])`
  - `PyOS.SocketPublish()` → `public static function Publish($Payload = [], $Context = [])`
  - `PyOS.SocketBroadcast()` → `public static function Broadcast($Payload = [], $Context = [])`
  - `PyOS.SocketEmit()` → `public static function Emit($Payload = [], $Context = [])`
  - `PyOS.SocketSubscribeToken()` → `public static function SubscribeToken($Payload = [], $Context = [])`
  - `PyOS.SocketUnsubscribeToken()` → `public static function UnsubscribeToken($Payload = [], $Context = [])`
  - `PyOS.SocketPresenceSet()` → `public static function PresenceSet($Payload = [], $Context = [])`
  - `PyOS.SocketPresenceGet()` → `public static function PresenceGet($Payload = [], $Context = [])`
  - `PyOS.SocketPresenceList()` → `public static function PresenceList($Payload = [], $Context = [])`
  - `PyOS.SocketAck()` → `public static function Ack($Payload = [], $Context = [])`
  - `PyOS.SocketRetry()` → `public static function Retry($Payload = [], $Context = [])`
  - `PyOS.SocketDeadLetter()` → `public static function DeadLetter($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Socket()` |
| ConfigGet | `public static function ConfigGet($Payload = [], $Context = [])` | `PyOS.SocketConfigGet()` |
| TopicBuild | `public static function TopicBuild($Payload = [], $Context = [])` | `PyOS.SocketTopicBuild()` |
| TopicParse | `public static function TopicParse($Payload = [], $Context = [])` | `PyOS.SocketTopicParse()` |
| Handshake | `public static function Handshake($Payload = [], $Context = [])` | `PyOS.SocketHandshake()` |
| Allow | `public static function Allow($Payload = [], $Context = [])` | `PyOS.SocketAllow()` |
| Publish | `public static function Publish($Payload = [], $Context = [])` | `PyOS.SocketPublish()` |
| Broadcast | `public static function Broadcast($Payload = [], $Context = [])` | `PyOS.SocketBroadcast()` |
| Emit | `public static function Emit($Payload = [], $Context = [])` | `PyOS.SocketEmit()` |
| SubscribeToken | `public static function SubscribeToken($Payload = [], $Context = [])` | `PyOS.SocketSubscribeToken()` |
| UnsubscribeToken | `public static function UnsubscribeToken($Payload = [], $Context = [])` | `PyOS.SocketUnsubscribeToken()` |
| PresenceSet | `public static function PresenceSet($Payload = [], $Context = [])` | `PyOS.SocketPresenceSet()` |
| PresenceGet | `public static function PresenceGet($Payload = [], $Context = [])` | `PyOS.SocketPresenceGet()` |
| PresenceList | `public static function PresenceList($Payload = [], $Context = [])` | `PyOS.SocketPresenceList()` |
| Ack | `public static function Ack($Payload = [], $Context = [])` | `PyOS.SocketAck()` |
| Retry | `public static function Retry($Payload = [], $Context = [])` | `PyOS.SocketRetry()` |
| DeadLetter | `public static function DeadLetter($Payload = [], $Context = [])` | `PyOS.SocketDeadLetter()` |
### <span id="PyOS-sorting"></span>PyOS.Sorting
- **Purpose**: Centralized, secure, deterministic sorting engine SQL-level + Memory-level sorting Multi-App, Multi-Tenant, Role-aware Stable sorting with forced tie-breakers Safe ORDER BY generation (no injection)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Sorting.py](PyOS/Py.26.00.00/Engine/PyOS.Sorting.py)
- **Class**: `PyOS_Sorting`
- **Facade Entry Points**:
  - `PyOS.Sorting()` → `public static function Main($Payload, $Context)`
  - `PyOS.SortingPlan()` → `public static function Plan($Payload, $Context)`
  - `PyOS.SortingSql()` → `public static function Sql($Payload, $Context)`
  - `PyOS.SortingApply()` → `public static function Apply($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.Sorting()` |
| Plan | `public static function Plan($Payload, $Context)` | `PyOS.SortingPlan()` |
| Sql | `public static function Sql($Payload, $Context)` | `PyOS.SortingSql()` |
| Apply | `public static function Apply($Payload, $Context)` | `PyOS.SortingApply()` |
### <span id="PyOS-state"></span>PyOS.State
- **Purpose**: OS-level State Management for PyOS (Multi-App + Multi-Tenant) Strong State Read/Write + Atomic Transitions + Audit/History High-performance cache-first reads with DB as source of truth Race-condition safe via Lock + ExpectedVersion (optimistic) Event-ready (optional publish) and policy-ready (optional validate)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.State.py](PyOS/Py.26.00.00/Engine/PyOS.State.py)
- **Class**: `PyOS_State`
- **Facade Entry Points**:
  - `PyOS.State()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.StateGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.StateExists()` → `public static function Exists(array $Payload = [], array $Context = [])`
  - `PyOS.StateSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `PyOS.StateNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `PyOS.StateUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `PyOS.StateDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `PyOS.StateTransition()` → `public static function Transition(array $Payload = [], array $Context = [])`
  - `PyOS.StateBatchGet()` → `public static function BatchGet(array $Payload = [], array $Context = [])`
  - `PyOS.StateBatchSet()` → `public static function BatchSet(array $Payload = [], array $Context = [])`
  - `PyOS.StateHistory()` → `public static function History(array $Payload = [], array $Context = [])`
  - `PyOS.StateExpire()` → `public static function Expire(array $Payload = [], array $Context = [])`
  - `PyOS.StateRefresh()` → `public static function Refresh(array $Payload = [], array $Context = [])`
  - `PyOS.StateCleanup()` → `public static function Cleanup(array $Payload = [], array $Context = [])`
  - `PyOS.StateExplain()` → `public static function Explain(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.State()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.StateGet()` |
| Exists | `public static function Exists(array $Payload = [], array $Context = [])` | `PyOS.StateExists()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `PyOS.StateSet()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `PyOS.StateNew()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `PyOS.StateUpdate()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `PyOS.StateDelete()` |
| Transition | `public static function Transition(array $Payload = [], array $Context = [])` | `PyOS.StateTransition()` |
| BatchGet | `public static function BatchGet(array $Payload = [], array $Context = [])` | `PyOS.StateBatchGet()` |
| BatchSet | `public static function BatchSet(array $Payload = [], array $Context = [])` | `PyOS.StateBatchSet()` |
| History | `public static function History(array $Payload = [], array $Context = [])` | `PyOS.StateHistory()` |
| Expire | `public static function Expire(array $Payload = [], array $Context = [])` | `PyOS.StateExpire()` |
| Refresh | `public static function Refresh(array $Payload = [], array $Context = [])` | `PyOS.StateRefresh()` |
| Cleanup | `public static function Cleanup(array $Payload = [], array $Context = [])` | `PyOS.StateCleanup()` |
| Explain | `public static function Explain(array $Payload = [], array $Context = [])` | `PyOS.StateExplain()` |
### <span id="PyOS-stream"></span>PyOS.Stream
- **Purpose**: High-Performance Streaming Engine for PyOS Supports SSE, chunked streaming, publish/subscribe Multi-App + Multi-Tenant safe Enterprise-grade audit, policy & observability ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Stream.py](PyOS/Py.26.00.00/Engine/PyOS.Stream.py)
- **Class**: `PyOS_Stream`
- **Facade Entry Points**:
  - `PyOS.Stream()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.StreamOpen()` → `public static function Open(array $Payload, array $Context)`
  - `PyOS.StreamPublish()` → `public static function Publish(array $Payload, array $Context)`
  - `PyOS.StreamSubscribe()` → `public static function Subscribe(array $Payload, array $Context)`
  - `PyOS.StreamSSE()` → `public static function SSE(array $Payload, array $Context)`
  - `PyOS.StreamStats()` → `public static function Stats(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Stream()` |
| Open | `public static function Open(array $Payload, array $Context)` | `PyOS.StreamOpen()` |
| Publish | `public static function Publish(array $Payload, array $Context)` | `PyOS.StreamPublish()` |
| Subscribe | `public static function Subscribe(array $Payload, array $Context)` | `PyOS.StreamSubscribe()` |
| SSE | `public static function SSE(array $Payload, array $Context)` | `PyOS.StreamSSE()` |
| Stats | `public static function Stats(array $Payload, array $Context)` | `PyOS.StreamStats()` |
### <span id="PyOS-subscription"></span>PyOS.Subscription
- **Purpose**: Enterprise Grade Subscription Engine (Multi-App + Multi-Tenant) Plan Catalog + Subscription Lifecycle + Entitlements + Usage Metering (OS Layer) Deterministic State Machine + Audit Ready + Cache/Perf Ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Subscription.py](PyOS/Py.26.00.00/Engine/PyOS.Subscription.py)
- **Class**: `PyOS_Subscription`
- **Facade Entry Points**:
  - `PyOS.Subscription()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.SubscriptionPlanList()` → `public static function PlanList(array $Payload, array $Context)`
  - `PyOS.SubscriptionPlanGet()` → `public static function PlanGet(array $Payload, array $Context)`
  - `PyOS.SubscriptionPlanNew()` → `public static function PlanNew(array $Payload, array $Context)`
  - `PyOS.SubscriptionPlanUpdate()` → `public static function PlanUpdate(array $Payload, array $Context)`
  - `PyOS.SubscriptionNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.SubscriptionGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.SubscriptionCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.SubscriptionPause()` → `public static function Pause(array $Payload, array $Context)`
  - `PyOS.SubscriptionResume()` → `public static function Resume(array $Payload, array $Context)`
  - `PyOS.SubscriptionEntitlementBuild()` → `public static function EntitlementBuild(array $Payload, array $Context)`
  - `PyOS.SubscriptionEntitlementCheck()` → `public static function EntitlementCheck(array $Payload, array $Context)`
  - `PyOS.SubscriptionLimitConsume()` → `public static function LimitConsume(array $Payload, array $Context)`
  - `PyOS.SubscriptionUsageAdd()` → `public static function UsageAdd(array $Payload, array $Context)`
  - `PyOS.SubscriptionUsageGet()` → `public static function UsageGet(array $Payload, array $Context)`
  - `PyOS.SubscriptionUpgrade()` → `public static function Upgrade(array $Payload, array $Context)`
  - `PyOS.SubscriptionDowngrade()` → `public static function Downgrade(array $Payload, array $Context)`
  - `PyOS.SubscriptionRenew()` → `public static function Renew(array $Payload, array $Context)`
  - `PyOS.SubscriptionProrationPreview()` → `public static function ProrationPreview(array $Payload, array $Context)`
  - `PyOS.SubscriptionWebhook()` → `public static function Webhook(array $Payload, array $Context)`
  - `PyOS.SubscriptionSync()` → `public static function Sync(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Subscription()` |
| PlanList | `public static function PlanList(array $Payload, array $Context)` | `PyOS.SubscriptionPlanList()` |
| PlanGet | `public static function PlanGet(array $Payload, array $Context)` | `PyOS.SubscriptionPlanGet()` |
| PlanNew | `public static function PlanNew(array $Payload, array $Context)` | `PyOS.SubscriptionPlanNew()` |
| PlanUpdate | `public static function PlanUpdate(array $Payload, array $Context)` | `PyOS.SubscriptionPlanUpdate()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.SubscriptionNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.SubscriptionGet()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.SubscriptionCancel()` |
| Pause | `public static function Pause(array $Payload, array $Context)` | `PyOS.SubscriptionPause()` |
| Resume | `public static function Resume(array $Payload, array $Context)` | `PyOS.SubscriptionResume()` |
| EntitlementBuild | `public static function EntitlementBuild(array $Payload, array $Context)` | `PyOS.SubscriptionEntitlementBuild()` |
| EntitlementCheck | `public static function EntitlementCheck(array $Payload, array $Context)` | `PyOS.SubscriptionEntitlementCheck()` |
| LimitConsume | `public static function LimitConsume(array $Payload, array $Context)` | `PyOS.SubscriptionLimitConsume()` |
| UsageAdd | `public static function UsageAdd(array $Payload, array $Context)` | `PyOS.SubscriptionUsageAdd()` |
| UsageGet | `public static function UsageGet(array $Payload, array $Context)` | `PyOS.SubscriptionUsageGet()` |
| Upgrade | `public static function Upgrade(array $Payload, array $Context)` | `PyOS.SubscriptionUpgrade()` |
| Downgrade | `public static function Downgrade(array $Payload, array $Context)` | `PyOS.SubscriptionDowngrade()` |
| Renew | `public static function Renew(array $Payload, array $Context)` | `PyOS.SubscriptionRenew()` |
| ProrationPreview | `public static function ProrationPreview(array $Payload, array $Context)` | `PyOS.SubscriptionProrationPreview()` |
| Webhook | `public static function Webhook(array $Payload, array $Context)` | `PyOS.SubscriptionWebhook()` |
| Sync | `public static function Sync(array $Payload, array $Context)` | `PyOS.SubscriptionSync()` |
### <span id="PyOS-tag"></span>PyOS.Tag
- **Purpose**: Universal Tag Engine for PyOS Multi-App + Multi-Tenant + High-Performance Entity-agnostic tagging (Post, Product, User, Order, etc.)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Tag.py](PyOS/Py.26.00.00/Engine/PyOS.Tag.py)
- **Class**: `PyOS_Tag`
- **Facade Entry Points**:
  - `PyOS.Tag()` → `public static function Main($Payload, $Context)`
  - `PyOS.TagNew()` → `public static function New($Payload, $Context)`
  - `PyOS.TagGet()` → `public static function Get($Payload, $Context)`
  - `PyOS.TagAssign()` → `public static function Assign($Payload, $Context)`
  - `PyOS.TagUnassign()` → `public static function Unassign($Payload, $Context)`
  - `PyOS.TagList()` → `public static function List($Payload, $Context)`
  - `PyOS.TagNormalize()` → `public static function Normalize($Payload, $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload, $Context)` | `PyOS.Tag()` |
| New | `public static function New($Payload, $Context)` | `PyOS.TagNew()` |
| Get | `public static function Get($Payload, $Context)` | `PyOS.TagGet()` |
| Assign | `public static function Assign($Payload, $Context)` | `PyOS.TagAssign()` |
| Unassign | `public static function Unassign($Payload, $Context)` | `PyOS.TagUnassign()` |
| List | `public static function List($Payload, $Context)` | `PyOS.TagList()` |
| Normalize | `public static function Normalize($Payload, $Context)` | `PyOS.TagNormalize()` |
### <span id="PyOS-task"></span>PyOS.Task
- **Purpose**: Universal Task Orchestration Engine for PyOS Convert any backend work into reliable Tasks Multi-App + Multi-Tenant safe execution Enterprise-grade retry, locking, auditing
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Task.py](PyOS/Py.26.00.00/Engine/PyOS.Task.py)
- **Class**: `PyOS_Task`
- **Facade Entry Points**:
  - `PyOS.Task()` → `public static function Main(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Task()` |
### <span id="PyOS-tax"></span>PyOS.Tax
- **Purpose**: Central Tax Decision & Calculation Engine for PyOS Works for any Website / PyApp / Mobile App / SaaS / Marketplace Multi-Tenant + Multi-App + Multi-Jurisdiction GST / VAT / SalesTax / Digital Goods / Reverse Charge ready
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Tax.py](PyOS/Py.26.00.00/Engine/PyOS.Tax.py)
- **Class**: `PyOS_Tax`
- **Facade Entry Points**:
  - `PyOS.Tax()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.TaxCalculate()` → `public static function Calculate(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Tax()` |
| Calculate | `public static function Calculate(array $Payload, array $Context)` | `PyOS.TaxCalculate()` |
### <span id="PyOS-template"></span>PyOS.Template
- **Purpose**: Enterprise-grade Template Rendering Engine for PyOS Multi-App + Multi-Brand + Multi-Tenant aware Secure, Cacheable, Overrideable View System
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Template.py](PyOS/Py.26.00.00/Engine/PyOS.Template.py)
- **Class**: `PyOS_Template`
- **Facade Entry Points**:
  - `PyOS.Template()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.TemplateRender()` → `public static function Render(array $Payload, array $Context)`
  - `PyOS.TemplateResolve()` → `public static function Resolve(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Template()` |
| Render | `public static function Render(array $Payload, array $Context)` | `PyOS.TemplateRender()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.TemplateResolve()` |
### <span id="PyOS-tenant"></span>PyOS.Tenant
- **Purpose**: Enterprise-grade Multi-Tenant Runtime for PyOS (Multi-App + Multi-Brand) Deterministic Tenant resolution (Domain / Header / Path / Session) Tenant isolation metadata (Shared / Schema / DB-per-tenant / Hybrid shard) Tenant limits / quotas / governor switches Tenant keys & secrets lifecycle (verify / rotate / revoke) Tenant feature flags (enable/disable/config) High-performance caching (O(1) average) + negative caching
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Tenant.py](PyOS/Py.26.00.00/Engine/PyOS.Tenant.py)
- **Class**: `PyOS_Tenant`
- **Facade Entry Points**:
  - `PyOS.Tenant()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.TenantResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.TenantCurrent()` → `public static function Current(array $Payload = [], array $Context = [])`
  - `PyOS.TenantGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.TenantNew()` → `public static function New(array $Payload = [], array $Context = [])`
  - `PyOS.TenantUpdate()` → `public static function Update(array $Payload = [], array $Context = [])`
  - `PyOS.TenantDisable()` → `public static function Disable(array $Payload = [], array $Context = [])`
  - `PyOS.TenantDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `PyOS.TenantDomainBind()` → `public static function DomainBind(array $Payload = [], array $Context = [])`
  - `PyOS.TenantDomainUnbind()` → `public static function DomainUnbind(array $Payload = [], array $Context = [])`
  - `PyOS.TenantDomainList()` → `public static function DomainList(array $Payload = [], array $Context = [])`
  - `PyOS.TenantIsolationGet()` → `public static function IsolationGet(array $Payload = [], array $Context = [])`
  - `PyOS.TenantIsolationSet()` → `public static function IsolationSet(array $Payload = [], array $Context = [])`
  - `PyOS.TenantLimitGet()` → `public static function LimitGet(array $Payload = [], array $Context = [])`
  - `PyOS.TenantLimitSet()` → `public static function LimitSet(array $Payload = [], array $Context = [])`
  - `PyOS.TenantLimitCheck()` → `public static function LimitCheck(array $Payload = [], array $Context = [])`
  - `PyOS.TenantFeatureEnable()` → `public static function FeatureEnable(array $Payload = [], array $Context = [])`
  - `PyOS.TenantFeatureDisable()` → `public static function FeatureDisable(array $Payload = [], array $Context = [])`
  - `PyOS.TenantFeatureGet()` → `public static function FeatureGet(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Tenant()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.TenantResolve()` |
| Current | `public static function Current(array $Payload = [], array $Context = [])` | `PyOS.TenantCurrent()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.TenantGet()` |
| New | `public static function New(array $Payload = [], array $Context = [])` | `PyOS.TenantNew()` |
| Update | `public static function Update(array $Payload = [], array $Context = [])` | `PyOS.TenantUpdate()` |
| Disable | `public static function Disable(array $Payload = [], array $Context = [])` | `PyOS.TenantDisable()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `PyOS.TenantDelete()` |
| DomainBind | `public static function DomainBind(array $Payload = [], array $Context = [])` | `PyOS.TenantDomainBind()` |
| DomainUnbind | `public static function DomainUnbind(array $Payload = [], array $Context = [])` | `PyOS.TenantDomainUnbind()` |
| DomainList | `public static function DomainList(array $Payload = [], array $Context = [])` | `PyOS.TenantDomainList()` |
| IsolationGet | `public static function IsolationGet(array $Payload = [], array $Context = [])` | `PyOS.TenantIsolationGet()` |
| IsolationSet | `public static function IsolationSet(array $Payload = [], array $Context = [])` | `PyOS.TenantIsolationSet()` |
| LimitGet | `public static function LimitGet(array $Payload = [], array $Context = [])` | `PyOS.TenantLimitGet()` |
| LimitSet | `public static function LimitSet(array $Payload = [], array $Context = [])` | `PyOS.TenantLimitSet()` |
| LimitCheck | `public static function LimitCheck(array $Payload = [], array $Context = [])` | `PyOS.TenantLimitCheck()` |
| FeatureEnable | `public static function FeatureEnable(array $Payload = [], array $Context = [])` | `PyOS.TenantFeatureEnable()` |
| FeatureDisable | `public static function FeatureDisable(array $Payload = [], array $Context = [])` | `PyOS.TenantFeatureDisable()` |
| FeatureGet | `public static function FeatureGet(array $Payload = [], array $Context = [])` | `PyOS.TenantFeatureGet()` |
### <span id="PyOS-test"></span>PyOS.Test
- **Purpose**: Enterprise-grade Test Operating System for PyOS Multi-App + Multi-Brand + Multi-Tenant test runner (matrix mode) Engine/Router compliance tests (Main() mandatory, naming rules, routing) Security smoke tests + performance sampling + regression placeholders Reports: JSON / MD / TEXT / JUnit (basic) Designed for CI/CD release-gate (exit_code style response)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Test.py](PyOS/Py.26.00.00/Engine/PyOS.Test.py)
- **Class**: `PyOS_Test`
- **Facade Entry Points**:
  - `PyOS.Test()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.TestSuite()` → `public static function Suite($Payload = [], $Context = [])`
  - `PyOS.TestCase()` → `public static function Case($Payload = [], $Context = [])`
  - `PyOS.TestRun()` → `public static function Run($Payload = [], $Context = [])`
  - `PyOS.TestMatrix()` → `public static function Matrix($Payload = [], $Context = [])`
  - `PyOS.TestReport()` → `public static function Report($Payload = [], $Context = [])`
  - `PyOS.TestEngineContract()` → `public static function EngineContract($Payload = [], $Context = [])`
  - `PyOS.TestRouterSmoke()` → `public static function RouterSmoke($Payload = [], $Context = [])`
  - `PyOS.TestSecurityInputSmoke()` → `public static function SecurityInputSmoke($Payload = [], $Context = [])`
  - `PyOS.TestAssertTrue()` → `public static function AssertTrue($Value, $Message = '')`
  - `PyOS.TestAssertEquals()` → `public static function AssertEquals($A, $B, $Message = '')`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Test()` |
| Suite | `public static function Suite($Payload = [], $Context = [])` | `PyOS.TestSuite()` |
| Case | `public static function Case($Payload = [], $Context = [])` | `PyOS.TestCase()` |
| Run | `public static function Run($Payload = [], $Context = [])` | `PyOS.TestRun()` |
| Matrix | `public static function Matrix($Payload = [], $Context = [])` | `PyOS.TestMatrix()` |
| Report | `public static function Report($Payload = [], $Context = [])` | `PyOS.TestReport()` |
| EngineContract | `public static function EngineContract($Payload = [], $Context = [])` | `PyOS.TestEngineContract()` |
| RouterSmoke | `public static function RouterSmoke($Payload = [], $Context = [])` | `PyOS.TestRouterSmoke()` |
| SecurityInputSmoke | `public static function SecurityInputSmoke($Payload = [], $Context = [])` | `PyOS.TestSecurityInputSmoke()` |
| AssertTrue | `public static function AssertTrue($Value, $Message = '')` | `PyOS.TestAssertTrue()` |
| AssertEquals | `public static function AssertEquals($A, $B, $Message = '')` | `PyOS.TestAssertEquals()` |
### <span id="PyOS-theme"></span>PyOS.Theme
- **Purpose**: OS-level Theme Engine for PyOS Multi-App, Multi-Brand, Multi-Tenant theme resolution Design Tokens, Assets, Templates, Variants Ultra-fast cached resolution with deterministic UID
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Theme.py](PyOS/Py.26.00.00/Engine/PyOS.Theme.py)
- **Class**: `PyOS_Theme`
- **Facade Entry Points**:
  - `PyOS.Theme()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ThemeResolve()` → `public static function Resolve(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Theme()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.ThemeResolve()` |
### <span id="PyOS-timeout"></span>PyOS.Timeout
- **Purpose**: OS-grade Timeout / Deadline enforcement for: API, Cron, Webhook, Worker, Queue, Task, Pipeline (Unit + Step) Multi-App + Multi-Tenant safe budgets Nested scopes: Request → Unit → Step → SubStep Soft timeout (graceful) + Hard timeout (abort signal / optional throw)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Timeout.py](PyOS/Py.26.00.00/Engine/PyOS.Timeout.py)
- **Class**: `PyOS_Timeout`
- **Facade Entry Points**:
  - `PyOS.Timeout()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.TimeoutStart()` → `public static function Start($Payload = [], $Context = [])`
  - `PyOS.TimeoutCheck()` → `public static function Check($Payload = [], $Context = [])`
  - `PyOS.TimeoutEnd()` → `public static function End($Payload = [], $Context = [])`
  - `PyOS.TimeoutBudget()` → `public static function Budget($Payload = [], $Context = [])`
  - `PyOS.TimeoutWrap()` → `public static function Wrap($Payload = [], $Context = [])`
  - `PyOS.TimeoutNow()` → `public static function Now($Payload = [], $Context = [])`
  - `PyOS.TimeoutDeadline()` → `public static function Deadline($Payload = [], $Context = [])`
  - `PyOS.TimeoutRemaining()` → `public static function Remaining($Payload = [], $Context = [])`
  - `PyOS.TimeoutKey()` → `public static function Key($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Timeout()` |
| Start | `public static function Start($Payload = [], $Context = [])` | `PyOS.TimeoutStart()` |
| Check | `public static function Check($Payload = [], $Context = [])` | `PyOS.TimeoutCheck()` |
| End | `public static function End($Payload = [], $Context = [])` | `PyOS.TimeoutEnd()` |
| Budget | `public static function Budget($Payload = [], $Context = [])` | `PyOS.TimeoutBudget()` |
| Wrap | `public static function Wrap($Payload = [], $Context = [])` | `PyOS.TimeoutWrap()` |
| Now | `public static function Now($Payload = [], $Context = [])` | `PyOS.TimeoutNow()` |
| Deadline | `public static function Deadline($Payload = [], $Context = [])` | `PyOS.TimeoutDeadline()` |
| Remaining | `public static function Remaining($Payload = [], $Context = [])` | `PyOS.TimeoutRemaining()` |
| Key | `public static function Key($Payload = [], $Context = [])` | `PyOS.TimeoutKey()` |
### <span id="PyOS-timezone"></span>PyOS.Timezone
- **Purpose**: Enterprise Grade Timezone Engine for Multi-App + Multi-Tenant PyOS Deterministic timezone resolution (Payload → User → Tenant → Brand → App → Ecosystem → UTC) High-performance (request-cache + optional cross-request cache) Safe parsing + UTC conversions + reporting boundaries (day/week/month) ----------------------------------------------------------------------------
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Timezone.py](PyOS/Py.26.00.00/Engine/PyOS.Timezone.py)
- **Class**: `PyOS_Timezone`
- **Facade Entry Points**:
  - `PyOS.Timezone()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneResolve()` → `public static function Resolve(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneValidate()` → `public static function Validate(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneSet()` → `public static function Set(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneNow()` → `public static function Now(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneToUTC()` → `public static function ToUTC(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneFromUTC()` → `public static function FromUTC(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneParse()` → `public static function Parse(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneFormat()` → `public static function Format(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneDayStartUTC()` → `public static function DayStartUTC(array $Payload = [], array $Context = [])`
  - `PyOS.TimezoneDayEndUTC()` → `public static function DayEndUTC(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Timezone()` |
| Resolve | `public static function Resolve(array $Payload = [], array $Context = [])` | `PyOS.TimezoneResolve()` |
| Validate | `public static function Validate(array $Payload = [], array $Context = [])` | `PyOS.TimezoneValidate()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.TimezoneGet()` |
| Set | `public static function Set(array $Payload = [], array $Context = [])` | `PyOS.TimezoneSet()` |
| Now | `public static function Now(array $Payload = [], array $Context = [])` | `PyOS.TimezoneNow()` |
| ToUTC | `public static function ToUTC(array $Payload = [], array $Context = [])` | `PyOS.TimezoneToUTC()` |
| FromUTC | `public static function FromUTC(array $Payload = [], array $Context = [])` | `PyOS.TimezoneFromUTC()` |
| Parse | `public static function Parse(array $Payload = [], array $Context = [])` | `PyOS.TimezoneParse()` |
| Format | `public static function Format(array $Payload = [], array $Context = [])` | `PyOS.TimezoneFormat()` |
| DayStartUTC | `public static function DayStartUTC(array $Payload = [], array $Context = [])` | `PyOS.TimezoneDayStartUTC()` |
| DayEndUTC | `public static function DayEndUTC(array $Payload = [], array $Context = [])` | `PyOS.TimezoneDayEndUTC()` |
### <span id="PyOS-toggle"></span>PyOS.Toggle
- **Purpose**: Enterprise-grade Feature Toggle / Flag / Kill-Switch Engine Multi-App, Multi-Brand, Multi-Tenant, User-aware resolution Ultra-fast read path using snapshot + cache Safe defaults + deterministic decisions
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Toggle.py](PyOS/Py.26.00.00/Engine/PyOS.Toggle.py)
- **Class**: `PyOS_Toggle`
- **Facade Entry Points**:
  - `PyOS.Toggle()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.ToggleGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.ToggleValue()` → `public static function Value(array $Payload, array $Context)`
  - `PyOS.ToggleBatch()` → `public static function Batch(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Toggle()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.ToggleGet()` |
| Value | `public static function Value(array $Payload, array $Context)` | `PyOS.ToggleValue()` |
| Batch | `public static function Batch(array $Payload, array $Context)` | `PyOS.ToggleBatch()` |
### <span id="PyOS-trace"></span>PyOS.Trace
- **Purpose**: Distributed tracing for PyOS (API, Cron, Queue, Worker, Pipeline) Ultra-low overhead, async-safe, multi-tenant aware Correlates EngineRouter → Engine → DB → HTTP → Queue
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Trace.py](PyOS/Py.26.00.00/Engine/PyOS.Trace.py)
- **Class**: `PyOS_Trace`
- **Facade Entry Points**:
  - `PyOS.Trace()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.TraceStart()` → `public static function Start(array $Payload = [], array $Context = [])`
  - `PyOS.TraceSpanStart()` → `public static function SpanStart(array $Payload = [], array $Context = [])`
  - `PyOS.TraceSpanEnd()` → `public static function SpanEnd(array $Payload = [], array $Context = [])`
  - `PyOS.TraceTag()` → `public static function Tag(array $Payload = [], array $Context = [])`
  - `PyOS.TraceError()` → `public static function Error(array $Payload = [], array $Context = [])`
  - `PyOS.TraceEnd()` → `public static function End(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Trace()` |
| Start | `public static function Start(array $Payload = [], array $Context = [])` | `PyOS.TraceStart()` |
| SpanStart | `public static function SpanStart(array $Payload = [], array $Context = [])` | `PyOS.TraceSpanStart()` |
| SpanEnd | `public static function SpanEnd(array $Payload = [], array $Context = [])` | `PyOS.TraceSpanEnd()` |
| Tag | `public static function Tag(array $Payload = [], array $Context = [])` | `PyOS.TraceTag()` |
| Error | `public static function Error(array $Payload = [], array $Context = [])` | `PyOS.TraceError()` |
| End | `public static function End(array $Payload = [], array $Context = [])` | `PyOS.TraceEnd()` |
### <span id="PyOS-transaction"></span>PyOS.Transaction
- **Purpose**: Universal Transaction Engine for PyOS Supports any backend: Wallet, E-Commerce, SaaS, MLM, Banking-style systems Multi-Tenant, Multi-App, Enterprise-grade integrity
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Transaction.py](PyOS/Py.26.00.00/Engine/PyOS.Transaction.py)
- **Class**: `PyOS_Transaction`
- **Facade Entry Points**:
  - `PyOS.Transaction()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.TransactionNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.TransactionGet()` → `public static function Get(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Transaction()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.TransactionNew()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.TransactionGet()` |
### <span id="PyOS-twofactor"></span>PyOS.TwoFactor
- **Purpose**: Provide enterprise‑grade Two‑Factor Authentication (2FA) Support TOTP, Email OTP, SMS OTP, Backup Codes (extensible) Policy‑driven, risk‑aware, step‑up authentication Fully compatible with PyOS.Loader + PyOS.EngineRouter
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.TwoFactor.py](PyOS/Py.26.00.00/Engine/PyOS.TwoFactor.py)
- **Class**: `PyOS_TwoFactor`
- **Facade Entry Points**:
  - `PyOS.TwoFactor()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.TwoFactorStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.TwoFactorChallengeNew()` → `public static function ChallengeNew(array $Payload, array $Context)`
  - `PyOS.TwoFactorChallengeVerify()` → `public static function ChallengeVerify(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.TwoFactor()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.TwoFactorStatus()` |
| ChallengeNew | `public static function ChallengeNew(array $Payload, array $Context)` | `PyOS.TwoFactorChallengeNew()` |
| ChallengeVerify | `public static function ChallengeVerify(array $Payload, array $Context)` | `PyOS.TwoFactorChallengeVerify()` |
### <span id="PyOS-update"></span>PyOS.Update
- **Purpose**: Enterprise-grade Update Orchestration Engine Core / SDK / App / Brand / Tenant updates Zero-downtime (Blue-Green / Canary / Rolling) Safe rollback + audit trail Fully Loader + EngineRouter compatible
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Update.py](PyOS/Py.26.00.00/Engine/PyOS.Update.py)
- **Class**: `PyOS_Update`
- **Facade Entry Points**:
  - `PyOS.Update()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.UpdateCheck()` → `public static function Check(array $Payload, array $Context)`
  - `PyOS.UpdatePlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.UpdateApply()` → `public static function Apply(array $Payload, array $Context)`
  - `PyOS.UpdateRollback()` → `public static function Rollback(array $Payload, array $Context)`
  - `PyOS.UpdateStatus()` → `public static function Status(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Update()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.UpdateCheck()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.UpdatePlan()` |
| Apply | `public static function Apply(array $Payload, array $Context)` | `PyOS.UpdateApply()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.UpdateRollback()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.UpdateStatus()` |
### <span id="PyOS-upgradeplanner"></span>PyOS.UpgradePlanner
- **Purpose**: Deterministic upgrade planning for PyOS ecosystem Multi-App, Multi-Brand, Multi-Tenant safe rollout planning Zero-downtime upgrade strategy generation Risk analysis, dependency ordering, rollback planning
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.UpgradePlanner.py](PyOS/Py.26.00.00/Engine/PyOS.UpgradePlanner.py)
- **Class**: `PyOS_UpgradePlanner`
- **Facade Entry Points**:
  - `PyOS.UpgradePlanner()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.UpgradePlannerPlan()` → `public static function Plan(array $Payload, array $Context)`
  - `PyOS.UpgradePlannerRisk()` → `public static function Risk(array $Payload, array $Context)`
  - `PyOS.UpgradePlannerSteps()` → `public static function Steps(array $Payload, array $Context)`
  - `PyOS.UpgradePlannerRollout()` → `public static function Rollout(array $Payload, array $Context)`
  - `PyOS.UpgradePlannerRollback()` → `public static function Rollback(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.UpgradePlanner()` |
| Plan | `public static function Plan(array $Payload, array $Context)` | `PyOS.UpgradePlannerPlan()` |
| Risk | `public static function Risk(array $Payload, array $Context)` | `PyOS.UpgradePlannerRisk()` |
| Steps | `public static function Steps(array $Payload, array $Context)` | `PyOS.UpgradePlannerSteps()` |
| Rollout | `public static function Rollout(array $Payload, array $Context)` | `PyOS.UpgradePlannerRollout()` |
| Rollback | `public static function Rollback(array $Payload, array $Context)` | `PyOS.UpgradePlannerRollback()` |
### <span id="PyOS-upload"></span>PyOS.Upload
- **Purpose**: Enterprise-grade Upload Engine for PyOS Multi-App, Multi-Tenant, High-Performance Upload OS Layer Supports chunked, resumable, secure uploads
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Upload.py](PyOS/Py.26.00.00/Engine/PyOS.Upload.py)
- **Class**: `PyOS_Upload`
- **Facade Entry Points**:
  - `PyOS.Upload()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.UploadInit()` → `public static function Init(array $Payload, array $Context)`
  - `PyOS.UploadChunk()` → `public static function Chunk(array $Payload, array $Context)`
  - `PyOS.UploadCommit()` → `public static function Commit(array $Payload, array $Context)`
  - `PyOS.UploadAbort()` → `public static function Abort(array $Payload, array $Context)`
  - `PyOS.UploadGet()` → `public static function Get(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Upload()` |
| Init | `public static function Init(array $Payload, array $Context)` | `PyOS.UploadInit()` |
| Chunk | `public static function Chunk(array $Payload, array $Context)` | `PyOS.UploadChunk()` |
| Commit | `public static function Commit(array $Payload, array $Context)` | `PyOS.UploadCommit()` |
| Abort | `public static function Abort(array $Payload, array $Context)` | `PyOS.UploadAbort()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.UploadGet()` |
### <span id="PyOS-user"></span>PyOS.User
- **Purpose**: Enterprise Grade User Engine for PyOS (Multi-App + Multi-Tenant) High-performance read/write with cache-first + strict scope enforcement Centralized user identity, meta, roles integration points OS-grade deterministic outputs, safe errors, audit-ready events
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.User.py](PyOS/Py.26.00.00/Engine/PyOS.User.py)
- **Class**: `PyOS_User`
- **Facade Entry Points**:
  - `PyOS.User()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.User()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.UserActions()` → `public static function Actions()`
  - `PyOS.UserGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.UserFind()` → `public static function Find(array $Payload, array $Context)`
  - `PyOS.UserNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.UserUpdate()` → `public static function Update(array $Payload, array $Context)`
  - `PyOS.UserStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.UserGetByEmail()` → `public static function GetByEmail(array $Payload, array $Context)`
  - `PyOS.UserGetByUserName()` → `public static function GetByUserName(array $Payload, array $Context)`
  - `PyOS.UserGetByPhone()` → `public static function GetByPhone(array $Payload, array $Context)`
  - `PyOS.UserMetaGet()` → `public static function MetaGet(array $Payload, array $Context)`
  - `PyOS.UserMetaSet()` → `public static function MetaSet(array $Payload, array $Context)`
  - `PyOS.UserMetaDelete()` → `public static function MetaDelete(array $Payload, array $Context)`
  - `PyOS.UserMetaBulkGet()` → `public static function MetaBulkGet(array $Payload, array $Context)`
  - `PyOS.UserMetaBulkSet()` → `public static function MetaBulkSet(array $Payload, array $Context)`
  - `PyOS.UserRoleGrant()` → `public static function RoleGrant(array $Payload, array $Context)`
  - `PyOS.UserRoleRevoke()` → `public static function RoleRevoke(array $Payload, array $Context)`
  - `PyOS.UserRoleList()` → `public static function RoleList(array $Payload, array $Context)`
  - `PyOS.UserHasRole()` → `public static function HasRole(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.User()` |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.User()` |
| Actions | `public static function Actions()` | `PyOS.UserActions()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.UserGet()` |
| Find | `public static function Find(array $Payload, array $Context)` | `PyOS.UserFind()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.UserNew()` |
| Update | `public static function Update(array $Payload, array $Context)` | `PyOS.UserUpdate()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.UserStatus()` |
| GetByEmail | `public static function GetByEmail(array $Payload, array $Context)` | `PyOS.UserGetByEmail()` |
| GetByUserName | `public static function GetByUserName(array $Payload, array $Context)` | `PyOS.UserGetByUserName()` |
| GetByPhone | `public static function GetByPhone(array $Payload, array $Context)` | `PyOS.UserGetByPhone()` |
| MetaGet | `public static function MetaGet(array $Payload, array $Context)` | `PyOS.UserMetaGet()` |
| MetaSet | `public static function MetaSet(array $Payload, array $Context)` | `PyOS.UserMetaSet()` |
| MetaDelete | `public static function MetaDelete(array $Payload, array $Context)` | `PyOS.UserMetaDelete()` |
| MetaBulkGet | `public static function MetaBulkGet(array $Payload, array $Context)` | `PyOS.UserMetaBulkGet()` |
| MetaBulkSet | `public static function MetaBulkSet(array $Payload, array $Context)` | `PyOS.UserMetaBulkSet()` |
| RoleGrant | `public static function RoleGrant(array $Payload, array $Context)` | `PyOS.UserRoleGrant()` |
| RoleRevoke | `public static function RoleRevoke(array $Payload, array $Context)` | `PyOS.UserRoleRevoke()` |
| RoleList | `public static function RoleList(array $Payload, array $Context)` | `PyOS.UserRoleList()` |
| HasRole | `public static function HasRole(array $Payload, array $Context)` | `PyOS.UserHasRole()` |
### <span id="PyOS-vector"></span>PyOS.Vector
- **Purpose**: Enterprise-grade Vector & Embedding Management Engine Multi-App + Multi-Tenant + AI-ready Vector Store Semantic Search, Similarity, Hybrid Search foundation
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Vector.py](PyOS/Py.26.00.00/Engine/PyOS.Vector.py)
- **Class**: `PyOS_Vector`
- **Facade Entry Points**:
  - `PyOS.Vector()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.VectorUpsert()` → `public static function Upsert(array $Payload = [], array $Context = [])`
  - `PyOS.VectorDelete()` → `public static function Delete(array $Payload = [], array $Context = [])`
  - `PyOS.VectorGet()` → `public static function Get(array $Payload = [], array $Context = [])`
  - `PyOS.VectorSearch()` → `public static function Search(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Vector()` |
| Upsert | `public static function Upsert(array $Payload = [], array $Context = [])` | `PyOS.VectorUpsert()` |
| Delete | `public static function Delete(array $Payload = [], array $Context = [])` | `PyOS.VectorDelete()` |
| Get | `public static function Get(array $Payload = [], array $Context = [])` | `PyOS.VectorGet()` |
| Search | `public static function Search(array $Payload = [], array $Context = [])` | `PyOS.VectorSearch()` |
### <span id="PyOS-version"></span>PyOS.Version
- **Purpose**: Central Version Intelligence Engine for PyOS Multi-App, Multi-Tenant, Multi-Layer version resolution Enterprise-grade rollout, canary, pinning & compatibility checks
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Version.py](PyOS/Py.26.00.00/Engine/PyOS.Version.py)
- **Class**: `PyOS_Version`
- **Facade Entry Points**:
  - `PyOS.Version()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.VersionResolve()` → `public static function Resolve(array $Payload, array $Context)`
  - `PyOS.VersionGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.VersionCheck()` → `public static function Check(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Version()` |
| Resolve | `public static function Resolve(array $Payload, array $Context)` | `PyOS.VersionResolve()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.VersionGet()` |
| Check | `public static function Check(array $Payload, array $Context)` | `PyOS.VersionCheck()` |
### <span id="PyOS-wallet"></span>PyOS.Wallet
- **Purpose**: Enterprise-grade Wallet Engine for any backend (Website/PyApp/Mobile/SaaS) Multi-App + Multi-Tenant safe by design High-performance read paths + transaction-safe write paths Ledger-ready (double-entry) architecture with idempotency protection ---------------------------------------------------------------------------- DESIGN RULES (Router v7.0 Compatible): PyOS::WalletXxx() -> Engine=Wallet, Action=Xxx All methods static Main($Payload, $Context) mandatory fallback entry ---------------------------------------------------------------------------- DEPENDENCIES (Engines): PyOS::Security()   : validation helpers (optional, but recommended) PyOS::Auth()       : session/user validation (optional at engine level) PyOS::Role()       : role checks (optional at engine level) PyOS::Permission() : permission checks (recommended) PyOS::Database()   : database operations (CRUD abstraction) PyOS::Lock()       : concurrency control (recommended) PyOS::Cache()      : cache (read optimization) PyOS::Log()        : structured logs (optional) PyOS::Audit()      : audit trail (recommended) PyOS::EventBus()   : domain events (optional) ----------------------------------------------------------------------------
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Wallet.py](PyOS/Py.26.00.00/Engine/PyOS.Wallet.py)
- **Class**: `PyOS_Wallet`
- **Facade Entry Points**:
  - `PyOS.Wallet()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.WalletCreate()` → `public static function Create(array $Payload, array $Context)`
  - `PyOS.WalletEnsure()` → `public static function Ensure(array $Payload, array $Context)`
  - `PyOS.WalletGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.WalletBalance()` → `public static function Balance(array $Payload, array $Context)`
  - `PyOS.WalletCredit()` → `public static function Credit(array $Payload, array $Context)`
  - `PyOS.WalletDebit()` → `public static function Debit(array $Payload, array $Context)`
  - `PyOS.WalletTransfer()` → `public static function Transfer(array $Payload, array $Context)`
  - `PyOS.WalletFreeze()` → `public static function Freeze(array $Payload, array $Context)`
  - `PyOS.WalletUnfreeze()` → `public static function Unfreeze(array $Payload, array $Context)`
  - `PyOS.WalletStatement()` → `public static function Statement(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Wallet()` |
| Create | `public static function Create(array $Payload, array $Context)` | `PyOS.WalletCreate()` |
| Ensure | `public static function Ensure(array $Payload, array $Context)` | `PyOS.WalletEnsure()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.WalletGet()` |
| Balance | `public static function Balance(array $Payload, array $Context)` | `PyOS.WalletBalance()` |
| Credit | `public static function Credit(array $Payload, array $Context)` | `PyOS.WalletCredit()` |
| Debit | `public static function Debit(array $Payload, array $Context)` | `PyOS.WalletDebit()` |
| Transfer | `public static function Transfer(array $Payload, array $Context)` | `PyOS.WalletTransfer()` |
| Freeze | `public static function Freeze(array $Payload, array $Context)` | `PyOS.WalletFreeze()` |
| Unfreeze | `public static function Unfreeze(array $Payload, array $Context)` | `PyOS.WalletUnfreeze()` |
| Statement | `public static function Statement(array $Payload, array $Context)` | `PyOS.WalletStatement()` |
### <span id="PyOS-webhook"></span>PyOS.Webhook
- **Purpose**: Enterprise-grade Webhook Operating Layer (Inbound + Outbound) Multi-App + Multi-Tenant compatible by Context + Unit resolver Signature verify (HMAC), replay window support, idempotency protection Fast ACK design + optional async dispatch (Queue/Task if available) Works only via PyOS.Loader / PyOS.EngineRouter (no direct includes)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Webhook.py](PyOS/Py.26.00.00/Engine/PyOS.Webhook.py)
- **Class**: `PyOS_Webhook`
- **Facade Entry Points**:
  - `PyOS.Webhook()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.WebhookIn()` → `public static function In($Payload = [], $Context = [])`
  - `PyOS.WebhookOut()` → `public static function Out($Payload = [], $Context = [])`
  - `PyOS.WebhookRoute()` → `public static function Route($Payload = [], $Context = [])`
  - `PyOS.WebhookVerify()` → `public static function Verify($Payload = [], $Context = [])`
  - `PyOS.WebhookIdempotency()` → `public static function Idempotency($Payload = [], $Context = [])`
  - `PyOS.WebhookNormalize()` → `public static function Normalize($Payload = [], $Context = [])`
  - `PyOS.WebhookDispatch()` → `public static function Dispatch($Payload = [], $Context = [])`
  - `PyOS.WebhookTenant()` → `public static function Tenant($Payload = [], $Context = [])`
  - `PyOS.WebhookScope()` → `public static function Scope($Payload = [], $Context = [])`
  - `PyOS.WebhookRespond()` → `public static function Respond($Payload = [], $Context = [], $HttpCode = 200)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Webhook()` |
| In | `public static function In($Payload = [], $Context = [])` | `PyOS.WebhookIn()` |
| Out | `public static function Out($Payload = [], $Context = [])` | `PyOS.WebhookOut()` |
| Route | `public static function Route($Payload = [], $Context = [])` | `PyOS.WebhookRoute()` |
| Verify | `public static function Verify($Payload = [], $Context = [])` | `PyOS.WebhookVerify()` |
| Idempotency | `public static function Idempotency($Payload = [], $Context = [])` | `PyOS.WebhookIdempotency()` |
| Normalize | `public static function Normalize($Payload = [], $Context = [])` | `PyOS.WebhookNormalize()` |
| Dispatch | `public static function Dispatch($Payload = [], $Context = [])` | `PyOS.WebhookDispatch()` |
| Tenant | `public static function Tenant($Payload = [], $Context = [])` | `PyOS.WebhookTenant()` |
| Scope | `public static function Scope($Payload = [], $Context = [])` | `PyOS.WebhookScope()` |
| Respond | `public static function Respond($Payload = [], $Context = [], $HttpCode = 200)` | `PyOS.WebhookRespond()` |
### <span id="PyOS-wellness"></span>PyOS.Wellness
- **Purpose**: Universal Wellness Domain Engine for PyOS (Multi-App + Multi-Tenant) Standardize Wellness Profile, Events, Habits, Goals, Aggregates High-performance, audit-friendly, permission-based backend layer
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Wellness.py](PyOS/Py.26.00.00/Engine/PyOS.Wellness.py)
- **Class**: `PyOS_Wellness`
- **Facade Entry Points**:
  - `PyOS.Wellness()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.WellnessProfileGet()` → `public static function ProfileGet(array $Payload, array $Context)`
  - `PyOS.WellnessProfileSet()` → `public static function ProfileSet(array $Payload, array $Context)`
  - `PyOS.WellnessEventIngest()` → `public static function EventIngest(array $Payload, array $Context)`
  - `PyOS.WellnessDailyGet()` → `public static function DailyGet(array $Payload, array $Context)`
  - `PyOS.WellnessHabitNew()` → `public static function HabitNew(array $Payload, array $Context)`
  - `PyOS.WellnessGoalNew()` → `public static function GoalNew(array $Payload, array $Context)`
  - `PyOS.WellnessScoreCompute()` → `public static function ScoreCompute(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Wellness()` |
| ProfileGet | `public static function ProfileGet(array $Payload, array $Context)` | `PyOS.WellnessProfileGet()` |
| ProfileSet | `public static function ProfileSet(array $Payload, array $Context)` | `PyOS.WellnessProfileSet()` |
| EventIngest | `public static function EventIngest(array $Payload, array $Context)` | `PyOS.WellnessEventIngest()` |
| DailyGet | `public static function DailyGet(array $Payload, array $Context)` | `PyOS.WellnessDailyGet()` |
| HabitNew | `public static function HabitNew(array $Payload, array $Context)` | `PyOS.WellnessHabitNew()` |
| GoalNew | `public static function GoalNew(array $Payload, array $Context)` | `PyOS.WellnessGoalNew()` |
| ScoreCompute | `public static function ScoreCompute(array $Payload, array $Context)` | `PyOS.WellnessScoreCompute()` |
### <span id="PyOS-withdraw"></span>PyOS.Withdraw
- **Purpose**: Enterprise Grade Withdraw Engine for Multi-App + Multi-Tenant PyOS.
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Withdraw.py](PyOS/Py.26.00.00/Engine/PyOS.Withdraw.py)
- **Class**: `PyOS_Withdraw`
- **Facade Entry Points**:
  - `PyOS.Withdraw()` → `public static function Main(array $Payload, array $Context)`
  - `PyOS.WithdrawQuote()` → `public static function Quote(array $Payload, array $Context)`
  - `PyOS.WithdrawNew()` → `public static function New(array $Payload, array $Context)`
  - `PyOS.WithdrawValidate()` → `public static function Validate(array $Payload, array $Context)`
  - `PyOS.WithdrawApprove()` → `public static function Approve(array $Payload, array $Context)`
  - `PyOS.WithdrawProcess()` → `public static function Process(array $Payload, array $Context)`
  - `PyOS.WithdrawCancel()` → `public static function Cancel(array $Payload, array $Context)`
  - `PyOS.WithdrawStatus()` → `public static function Status(array $Payload, array $Context)`
  - `PyOS.WithdrawGet()` → `public static function Get(array $Payload, array $Context)`
  - `PyOS.WithdrawList()` → `public static function List(array $Payload, array $Context)`
  - `PyOS.WithdrawFee()` → `public static function Fee(array $Payload, array $Context)`
  - `PyOS.WithdrawLimits()` → `public static function Limits(array $Payload, array $Context)`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload, array $Context)` | `PyOS.Withdraw()` |
| Quote | `public static function Quote(array $Payload, array $Context)` | `PyOS.WithdrawQuote()` |
| New | `public static function New(array $Payload, array $Context)` | `PyOS.WithdrawNew()` |
| Validate | `public static function Validate(array $Payload, array $Context)` | `PyOS.WithdrawValidate()` |
| Approve | `public static function Approve(array $Payload, array $Context)` | `PyOS.WithdrawApprove()` |
| Process | `public static function Process(array $Payload, array $Context)` | `PyOS.WithdrawProcess()` |
| Cancel | `public static function Cancel(array $Payload, array $Context)` | `PyOS.WithdrawCancel()` |
| Status | `public static function Status(array $Payload, array $Context)` | `PyOS.WithdrawStatus()` |
| Get | `public static function Get(array $Payload, array $Context)` | `PyOS.WithdrawGet()` |
| List | `public static function List(array $Payload, array $Context)` | `PyOS.WithdrawList()` |
| Fee | `public static function Fee(array $Payload, array $Context)` | `PyOS.WithdrawFee()` |
| Limits | `public static function Limits(array $Payload, array $Context)` | `PyOS.WithdrawLimits()` |
### <span id="PyOS-worker"></span>PyOS.Worker
- **Purpose**: OS-grade background job execution engine Multi-App + Multi-Tenant + Fair Scheduling Safe async execution with retries, locks, metrics
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Worker.py](PyOS/Py.26.00.00/Engine/PyOS.Worker.py)
- **Class**: `PyOS_Worker`
- **Facade Entry Points**:
  - `PyOS.Worker()` → `public static function Main(array $Payload = [], array $Context = [])`
  - `PyOS.WorkerRun()` → `public static function Run(array $Payload = [], array $Context = [])`
  - `PyOS.WorkerOnce()` → `public static function Once(array $Payload = [], array $Context = [])`
  - `PyOS.WorkerExecute()` → `public static function Execute(array $Payload = [], array $Context = [])`
  - `PyOS.WorkerStats()` → `public static function Stats(array $Payload = [], array $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main(array $Payload = [], array $Context = [])` | `PyOS.Worker()` |
| Run | `public static function Run(array $Payload = [], array $Context = [])` | `PyOS.WorkerRun()` |
| Once | `public static function Once(array $Payload = [], array $Context = [])` | `PyOS.WorkerOnce()` |
| Execute | `public static function Execute(array $Payload = [], array $Context = [])` | `PyOS.WorkerExecute()` |
| Stats | `public static function Stats(array $Payload = [], array $Context = [])` | `PyOS.WorkerStats()` |
### <span id="PyOS-workflow"></span>PyOS.Workflow
- **Purpose**: Workflow-as-OS-Resource for PyOS backend Multi-App + Multi-Tenant workflow definition + execution State/DAG/Saga-ready (v1 focuses on deterministic sequential execution with controlled branching hooks; graph expansion is forward compatible)
- **File**: [PyOS/Py.26.00.00/Engine/PyOS.Workflow.py](PyOS/Py.26.00.00/Engine/PyOS.Workflow.py)
- **Class**: `PyOS_Workflow`
- **Facade Entry Points**:
  - `PyOS.Workflow()` → `public static function Main($Payload = [], $Context = [])`
  - `PyOS.WorkflowNew()` → `public static function New($Payload = [], $Context = [])`
  - `PyOS.WorkflowUpdate()` → `public static function Update($Payload = [], $Context = [])`
  - `PyOS.WorkflowGet()` → `public static function Get($Payload = [], $Context = [])`
  - `PyOS.WorkflowList()` → `public static function List($Payload = [], $Context = [])`
  - `PyOS.WorkflowValidate()` → `public static function Validate($Payload = [], $Context = [])`
  - `PyOS.WorkflowDeploy()` → `public static function Deploy($Payload = [], $Context = [])`
  - `PyOS.WorkflowRun()` → `public static function Run($Payload = [], $Context = [])`
  - `PyOS.WorkflowResume()` → `public static function Resume($Payload = [], $Context = [])`
  - `PyOS.WorkflowCancel()` → `public static function Cancel($Payload = [], $Context = [])`
  - `PyOS.WorkflowStatus()` → `public static function Status($Payload = [], $Context = [])`

| Method | Signature | Facade |
| --- | --- | --- |
| Main | `public static function Main($Payload = [], $Context = [])` | `PyOS.Workflow()` |
| New | `public static function New($Payload = [], $Context = [])` | `PyOS.WorkflowNew()` |
| Update | `public static function Update($Payload = [], $Context = [])` | `PyOS.WorkflowUpdate()` |
| Get | `public static function Get($Payload = [], $Context = [])` | `PyOS.WorkflowGet()` |
| List | `public static function List($Payload = [], $Context = [])` | `PyOS.WorkflowList()` |
| Validate | `public static function Validate($Payload = [], $Context = [])` | `PyOS.WorkflowValidate()` |
| Deploy | `public static function Deploy($Payload = [], $Context = [])` | `PyOS.WorkflowDeploy()` |
| Run | `public static function Run($Payload = [], $Context = [])` | `PyOS.WorkflowRun()` |
| Resume | `public static function Resume($Payload = [], $Context = [])` | `PyOS.WorkflowResume()` |
| Cancel | `public static function Cancel($Payload = [], $Context = [])` | `PyOS.WorkflowCancel()` |
| Status | `public static function Status($Payload = [], $Context = [])` | `PyOS.WorkflowStatus()` |

## Missing / Unknown
The following engines had incomplete metadata during automated extraction:
- PyOS.Auth: No public static methods found
- PyOS.Env: No public static methods found
- PyOS.Env: Purpose not documented
- PyOS.Row: No public static methods found
