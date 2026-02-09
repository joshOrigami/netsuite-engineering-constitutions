.specify/memory/NetSuite_Integration_Constitution.md 


## Disclaimer


This document is provided **“as is”** and **“as available”**, without warranties of any kind. The author assumes no responsibility for errors, omissions, or outcomes resulting from the use of this material.


All design, implementation, security, compliance, and operational decisions made using this document are the sole responsibility of the user.


This document does not constitute legal, security, accounting, or compliance advice.


You may **reuse, modify, and distribute** this document freely.
Attribution to the original author or source is appreciated when shared publicly.


Authored by Joshua Meiri at Origami Precision for NetSuite SDD workflows.


Version: 1.0  
Date: 2026-02-08


---


# NetSuite Integration Constitution (Universal)


## 0) Purpose


This Constitution defines **non-negotiable engineering rules** for integrations that read from or write to NetSuite, including inbound, outbound, batch, and real-time flows.


It exists to prevent:
- silent data corruption
- duplicate or non-idempotent writes
- code drift
- schema surprises across environments
- destructive or ungoverned refactors


These rules apply regardless of transport (REST, RESTlet, CSV, SOAP, middleware).


---


## 1) Prime Directives


- **Data correctness overrides convenience.**
- **Contracts define reality.**
- **All writes must be idempotent.**
- **Currency, tax, and subsidiary context must be explicit.**
- **If behavior is unclear, implementation must stop.**


---


## 2) Scope & Integration Modes


- **Inbound**: External system → NetSuite
- **Outbound**: NetSuite → External system
- **Batch**: File-based or scheduled flows
- **Backfill / Replay**: Historical or corrective runs


These rules apply equally to custom code, middleware, and vendor-managed connectors.


---


## 3) Canonical Data & Boundary Invariants


### 3.1 Identifier Stability & Mapping


- **No NetSuite internalId is assumed stable across environments.**
- All identifiers must be mapped explicitly.
- Exactly one idempotency key must exist per write path.
- Correlation identifiers are mandatory.


### 3.2 Currency & FX


- Currency must always be explicit.
- FX source and effective date must be declared.
- Replays must preserve original FX semantics.


### 3.3 Subsidiary & Legal Entity Boundaries


- Subsidiary context must always be explicit.
- Default subsidiaries are forbidden.
- Cross-subsidiary behavior requires explicit contract.
- Subsidiary IDs must not be assumed stable across environments.


Violations are critical integration defects.


### 3.4 Chart of Accounts (CoA)


- Integrations must not hard-code account internalIds.
- Account mapping must be explicit and environment-aware.
- Account structure differences must be assumed.


### 3.5 Tax & Localization


- Tax calculation responsibility must be explicit.
- Locale-specific tax regimes must be declared.
- Gross vs net semantics must be explicit.
- Integrations must not overwrite tax implicitly.


### 3.6 NetSuite Feature Dependencies


- Required NetSuite features must be declared.
- Feature availability must not be assumed.
- OneWorld, Multi-Book, ARM, SuiteBilling, Fixed Assets are optional capabilities.
- Integrations must fail safely if required features are unavailable.


---


## 4) Contracts & Versioning


- Every integration boundary requires a versioned contract:
  - schema
  - semantics
  - error model
  - examples
- Additive changes must be backward-compatible.
- Breaking changes require:
  - ADR
  - version bump
  - migration plan
  - deprecation window
- Code changes that alter behavior are forbidden without a contract update.


---


## 5) Idempotency & De-duplication


- All write paths MUST be idempotent.
- At-least-once delivery must be assumed.
- Acceptable strategies:
  - externalId upsert
  - idempotency key fields
  - persistent mapping tables
- De-duplication rules must be explicit per entity.
- Ordering rules must be defined where updates can arrive out of order.


---


## 6) Retry, Failure, and Recovery


- Retries must be bounded.
- Exponential backoff with jitter is required.
- Errors must be classified:
  - retryable
  - non-retryable
- Exhausted failures must be persisted with:
  - payload snapshot (redacted)
  - error classification
  - correlation id
  - remediation guidance


Partial batch failures must retry only failed items and avoid duplication.


---


## 7) Reconciliation & Auditability


- Every integration must support reconciliation:
  - counts
  - sampling
  - checksum or diff where feasible
- Audit trails must capture:
  - before/after values (or diff)
  - timestamp
  - source system
  - correlation id
- Backfills must be idempotent, restart-safe, and verifiable.


---


## 8) NetSuite-Specific Constraints


- Prefer stable, supported APIs.
- RESTlets require explicit contracts and validation.
- Governance limits must be respected.
- Per-record load/save loops are forbidden in batch flows.
- Entity dependency ordering must be explicit.


---


## 9) Security & Access Control


- Least-privilege service roles are required.
- Secrets must never be stored in code or logs.
- Authentication and authorization must be explicit.
- Sensitive data must be redacted in logs and payload snapshots.


---


## 10) Observability Requirements


Logs must include:
- integration name
- direction (inbound/outbound)
- environment
- entity and identifier
- operation
- result
- correlation id


Metrics must include:
- processed
- success
- failure
- retry counts
- latency


---


## 11) Testing & Verification


Each integration change must include:
- contract validation tests
- idempotency replay tests
- sandbox execution evidence


High-risk flows require end-to-end verification.


---


## 12) Release & Change Management


- Releases require:
  - updated contracts
  - migration and rollback plans
  - monitoring and alert readiness
- Feature flags are recommended for new entities or behaviors.


---


## 13) ADR Requirements


An ADR is required for:
- changing FX or subsidiary semantics
- changing idempotency strategy
- introducing new integration patterns
- breaking contract changes
- major refactors or rewrites


---


## 14) SDD Rules of Engagement


- Start from specification, not prompts.
- Contracts must exist before implementation.
- Plans and tasks are required for non-trivial work.
- Scope must be tightly controlled.
- If constraints are unclear or contradictory, work MUST stop.