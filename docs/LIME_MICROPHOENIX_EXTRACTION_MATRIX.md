---
title: "Lime MicroPhoenix Extraction Matrix"
status: "active"
version: "1.2.0"
last_updated: "2026-04-14"
date: "2026-04-14"
tags: [lime, microphoenix, extraction, matrix, standalone, mvp, architecture]
mode: "design"
---

# Lime MicroPhoenix Extraction Matrix

## Purpose

This document defines the exact extraction boundary between MicroPhoenix and Lime.

Its role is to prevent two symmetric failures:

1. **under-extraction** — shipping a toy backend that loses the durable engineering advantages already solved in MicroPhoenix;
2. **over-extraction** — dragging platform-scale complexity into a standalone MVP until it stops being an MVP.

The rule is simple:

> Lime preserves MicroPhoenix's strongest software-engineering invariants, but it does not inherit MicroPhoenix's platform-scale surface area.

## Extraction Discipline

### Extraction Classes

Every candidate pattern must be classified as one of four kinds:

| Class | Meaning | Lime action |
| --- | --- | --- |
| **Preserve** | essential architectural primitive already proven in MicroPhoenix | carry into MVP now |
| **Adapt** | valuable pattern whose current form is too large for Lime | simplify for MVP |
| **Defer** | strategically useful but not needed for first release | schedule for v1.1+ |
| **Exclude** | platform-only or domain-specific surface that would distort Lime | do not import |

### Reduction Law

When a MicroPhoenix surface has both a **semantic core** and a **platform shell**, Lime extracts only the semantic core.

Examples:

- extract `ILLMAdapter`, not the whole agentic orchestration stack
- extract append-only event recording, not global causal chains and upcasters
- extract bootstrap phases, not thirty DI modules
- extract request correlation, not the full metadata-heavy context graph

## Comprehensive Vector Matrix

This table is the primary extraction control surface for Lime. It covers the full design perimeter so that no major vector is handled implicitly.

| Vector | MicroPhoenix state | Lime class | Lime MVP action | Post-MVP note |
| --- | --- | --- | --- | --- |
| Product identity | platform-of-platforms with many domains | Exclude shell, preserve kernel | present Lime as one focused chat backend | only widen scope if product identity changes |
| Entry chain and bootstrap | explicit phased startup with cleanup | Preserve | keep index -> orchestrator -> app -> listen and reverse shutdown | add more phases only when new runtime lanes appear |
| DI and token registry | large token estate across many domains | Adapt | keep container semantics, shrink token catalog to Lime boundary | split by modules later if token count grows |
| Config and environment | broad Zod schema for many domains and modes | Adapt | keep typed load and fail-fast startup, reduce to chat/runtime variables only | add profiles and secrets backends later |
| Public API surface | large route/controller estate | Adapt | keep chat completions, health, metrics optional, conversation CRUD | add admin/reporting routes only after actual need |
| Provider abstraction | clean port/adapter pattern around LLMs | Preserve | keep `ILLMAdapter` and concrete adapters with streaming | add Anthropic/local adapters later |
| Eventing | rich append-only event infrastructure | Adapt | keep immutable events and append-only save path, reduce query surface | add replay/GSN/correlation queries later |
| Persistence | multiple adapters and storage choices | Adapt | in-memory dev mode plus PostgreSQL prod mode | introduce read models or CQRS only if load justifies |
| Request context and tracing | rich ALS context with many fields | Adapt | keep `correlationId`, `requestId`, `startTime`, optional `userId` | add baggage and cross-service trace fields later |
| Logging and metrics | structured logging plus monitoring facade | Preserve and adapt | keep structured logs and a minimal Prometheus metric set | add OpenTelemetry or tracing exporters later |
| Security and trust boundaries | extensive policy surfaces and platform guards | Adapt and exclude | preserve HTTP/API security baseline, exclude platform-only execution firewall surfaces | add richer authz and policy engines later |
| Testing and verification | strong verification culture with many rails | Preserve and adapt | keep unit, integration, architecture, contract-minded verification | broaden to fuzzing, chaos, replay tests later |
| Docker and runtime packaging | production-minded container discipline | Preserve | multi-stage build, named stages, non-root user, healthcheck | distroless and attestation-first releases later |
| CI/CD and supply chain | mature protocol stack | Adapt | CI, CodeQL or equivalent, dependency review, update bot, least-privilege workflows | add SBOM, attestations, signed releases at release tier |
| OSS publication and governance | publication protocol and curated exports | Adapt | README, LICENSE, SECURITY, CONTRIBUTING, CODE_OF_CONDUCT, CODEOWNERS, rulesets | add badges, governance, release evidence packet later |
| MCP, agents, memory, extraction control plane | platform shell | Exclude | do not import into Lime MVP | revisit only if Lime becomes a platform instead of a product |

## April 2026 Calibration

The extraction matrix is calibrated against current external best practice rather than only internal taste.

### Version And Runtime Baseline

- Node.js Active LTS line: `24.x`
- Express stable line: `5.2.x`
- TypeScript stable line: `6.0.x`
- Vitest stable line: `4.1.x`
- OpenAI SDK stable line: `6.34.x`

### External Practice Alignment

| External baseline | What Lime adopts | What Lime intentionally does not copy |
| --- | --- | --- |
| OpenAI official guidance (Responses API recommended for all new projects; GPT-5.4 latest; Chat Completions CRUD now available) | provider-neutral adapter design, request IDs, retry-aware error handling, streaming support, awareness of Responses API upgrade path | hard lock-in to either API; Chat Completions limitations for GPT-5.4 tool calling with `reasoning: none` accepted as known risk with documented upgrade plan |
| Express 5 guidance | async handler semantics, safer defaults, middleware discipline | broad framework plugin ecosystems by default |
| TypeScript 6 guidance | `NodeNext`, explicit `types`, explicit `rootDir`, no deprecated module resolution | legacy TS config patterns like `baseUrl` or `moduleResolution: node` |
| Vitest 4 guidance | explicit coverage include, modern ESM test runner, project splits | legacy Jest-global assumptions |
| Docker current guidance | named multi-stage builds, BuildKit-friendly stages, slim runtime | heavyweight prod image hardening before MVP exists |
| GitHub and OpenSSF guidance | branch rulesets, push protection, least-privilege workflows, dependency review, SBOM/attestation path | enterprise-scale process overhead before codebase maturity |
| SLSA v1.2 | provenance-aware release posture | pretending MVP needs full L3/L4 controls from day one |

### Practical Interpretation

This means Lime should be:

- modern, not legacy-compatible by default
- secure-by-default at the HTTP and repository boundary
- release-aware, but not process-bloated
- product-shaped, not platform-shaped

## Preserve For Lime MVP

### 1. DI Container Kernel

**Why preserve**

This is the smallest mechanism that enforces inversion of control, testability, and future provider swap-ability.

**MicroPhoenix sources**

- `src/core/di/container.ts`
- `src/core/di/DI_TOKENS.ts`

**Lime action**

- keep factory-based registration
- keep singleton and transient lifecycles
- keep reverse-order disposal
- shrink token registry to the Lime boundary only

**Lime scope**

- `Config`
- `Logger`
- `RequestContextAccessor`
- `EventStore`
- `EventBus`
- `ConversationRepository`
- `LLMAdapter`
- `Cache`
- `ChatService`
- controllers only if container-resolved

### 2. Result Monad

**Why preserve**

This is one of the highest ROI extractions. It removes scattered throw/catch noise in expected business failures while preserving explicitness.

**MicroPhoenix source**

- `src/core/result.ts`

**Lime action**

- preserve nearly 1:1
- use for domain/application flows and adapter error mapping

### 3. Port/Adapter Boundary

**Why preserve**

This is the architectural hinge that makes Lime more than an OpenAI wrapper.

**MicroPhoenix sources**

- `src/domain/ai/ILLMAdapter.ts`
- `src/infrastructure/ai/OpenAIAdapter.ts`
- `src/domain/events/event-store.interface.ts`

**Lime action**

- preserve the port concept strictly
- narrow the interface surface to MVP needs

### 4. Typed Config Loading

**Why preserve**

This is where many standalone projects decay first. Typed config is foundational, not optional.

**MicroPhoenix source**

- `src/config/index.ts`

**Lime action**

- preserve `loadConfigFromEnv()` pattern
- preserve Zod validation
- preserve fail-fast startup
- remove all non-chat and non-runtime-relevant branches

### 5. Bootstrap And Graceful Shutdown

**Why preserve**

Lifecycle correctness is a core platform lesson that should not be relearned in Lime.

**MicroPhoenix sources**

- `src/core/bootstrap/orchestrator.ts`
- `src/index.ts`

**Lime action**

- preserve explicit bootstrap phases
- preserve signal handling
- preserve reverse cleanup of singleton resources
- reduce startup gates to what the standalone product actually needs

### 6. Request Correlation Context

**Why preserve**

Correlation IDs are the minimum viable observability primitive.

**MicroPhoenix source family**

- request context and middleware surfaces referenced in app/bootstrap layers

**Lime action**

- keep `AsyncLocalStorage`
- keep `correlationId` and `requestId`
- drop secondary metadata fields from MVP

### 7. Structured Logging

**Why preserve**

This is necessary for API operations, debugging, and future production adoption.

**MicroPhoenix source family**

- structured logger abstractions and implementations

**Lime action**

- preserve interface concept
- implement with `pino`
- use child loggers for correlation binding

## Adapt For Lime MVP

### 1. Event Store Surface

**MicroPhoenix shape**

Rich event-store contracts cover replay, correlation queries, event type scans, ranges, versions, and snapshots.

**Lime MVP need**

Only:

- `saveEvents(events)`
- `getEventsByAggregateId(id)`
- optional `getAllEvents()` for debugging/export

**Adaptation principle**

Preserve append-only semantics, not the full query estate.

### 2. Config Schema Size

**MicroPhoenix shape**

Large, deeply nested, platform-wide schema spanning healthcare, memory, extraction, and multiple runtime domains.

**Lime MVP need**

Roughly ten variables:

- `NODE_ENV`
- `PORT`
- `LOG_LEVEL`
- `CORS_ORIGINS`
- `API_KEY?`
- `DATABASE_URL?`
- `REDIS_URL?`
- `OPENAI_API_KEY?`
- `OPENAI_BASE_URL?`
- `OPENAI_MODEL`

**Adaptation principle**

Preserve typed loading, remove cross-platform schema sprawl.

### 3. Bootstrap Phasing

**MicroPhoenix shape**

Broad phase system coordinating extensive infrastructure and policy surfaces.

**Lime MVP need**

Three phases only:

1. config and logger
2. container and adapter registration
3. HTTP app creation and listen

**Adaptation principle**

Preserve explicit lifecycle, reduce orchestration breadth.

### 4. Express App Composition

**MicroPhoenix shape**

Many cross-cutting concerns, route registrars, platform middleware, and specialized surfaces.

**Lime MVP need**

- security middleware
- request logging
- request context
- auth optional
- rate limiting
- 3 route groups maximum

**Adaptation principle**

Preserve middleware order discipline, reduce route estate.

### 5. Request Context Payload

**MicroPhoenix shape**

Rich context object carrying multiple tracing and environment fields.

**Lime MVP need**

- `correlationId`
- `requestId`
- `startTime`
- optional `userId`

**Adaptation principle**

Preserve propagation, reduce payload cardinality.

## Defer To Lime v1.1+

### 1. Event Causation Chains

Preserve only after asynchronous jobs, message edits, forks, or replay semantics create real need.

### 2. Global Sequence Number And Replay Windows

Useful for multi-worker coordination or analytics replay, unnecessary in first-release chat backend.

### 3. CQRS Read Models

Needed when list/search/analytics latency requires denormalized projections. Not needed for initial CRUD and chat completion flow.

### 4. Embeddings And Semantic Retrieval

Potentially valuable for RAG or prompt memory, but not required to deliver a strong chat-completions-compatible backend.

### 5. HMAC-Signed Internal Protocols

Relevant once Lime has worker-to-control-plane or multi-service internal traffic. Overkill for same-process MVP.

### 6. Lease, Heartbeat, And Claim Protocols

Relevant for asynchronous distributed workers, not for synchronous request/response inference.

### 7. Release Evidence Packet Automation

Valuable at public-release stage, but can follow once the codebase itself is real.

## Exclude From Lime MVP

### Platform-Only Surfaces

- MCP server estate
- extraction pipeline machinery
- self-learning or TIM memory systems
- multi-agent orchestration
- healthcare-specific modules
- graph-based documentation retrieval infrastructure
- plugin ecosystems and operator workflow control plane

### Why They Must Be Excluded

These surfaces solve platform governance or research workflow problems, not the core Lime product problem.

Importing them would create false architectural coupling, slow iteration, inflate docs, and confuse the public repository identity.

## Best Local Design Patterns To Reuse

### 1. Standalone Product Layering

**Primary local references**

- `docs/superpowers/specs/2026-03-24-mri-standalone-project-design.md`
- `docs/superpowers/specs/2026-03-24-mri-standalone-v1-launch-design.md`

**Reusable insight**

Separate:

- product identity
- runtime scaffold
- launch scope
- publication readiness

Do not collapse them into one giant plan.

### 2. MVP vs Public Launch Separation

**Primary local references**

- `docs/superpowers/specs/2026-03-25-mri-github-mvp-preparation-design.md`
- `docs/superpowers/specs/2026-03-24-release-evidence-packet-bridge-design.md`

**Reusable insight**

Code closure and public GitHub readiness are different gates.

Lime should preserve this distinction:

- MVP closure = runtime and architecture truth
- public launch = repo hygiene, community health, supply-chain posture

### 3. Runtime Scaffold First

**Primary local reference**

- `docs/superpowers/specs/2026-03-24-mri-standalone-runtime-scaffold-plan.md`

**Reusable insight**

Scaffold, then runtime, then API contract, then hardening. This sequence reduces false progress.

## Contradictions And Drift To Watch

### 1. Config Drift

Design-level desire for a minimal standalone project conflicts with the actual size of MicroPhoenix config surfaces.

**Lime rule**: copy pattern, not payload.

### 2. Event Store Drift

The full MicroPhoenix event-store interface is too rich for MVP.

**Lime rule**: preserve append-only semantics, not full query API.

### 3. Request Context Drift

The actual platform context model is richer than Lime needs.

**Lime rule**: preserve correlation semantics only.

### 4. Publication Drift

Large public-launch designs often imply more must ship than an MVP needs.

**Lime rule**: public polish cannot redefine core product scope.

## Implementation Ordering Constraint

To keep extraction disciplined, build in this order:

1. core kernel: DI, Result, config, logger, context
2. domain kernel: conversation, messages, events, ports
3. in-memory infrastructure: mock LLM, in-memory repo, event store, event bus
4. presentation: Express app, DTOs, controllers, SSE
5. production adapters: OpenAI, Postgres, Redis optional
6. hardening: tests, Docker, CI, publication surfaces

If any step introduces platform-only complexity before its prerequisite kernel exists, extraction has gone off-course.

## MVP Preservation Law

Lime MVP is complete when it has:

- OpenAI-compatible chat completions
- streaming
- durable conversations in production mode
- zero-dependency local development mode
- request correlation
- structured logs
- clean architecture boundaries

Lime MVP is no longer an MVP if it acquires:

- agent orchestration
- platform governance subsystems
- plugin or memory frameworks
- diagnostic control-plane machinery
- distributed worker coordination before product need

## Final Extraction Recommendation

The correct extraction from MicroPhoenix into Lime is approximately:

- **preserve almost directly**: DI container, Result monad, config loading model, bootstrap/shutdown discipline, port/adapter structure
- **adapt aggressively**: event store surface, request context richness, app middleware estate, token catalog, config schema size
- **defer intentionally**: causation chains, replay windows, CQRS projections, HMAC protocols, embeddings, evidence packet automation
- **exclude outright**: MCP, multi-agent, healthcare domain, memory planes, extraction systems, platform control plane

That gives Lime the engineering maturity of a serious backend without importing the identity of a much larger platform.
