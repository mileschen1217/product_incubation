# product-incubation

A Claude Code plugin for systematic product and feature incubation — ensures cross-cutting concerns (security, observability, i18n, auth) are planned upfront, not bolted on later. Uses [OpenSpec](https://github.com/fission-ai/openspec) for spec lifecycle management.

## What it does

Tracks 17 product areas across 5 groups using OpenSpec's file lifecycle for status. Supports both **product mode** (full 17 areas) and **feature mode** (focused subset of ~10 areas).

```
Product Status — My Project
══════════════════════════════════

WHY
  ✓ Problem & Vision ················ 3/3 sections
  ◐ User Scenarios ·················· 3 scenarios (in progress)

HOW
  ◐ Security ························ 2 scenarios (in progress)
  ○ Performance ····················· (not started)

Gaps: Performance, Maintenance & Ops
Done: 5/12  ·  In progress: 3/12
```

## Prerequisites

- **Claude Code** with plugin support
- **OpenSpec CLI** — `npm install -g @fission-ai/openspec`

## Install

```bash
# Step 1: Add as a marketplace source
/plugin marketplace add mileschen1217/product_incubation

# Step 2: Install the plugin
/plugin install product-incubation@mileschen1217-product_incubation
```

## Commands

| Command | Description |
|---------|-------------|
| `/product-incubation:init` | Initialize OpenSpec with product-incubation schema and manifest |
| `/product-incubation:status` | Read manifest and OpenSpec state, report area completeness dashboard |
| `/product-incubation:import` | Import existing docs into OpenSpec specs |
| `/product-incubation:review` | Rubric-driven spec quality review |
| `/product-incubation:set-phase` | Update the current lifecycle phase in the manifest |

### `/product-incubation:init`

Sets up the product incubation framework for your project. Asks two questions:

1. **Scope mode** — Product (full 17 areas) or Feature (focused subset)
2. **Phase** — DISCOVER, SPECIFY, ARCHITECT, or BUILD+

Installs the `product-incubation` OpenSpec schema and generates `openspec/incubation.yaml` (the manifest mapping all 17 areas to OpenSpec capabilities).

**Example:**
```
> /product-incubation:init
What are you incubating? → Feature
Which domains? → Frontend, Backend
Which phase? → SPECIFY

Schema: product-incubation (installed to openspec/schemas/)
Manifest: openspec/incubation.yaml (17 areas defined)
Active areas: 10 of 17
```

### `/product-incubation:status`

Reads the incubation manifest and OpenSpec file structure:
- Specs in `openspec/specs/` → done
- Specs in `openspec/changes/*/specs/` → in progress
- Not found → not started

Shows per-area status with section-level completeness (scenario counts for requirement areas, filled sections for document areas) and phase-aware guidance for next steps.

### `/product-incubation:import`

Imports existing project documentation into OpenSpec specs. Scans your docs, maps content to the 17 areas, and generates draft specs for review.

**Example:**
```
> /product-incubation:import
Where are your existing docs? → Auto-scan

Found documents:
  1. docs/architecture.md — System architecture and component design
  2. docs/security.md — Security model and authentication

Proposed mapping:
  docs/architecture.md  →  ARCH (Tech Architecture)
  docs/security.md      →  SEC (Security)

Import Complete
═══════════════════════════════════
Imported: 2 areas from 2 source documents
  Archived (done):     1 — ARCH
  Needs work (draft):  1 — SEC
```

### `/product-incubation:review`

Evaluates spec quality against a 6-principle rubric (Specificity, Testability, Edge Coverage, Completeness, Cross-references, Consistency) and checks cross-area consistency.

**Example:**
```
> /product-incubation:review

Spec Review — My Project
══════════════════════════════════════

FUNC (Functional Spec) — 2 suggestions
  ⚠ Edge Coverage: No error scenarios for form submission
  ℹ Specificity: Consider more concrete WHEN conditions

Cross-area consistency:
  ⚠ API ↔ SEC: 3 endpoints not covered in Security spec
  ✓ FUNC ↔ API: All features have matching endpoints

══════════════════════════════════════
Summary: 3 suggestions across 2 areas
Priority: SEC ↔ API gap, FUNC error cases
```

### `/product-incubation:set-phase`

Updates the lifecycle phase in the incubation manifest. Shows the current phase, presents the full lifecycle (DISCOVER → SPECIFY → ARCHITECT → BUILD → VALIDATE → SHIP), and lets you select the new phase.

## Product vs Feature Mode

| | Product Mode | Feature Mode |
|---|---|---|
| **Use for** | New products, platforms, services | Features within existing products |
| **Areas** | All 17 | ~10 core areas |
| **Skips** | Nothing | VIS, KPI, OBS, OPS, ROAD |
| **Conditionally adds** | N/A | DEPLOY (if infra), I18N (if multi-locale) |

## The 17 Areas

| Group | # | Area | Prefix | Scope | Type |
|-------|---|------|--------|-------|------|
| **WHY** | 1 | Problem & Vision | `VIS` | `product` | document |
| | 2 | User Scenarios | `USR` | `core` | requirement |
| | 3 | Success Metrics | `KPI` | `product` | document |
| **WHAT** | 4 | Functional Spec | `FUNC` | `core` | requirement |
| | 5 | Data Model | `DATA` | `core` `domain:backend` | requirement |
| | 6 | API Contract | `API` | `core` `domain:backend` | requirement |
| | 7 | UX/UI Spec | `UX` | `core` `domain:frontend` | requirement |
| **HOW** | 8 | Tech Architecture | `ARCH` | `core` | requirement |
| | 9 | Security | `SEC` | `core` | requirement |
| | 10 | Performance | `PERF` | `core` | requirement |
| | 11 | i18n / L10n | `I18N` | `domain:frontend` | requirement |
| | 12 | Decision Log | `DEC` | `core` | document |
| **QUALITY** | 13 | Test Strategy | `TEST` | `core` | requirement |
| | 14 | Observability | `OBS` | `product` | requirement |
| | 15 | Deployment | `DEPLOY` | `domain:infra` | requirement |
| | 16 | Maintenance & Ops | `OPS` | `product` | requirement |
| **TRACKING** | 17 | Roadmap & Milestones | `ROAD` | `product` | document |

### Area Types

- **Requirement areas** (13): Use `### Requirement:` + `#### Scenario:` with WHEN/THEN format
- **Document areas** (4: VIS, KPI, DEC, ROAD): Use area-specific section templates (narrative format)

### Scope Tags

| Tag | Meaning |
|-----|---------|
| `core` | Always relevant (product or feature) |
| `product` | Product-level only — skip for feature work |
| `domain:frontend` | When work touches frontend |
| `domain:backend` | When work touches backend |
| `domain:infra` | When work touches deployment/infra |

## Lifecycle Phases

```
DISCOVER (1-3) -> SPECIFY (4-7) -> ARCHITECT (8-12) -> BUILD -> VALIDATE (13-14) -> SHIP (15-17)
```

## Typical Workflow

```
1. /product-incubation:init        Set up OpenSpec + manifest (product or feature mode)
         ↓
2a. openspec new change <name>     Start work on an area from scratch
  — OR —
2b. /product-incubation:import     Import existing docs into OpenSpec specs
         ↓
3. Write proposal + specs          Define requirements (requirement or document format)
         ↓
4. openspec archive <name>         Archive completed specs (marks area as done)
         ↓
5. /product-incubation:status      Check coverage, find gaps
         ↓
6. /product-incubation:review      Review spec quality and cross-area consistency
         ↓
7. Pick next area                  Repeat from step 2
```

Run status **before** picking the next area — this surfaces incomplete areas so you close gaps before moving on. Run review periodically to catch quality issues early.

## License

MIT
