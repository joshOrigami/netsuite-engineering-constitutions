.specify/memory/NetSuite_SuiteScript_Constitution.md


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


# NetSuite SuiteScript Constitution (Universal)


## 0) Purpose


This Constitution defines **non-negotiable engineering rules** for NetSuite SuiteScript development. Its purpose is to prevent behavioral drift, avoid destructive rewrites, enforce reuse, and ensure scripts remain safe, performant, and supportable across Sandbox and Production environments.


---


## 1) Prime Directive


- Preserve existing business behavior unless a specification explicitly changes it.
- Prefer small, reviewable changes over large refactors.
- Never rewrite existing scripts or shared logic unless:
  - an Architectural Decision Record (ADR) exists, and
  - behavior is preserved via tests or documented verification steps.


---


## 2) Scope & Definitions


- **Script Types**: Client, User Event, Suitelet, Map/Reduce, Scheduled, Restlet, Workflow Action, Portlet.
- **Record Operations**: create, load, submitFields, delete, line edits, searches, SuiteQL.
- **Governance**: NetSuite usage units, execution limits, and concurrency constraints.


---


## 3) Reuse & Modularity Invariants


- Shared business logic MUST be reused; duplication is forbidden.
- Entry-point scripts MUST be orchestration-focused.
- Shared logic SHOULD be isolated from NetSuite runtime concerns.
- New modules must have a single responsibility.


---


## 4) Record Lifecycle & Safety Rules


- Scripts MUST NOT assume a single invocation context.
- Logic must behave correctly across UI, CSV, REST, inline edit, and scripted entry.
- Record writes must be minimized.
- Writing back to the triggering record requires justification.
- Self-triggering scripts MUST include recursion prevention.
- Errors must never be silently swallowed.


---


## 5) Performance & Governance Constraints


- Avoid per-record load/save loops.
- Searches must be filtered and paginated.
- MR/Scheduled scripts must be idempotent and restart-safe.
- SuiteQL must be parameterized.
- Concurrency must always be assumed.


---


## 6) Canonical Data & Boundary Invariants


### 6.1 Identifier & Metadata Stability


- **No NetSuite internalId is assumed stable across environments.**
- All internalIds must be resolved via configuration or mapping.
- External identifiers must be preserved when available.
- Metadata dependencies (fields, records, forms, searches) must be explicitly declared.


### 6.2 Currency & FX


- Currency must always be explicit.
- Base currency must never be assumed.
- FX source and effective date must be explicit.
- Historical transactions must not be implicitly revalued.


### 6.3 Subsidiary & Legal Entity Boundaries


- Subsidiary context must always be explicit.
- Default or inferred subsidiaries are forbidden.
- Cross-subsidiary behavior requires explicit design approval.


Violations are **critical defects**.


### 6.4 Chart of Accounts (CoA)


- Account internalIds MUST NOT be hard-coded.
- Account references must use:
  - account numbers, or
  - external identifiers, or
  - explicit environment mappings.
- Scripts must not assume identical CoA structure across environments.


### 6.5 Tax & Localization


- Tax behavior must be explicit.
- Scripts must not assume tax is calculated automatically.
- Locale-specific tax logic (VAT/GST/Sales Tax) must be declared.
- Gross vs net amount semantics must be explicit.


### 6.6 NetSuite Feature Dependencies


- Scripts must declare required NetSuite features explicitly.
- Feature availability must not be assumed.
- OneWorld, Multi-Book, ARM, SuiteBilling, Fixed Assets, etc. must be treated as optional capabilities.
- Feature-dependent logic must fail safely if unavailable.


---


## 7) Logging, Observability, and Debuggability


Logs MUST include:
- script name or identifier
- execution context
- record type and record ID (when applicable)
- correlation or execution identifier (when available)


Sensitive or regulated data MUST NOT be logged.


---


## 8) Error Handling & User Messaging


- Errors must be deterministic and diagnosable.
- Client scripts must present user-friendly messages.
- Server-side scripts must validate inputs early and avoid partial writes.
- Error handling behavior must be documented when non-obvious.


---


## 9) Script-Type Guardrails


### 9.1 Client Scripts (CS)
- Must not contain business-critical enforcement logic.
- Must handle Edit and Inline Edit consistently.
- UI handlers must remain lightweight.


### 9.2 User Event Scripts (UE)
- beforeLoad: UI decoration only.
- beforeSubmit: validation and derived field logic.
- afterSubmit: side effects only.
- Recursion prevention is mandatory.


### 9.3 Suitelets (SL)
- Explicit authorization is required.
- GET requests must be safe and side-effect free.
- Long-running work must be delegated to async processes.


### 9.4 Map/Reduce (MR)
- Idempotent per input key.
- Restart-safe with clear auditability.


### 9.5 Scheduled Scripts (SCH)
- Must be safe to re-run without duplication.


### 9.6 Restlets (RL)
- Stable API contracts are required.
- All inputs must be validated.
- Idempotency is strongly encouraged.


### 9.7 Workflow Action Scripts (WFA)
- Must be deterministic and fast.
- Heavy logic must be delegated elsewhere.


---


## 10) Testing & Verification Requirements


Every change MUST include at least one of:
- automated tests for pure logic
- deterministic verification steps
- sandbox execution evidence


High-risk changes require regression verification.


---


## 11) Deployment & Environment Safety


- Production changes require:
  - approved specification
  - execution plan
  - verification evidence
- Environment-specific identifiers must be isolated and documented.
- Script parameters and deployments must be reviewed explicitly.


---


## 12) Security & Access Control


- Secrets must never be stored in code nor in script/company parameters.
- Least-privilege roles are required.
- Scripts exposing data or actions must enforce authorization explicitly.


---


## 13) Documentation & Decision Records


- ADRs are required for:
  - refactors
  - new architectural patterns
  - behavior changes with broad impact
- Runbooks are required for:
  - scheduled processes
  - Map/Reduce jobs
  - operationally critical scripts


---


## 14) SDD Rules of Engagement


- Work must start from a specification, not ad-hoc prompts.
- Plans and tasks are required for non-trivial changes.
- Changes must be tightly scoped.
- When constraints are unclear or conflicting, work MUST stop and escalate.