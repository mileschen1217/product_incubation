---
name: product-incubation
description: Use when starting a new product, feature, or significant initiative. Also use when reviewing project completeness, planning cross-cutting concerns, or hearing "we should have thought of this earlier." Covers full lifecycle from vision through deployment with OpenSpec-backed status tracking.
---

# Product Incubation

## Overview

A systematic SOP for incubating products and features from idea to production. Prevents the common failure mode of building features first, then reactively adding security, observability, i18n, and ops — which causes rework and gaps.

**Core principle:** Every relevant area gets planned before build starts. Status is tracked through OpenSpec's file lifecycle — specs in `openspec/specs/` are done, specs in `openspec/changes/` are in progress.

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

| # | Area | Prefix | Scope | Type | Done when |
|---|------|--------|-------|------|-----------|
| 1 | Problem & Vision | `VIS` | `product` | document | Problem statement, target user, market context |
| 2 | User Scenarios | `USR` | `core` | requirement | End-to-end journeys with acceptance criteria |
| 3 | Success Metrics | `KPI` | `product` | document | Measurable KPIs (adoption, performance, business) |

### WHAT (Specification)

| # | Area | Prefix | Scope | Type | Done when |
|---|------|--------|-------|------|-----------|
| 4 | Functional Spec | `FUNC` | `core` | requirement | Features with input/process/output, scope boundaries |
| 5 | Data Model | `DATA` | `core` `domain:backend` | requirement | Entities, relationships, constraints, DB schema |
| 6 | API Contract | `API` | `core` `domain:backend` | requirement | Endpoints, request/response shapes, error codes |
| 7 | UX/UI Spec | `UX` | `core` `domain:frontend` | requirement | Design direction, breakpoints, a11y, interaction states |

### HOW (Architecture & Decisions)

| # | Area | Prefix | Scope | Type | Done when |
|---|------|--------|-------|------|-----------|
| 8 | Tech Architecture | `ARCH` | `core` | requirement | System diagram, component boundaries, data flow |
| 9 | Security | `SEC` | `core` | requirement | Auth model, input validation, OWASP, threat model |
| 10 | Performance | `PERF` | `core` | requirement | Load targets, latency budgets, scaling strategy |
| 11 | i18n / L10n | `I18N` | `domain:frontend` | requirement | Supported locales, string externalization, formatting |
| 12 | Decision Log | `DEC` | `core` | document | Key tech decisions with rationale (MADR format) |

### QUALITY (Validation & Operations)

| # | Area | Prefix | Scope | Type | Done when |
|---|------|--------|-------|------|-----------|
| 13 | Test Strategy | `TEST` | `core` | requirement | Unit/integration/E2E coverage plan, CI pipeline |
| 14 | Observability | `OBS` | `product` | requirement | Logging, metrics, error tracking, alerting, health |
| 15 | Deployment | `DEPLOY` | `domain:infra` | requirement | Environment setup, CI/CD, rollback plan |
| 16 | Maintenance & Ops | `OPS` | `product` | requirement | Backup, monitoring, on-call, runbooks |

### TRACKING

| # | Area | Prefix | Scope | Type | Done when |
|---|------|--------|-------|------|-----------|
| 17 | Roadmap & Milestones | `ROAD` | `product` | document | Phased delivery plan with priorities |

> **Note:** Tech stack choices are decision records — use `DEC` prefix. Create an OpenSpec change covering the `decision-log` capability.

## Area Types

Each area uses one of two spec formats:

### Requirement areas (13 areas)

`FUNC`, `DATA`, `API`, `UX`, `SEC`, `PERF`, `USR`, `TEST`, `I18N`, `OBS`, `DEPLOY`, `OPS`, `ARCH`

Use OpenSpec's standard requirement format:
- `### Requirement: <name>` with SHALL/MUST language
- `#### Scenario: <name>` with WHEN/THEN conditions
- Every requirement must have at least one scenario

### Document areas (4 areas)

`VIS`, `KPI`, `DEC`, `ROAD`

Use area-specific section templates instead of WHEN/THEN scenarios:

| Area | Sections |
|------|----------|
| VIS | Problem Statement / Target Users / Market Context |
| KPI | Metric / Target / Measurement Method |
| DEC | Context / Decision / Consequences / Alternatives Considered |
| ROAD | Phase / Milestones / Priority / Timeline |

## OpenSpec Workflow

Product incubation uses OpenSpec as the spec lifecycle engine. Each area maps to an OpenSpec **capability** (kebab-case name in the manifest).

### How it works

1. **`/product-incubation:init`** installs the `product-incubation` schema and generates `openspec/incubation.yaml` (the manifest mapping all 17 areas)
2. **`openspec change new <name>`** starts work on an area — write a proposal, then specs
3. **`openspec archive <name>`** moves completed specs from `changes/` to `specs/` (done)
4. **`/product-incubation:status`** reads the manifest and OpenSpec file structure to report area coverage

### Status tracking

Status is determined by file location — no manual markers needed:

| Location | Status |
|----------|--------|
| `openspec/specs/<capability>/spec.md` | Done (archived) |
| `openspec/changes/<name>/specs/<capability>/` | In progress |
| Not found anywhere | Not started |

### Manifest (incubation.yaml)

The manifest at `openspec/incubation.yaml` defines all 17 areas with their prefix, capability name, scope, group, and type. It's generated by `/product-incubation:init` and read by `/product-incubation:status`.

Each area entry:
```yaml
- prefix: FUNC
  capability: functional-spec
  scope: core
  group: WHAT
  type: requirement
```

## Lifecycle Phases

```
DISCOVER (Areas 1-3) -> SPECIFY (Areas 4-7) -> ARCHITECT (Areas 8-12) -> BUILD -> VALIDATE (Areas 13-14) -> SHIP (Areas 15-17)
```

### Phase 1: DISCOVER

**Entry:** Idea or problem statement exists.
**Do:** Document problem, target users, market context, user scenarios, success metrics.
**Exit:** Areas 1-3 have archived specs in `openspec/specs/`.

### Phase 2: SPECIFY

**Entry:** Phase 1 complete.
**Do:** Write functional spec, data model, API contract, UX spec. Define scope boundaries (what's NOT included).
**Exit:** Areas 4-7 have archived specs.

### Phase 3: ARCHITECT

**Entry:** Phase 2 complete.
**Do:** Design system architecture, plan security/perf/i18n. Write decision records for non-obvious choices (including tech stack).
**Exit:** Areas 8-12 have archived specs.

### Phase 4: BUILD

**Entry:** Phase 3 complete.
**Do:** Implement features. Use OpenSpec changes to track work — create changes, complete tasks, archive when done.
**Exit:** All FUNC requirements implemented and specs archived.

### Phase 5: VALIDATE

**Entry:** Core implementation complete.
**Do:** Execute test strategy, set up observability. Verify coverage against spec.
**Exit:** Areas 13-14 have archived specs.

### Phase 6: SHIP

**Entry:** Phase 5 complete.
**Do:** Set up deployment pipeline, write runbooks, document ops procedures.
**Exit:** Areas 15-17 have archived specs. Product is production-ready.

## Commands

| Command | Description |
|---------|-------------|
| `/product-incubation:init` | Initialize OpenSpec with product-incubation schema and manifest |
| `/product-incubation:status` | Read manifest and OpenSpec state, report area completeness dashboard |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Skip HOW layer, bolt on security/i18n later | Plan all HOW areas before BUILD |
| Over-specify early phases | Each phase refines previous — start broad, narrow down |
| Treat phases as waterfall | Iterate within phases. Phase gates are checkpoints, not walls |
| Marking specs done without verifying | Run tests and review code before archiving a change |
| Separate "tech stack" area | Tech choices are decisions — use DEC prefix |
| Creating specs outside OpenSpec | Always use `openspec change new` — the file lifecycle drives status |
