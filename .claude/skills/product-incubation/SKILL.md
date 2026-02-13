---
name: product-incubation
description: Use when starting a new product, feature, or significant initiative. Covers full lifecycle from vision through deployment, ensuring cross-cutting concerns (security, observability, i18n, auth) are planned upfront rather than bolted on later.
---

# Product Incubation

## Overview

A systematic SOP for incubating products from idea to production. Prevents the common failure mode of building features first, then reactively adding security, observability, i18n, and ops — which causes rework and gaps.

**Core principle:** Every product area gets planned before build starts. Traceability links specs to code to tests — no separate status tracking to maintain.

## When to Use

- Starting a new product or major feature from scratch
- Reviewing an existing product for completeness gaps
- Planning a new phase/milestone that adds significant scope
- When you hear "we should have thought of this earlier"

**Not for:** Bug fixes, minor features, refactors within existing architecture.

## Quick Reference: 18 Product Areas

### Layer 1: WHY (Vision & Context)

| # | Area | Prefix | Done when |
|---|------|--------|-----------|
| 1 | Problem & Vision | `VIS` | Problem statement, target user, market context |
| 2 | User Scenarios | `USR` | Complete user journeys with acceptance criteria |
| 3 | Success Metrics | `KPI` | Measurable KPIs (adoption, performance, business) |

### Layer 2: WHAT (Specification)

| # | Area | Prefix | Done when |
|---|------|--------|-----------|
| 4 | Functional Spec | `FUNC` | Features with input/process/output, scope boundaries |
| 5 | Data Model | `DATA` | Entities, relationships, constraints |
| 6 | API Contract | `API` | Endpoints, request/response shapes, error codes |
| 7 | UX/UI Spec | `UX` | Design direction, breakpoints, a11y requirements |

### Layer 3: HOW (Architecture & Tech)

| # | Area | Prefix | Done when |
|---|------|--------|-----------|
| 8 | Tech Architecture | `ARCH` | System diagram, component boundaries, data flow |
| 9 | Tech Stack | `STACK` | Technologies chosen with rationale (decision records) |
| 10 | Security | `SEC` | Auth model, input validation, OWASP, threat model |
| 11 | Performance | `PERF` | Load targets, latency budgets, scaling strategy |
| 12 | i18n / L10n | `I18N` | Supported locales, string externalization, formatting |

### Layer 4: QUALITY (Validation & Operations)

| # | Area | Prefix | Done when |
|---|------|--------|-----------|
| 13 | Test Strategy | `TEST` | Unit/integration/E2E coverage plan, CI pipeline |
| 14 | Observability | `OBS` | Logging, metrics, error tracking, alerting, health |
| 15 | Deployment | `DEPLOY` | Environment setup, CI/CD, rollback plan |
| 16 | Maintenance & Ops | `OPS` | Backup, monitoring, on-call, runbooks |

### Layer 5: TRACKING (Progress & Decisions)

| # | Area | Prefix | Done when |
|---|------|--------|-----------|
| 17 | Roadmap & Milestones | `ROAD` | Phased delivery plan with priorities |
| 18 | Decision Log | `DEC` | Key decisions with rationale (MADR format) |

## Lifecycle Phases

```
DISCOVER (Areas 1-3) → SPECIFY (Areas 4-7) → ARCHITECT (Areas 8-12) → BUILD → VALIDATE (Areas 13-14) → SHIP (Areas 15-18)
```

### Phase 1: DISCOVER

**Entry:** Idea or problem statement exists.
**Do:** Document problem, target users, market context, user scenarios, success metrics.
**Exit:** Areas 1-3 documented. Stakeholder alignment on problem and scope.
**Artifacts:** `docs/specs/VIS-*.md`, `docs/specs/USR-*.md`

### Phase 2: SPECIFY

**Entry:** Phase 1 complete.
**Do:** Write functional spec, data model, API contract, UX spec. Define scope boundaries (what's NOT included).
**Exit:** Areas 4-7 documented. All features have requirement IDs.
**Artifacts:** `docs/specs/FUNC-*.md`, `docs/specs/DATA-model.md`, `docs/specs/API-contract.md`, `docs/specs/UX-spec.md`

### Phase 3: ARCHITECT

**Entry:** Phase 2 complete.
**Do:** Choose tech stack, design system architecture, plan security/perf/i18n. Write decision records for non-obvious choices.
**Exit:** Areas 8-12 documented. Decision records for all major tech choices.
**Artifacts:** `docs/architecture.md`, `docs/security.md`, `docs/decisions/YYYY-MM-DD-*.md`

### Phase 4: BUILD

**Entry:** Phase 3 complete.
**Do:** Implement features. Tag code with requirement IDs (`# REQ: FUNC-001`). Name tests with IDs (`test_FUNC_001_*`).
**Exit:** All FUNC requirements implemented. Traceability from spec to code to test.

### Phase 5: VALIDATE

**Entry:** Core implementation complete.
**Do:** Execute test strategy, set up observability. Verify coverage against spec.
**Exit:** Areas 13-14 complete. All requirements have test coverage.
**Artifacts:** Test reports, observability config.

### Phase 6: SHIP

**Entry:** Phase 5 complete.
**Do:** Set up deployment pipeline, write runbooks, document ops procedures.
**Exit:** Areas 15-18 complete. Product is production-ready.
**Artifacts:** `docs/deployment.md`, `docs/runbooks/`, decision log up to date.

## Traceability System

Every requirement gets a convention-based ID that appears across all artifacts:

**Format:** `{PREFIX}-{SEQ}` (e.g., `FUNC-001`, `SEC-003`, `PERF-002`)

**Where IDs appear:**

| Location | Format | Example |
|----------|--------|---------|
| Spec files | Requirement definition | `### FUNC-001: CAD file upload` |
| Design docs | Reference | `Addresses FUNC-001, SEC-003` |
| Code | Comment | `# REQ: FUNC-001` |
| Tests | Name | `test_FUNC_001_cad_upload_extracts_blocks` |

**Status derivation (no manual tracking):**

| Condition | Status | Symbol |
|-----------|--------|--------|
| ID in spec only | `todo` | `○` |
| ID in spec + code | `build` | `◐` |
| ID in spec + code + tests | `done` | `✓` |

Use `/product-incubation:audit` to scan and report traceability status.

## Artifact Structure

```
docs/
  specs/                          # Layer 2: WHAT
    VIS-problem.md                # Vision & problem statement
    USR-scenarios.md              # User scenarios
    FUNC-*.md                     # Functional specs with REQ IDs
    DATA-model.md                 # Data model spec
    API-contract.md               # API spec
    UX-spec.md                    # UX/UI spec
  plans/                          # Design docs (existing pattern)
    YYYY-MM-DD-<topic>-design.md
  decisions/                      # MADR-style decision records
    YYYY-MM-DD-<decision>.md
  architecture.md                 # System architecture
  security.md                     # Security model
  observability.md                # Observability plan
  development.md                  # Dev setup guide
  PROJECT-STATUS.md               # Roadmap & status
product-status.yaml               # Auto-generated by audit/sync
```

Use templates from `templates/` directory (alongside this SKILL.md) for new artifacts.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Skip Layer 3 (HOW), bolt on security/i18n later | Plan all 12 HOW areas before BUILD |
| No requirement IDs — can't trace spec to test | Add IDs during SPECIFY, reference everywhere |
| Over-specify early phases | Each phase refines previous — start broad, narrow down |
| Treat phases as waterfall | Iterate within phases. Phase gates are checkpoints, not walls |
