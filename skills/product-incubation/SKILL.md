---
name: product-incubation
description: Use when starting a new product, feature, or significant initiative. Also use when reviewing project completeness, planning cross-cutting concerns, or hearing "we should have thought of this earlier." Covers full lifecycle from vision through deployment with status tracking via spec markers.
---

# Product Incubation

## Overview

A systematic SOP for incubating products and features from idea to production. Prevents the common failure mode of building features first, then reactively adding security, observability, i18n, and ops — which causes rework and gaps.

**Core principle:** Every relevant area gets planned before build starts. Status lives in spec files as markers on requirement headings — no separate tracking to maintain.

## When to Use

- Starting a new product or major feature from scratch
- Reviewing an existing product for completeness gaps
- Planning a new phase/milestone that adds significant scope
- When you hear "we should have thought of this earlier"

**Not for:** Bug fixes, minor features, refactors within existing architecture.

## Scope Modes

### Product Mode (full 17 areas)

For new products, platforms, or standalone services. All areas apply.

### Feature Mode (focused subset)

For significant features within an existing product. Default 10 areas:

`USR`, `FUNC`, `DATA`, `API`, `UX`, `ARCH`, `SEC`, `PERF`, `TEST`, `DEC`

Plus conditionally:
- `DEPLOY` — if the feature requires migration, new infra, or config changes
- `I18N` — if the feature has user-facing UI in a multi-locale app

Use `/product-incubation:init` to interactively select the mode.

## Scope Tags

Each area has scope tags that determine relevance:

| Tag | Meaning |
|-----|---------|
| `core` | Always relevant (product or feature mode) |
| `product` | Product-level only — skip for feature work |
| `domain:frontend` | Relevant when work touches frontend |
| `domain:backend` | Relevant when work touches backend |
| `domain:infra` | Relevant when work touches deployment/infra |

## Quick Reference: 17 Product Areas

### WHY (Vision & Context)

| # | Area | Prefix | Scope | Done when |
|---|------|--------|-------|-----------|
| 1 | Problem & Vision | `VIS` | `product` | Problem statement, target user, market context |
| 2 | User Scenarios | `USR` | `core` | End-to-end journeys with acceptance criteria |
| 3 | Success Metrics | `KPI` | `product` | Measurable KPIs (adoption, performance, business) |

### WHAT (Specification)

| # | Area | Prefix | Scope | Done when |
|---|------|--------|-------|-----------|
| 4 | Functional Spec | `FUNC` | `core` | Features with input/process/output, scope boundaries |
| 5 | Data Model | `DATA` | `core` `domain:backend` | Entities, relationships, constraints, DB schema |
| 6 | API Contract | `API` | `core` `domain:backend` | Endpoints, request/response shapes, error codes |
| 7 | UX/UI Spec | `UX` | `core` `domain:frontend` | Design direction, breakpoints, a11y, interaction states |

### HOW (Architecture & Decisions)

| # | Area | Prefix | Scope | Done when |
|---|------|--------|-------|-----------|
| 8 | Tech Architecture | `ARCH` | `core` | System diagram, component boundaries, data flow |
| 9 | Security | `SEC` | `core` | Auth model, input validation, OWASP, threat model |
| 10 | Performance | `PERF` | `core` | Load targets, latency budgets, scaling strategy |
| 11 | i18n / L10n | `I18N` | `domain:frontend` | Supported locales, string externalization, formatting |
| 12 | Decision Log | `DEC` | `core` | Key tech decisions with rationale (MADR format) |

### QUALITY (Validation & Operations)

| # | Area | Prefix | Scope | Done when |
|---|------|--------|-------|-----------|
| 13 | Test Strategy | `TEST` | `core` | Unit/integration/E2E coverage plan, CI pipeline |
| 14 | Observability | `OBS` | `product` | Logging, metrics, error tracking, alerting, health |
| 15 | Deployment | `DEPLOY` | `core` `domain:infra` | Environment setup, CI/CD, rollback plan |
| 16 | Maintenance & Ops | `OPS` | `product` | Backup, monitoring, on-call, runbooks |

### TRACKING

| # | Area | Prefix | Scope | Done when |
|---|------|--------|-------|-----------|
| 17 | Roadmap & Milestones | `ROAD` | `product` | Phased delivery plan with priorities |

> **Note:** Tech stack choices are decision records — use `DEC` prefix, not a separate area. Write a `docs/decisions/YYYY-MM-DD-tech-stack.md` using the decision template.

## Lifecycle Phases

```
DISCOVER (Areas 1-3) -> SPECIFY (Areas 4-7) -> ARCHITECT (Areas 8-12) -> BUILD -> VALIDATE (Areas 13-14) -> SHIP (Areas 15-17)
```

### Phase 1: DISCOVER

**Entry:** Idea or problem statement exists.
**Do:** Document problem, target users, market context, user scenarios, success metrics.
**Exit:** Areas 1-3 documented. Stakeholder alignment on problem and scope.
**Artifacts:** `docs/specs/VIS-*.md`, `docs/specs/USR-*.md`, `docs/specs/KPI-*.md`

### Phase 2: SPECIFY

**Entry:** Phase 1 complete.
**Do:** Write functional spec, data model, API contract, UX spec. Define scope boundaries (what's NOT included).
**Exit:** Areas 4-7 documented. All features have requirement IDs.
**Artifacts:** `docs/specs/FUNC-*.md`, `docs/specs/DATA-model.md`, `docs/specs/API-contract.md`, `docs/specs/UX-spec.md`

### Phase 3: ARCHITECT

**Entry:** Phase 2 complete.
**Do:** Design system architecture, plan security/perf/i18n. Write decision records for non-obvious choices (including tech stack).
**Exit:** Areas 8-12 documented. Decision records for all major tech choices.
**Artifacts:** `docs/architecture.md`, `docs/security.md`, `docs/decisions/YYYY-MM-DD-*.md`

### Phase 4: BUILD

**Entry:** Phase 3 complete.
**Do:** Implement features. Update requirement headings with `◐` while in progress, `✓` when done. Use `/product-incubation:refresh` to bulk-update markers based on project state.
**Exit:** All FUNC requirements implemented and marked `✓` in spec files.

### Phase 5: VALIDATE

**Entry:** Core implementation complete.
**Do:** Execute test strategy, set up observability. Verify coverage against spec.
**Exit:** Areas 13-14 complete. All requirements have test coverage.
**Artifacts:** Test reports, observability config.

### Phase 6: SHIP

**Entry:** Phase 5 complete.
**Do:** Set up deployment pipeline, write runbooks, document ops procedures.
**Exit:** Areas 15-17 complete. Product is production-ready.
**Artifacts:** `docs/specs/DEPLOY-plan.md`, `docs/specs/OPS-runbooks.md`, decision log up to date.

## Status Tracking

Every requirement gets a convention-based ID in spec file headings:

**Format:** `{PREFIX}-{NNN}` (e.g., `FUNC-001`, `SEC-003`, `PERF-002`)

**Status markers** are appended to requirement headings in spec files:

```markdown
### FUNC-001: CAD file upload ✓
### FUNC-002: File validation ◐
### FUNC-003: Batch processing
```

| Marker | Meaning |
|--------|---------|
| `✓` | Done — implemented and verified |
| `◐` | In progress — actively being worked on |
| No marker | Not started |

Status is updated in spec files by the user or Claude during normal work — no separate tagging step.

Use `/product-incubation:status` to read spec files and report a status dashboard.

## Artifact Structure

```
docs/
  specs/                          # All spec files across layers
    VIS-problem.md                # Vision & problem statement
    USR-scenarios.md              # User scenarios
    KPI-metrics.md                # Success metrics
    FUNC-*.md                     # Functional specs
    DATA-model.md                 # Data model spec
    API-contract.md               # API spec
    UX-spec.md                    # UX/UI spec
    PERF-targets.md               # Performance targets
    I18N-plan.md                  # i18n plan
    TEST-strategy.md              # Test strategy
    DEPLOY-plan.md                # Deployment plan
    OPS-runbooks.md               # Ops & maintenance
  plans/                          # Design docs (existing pattern)
    YYYY-MM-DD-<topic>-design.md
  decisions/                      # MADR-style decision records
    YYYY-MM-DD-<decision>.md
  architecture.md                 # System architecture
  security.md                     # Security model
  observability.md                # Observability plan
  development.md                  # Dev setup guide
  PROJECT-STATUS.md               # Roadmap & status
product-status.yaml               # Auto-generated by sync
```

Use templates from `templates/` directory (alongside this SKILL.md) for new artifacts.

## Commands

| Command | Description |
|---------|-------------|
| `/product-incubation:init` | Scaffold docs structure — asks product or feature mode |
| `/product-incubation:status` | Read spec files and report area completeness + gaps |
| `/product-incubation:refresh` | Add missing requirement IDs and update status markers |
| `/product-incubation:sync` | Push requirement status to Linear issues |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Skip HOW layer, bolt on security/i18n later | Plan all HOW areas before BUILD |
| No requirement IDs in spec headings | Add `### PREFIX-NNN: Title` headings during SPECIFY |
| Over-specify early phases | Each phase refines previous — start broad, narrow down |
| Treat phases as waterfall | Iterate within phases. Phase gates are checkpoints, not walls |
| Marking `✓` without verifying | Run tests and review code before adding the `✓` marker |
| Separate "tech stack" area | Tech choices are decisions — use DEC prefix and decision template |
