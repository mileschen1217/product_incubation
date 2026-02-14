# product-incubation

A Claude Code plugin for systematic product and feature incubation — ensures cross-cutting concerns (security, observability, i18n, auth) are planned upfront, not bolted on later. Uses [OpenSpec](https://github.com/fission-ai/openspec) for spec lifecycle management.

## What it does

Tracks 17 product areas across 5 groups using OpenSpec's file lifecycle for status. Supports both **product mode** (full 17 areas) and **feature mode** (focused subset of ~10 areas).

```
Product Status — My Project
══════════════════════════════════

WHY
  ✓ Problem & Vision ················ 100%  (1/1)
  ◐ User Scenarios ··················  0%   (0/1)

HOW
  ◐ Security ························  0%   (0/1)
  ○ Performance ·····················  0%   (0/1)

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

Shows per-area status and a compact dashboard grouped by WHY/WHAT/HOW/QUALITY/TRACKING.

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
2. openspec change new <name>      Start work on an area
         ↓
3. Write proposal + specs          Define requirements (requirement or document format)
         ↓
4. openspec archive <name>         Archive completed specs (marks area as done)
         ↓
5. /product-incubation:status      Check coverage, find gaps
         ↓
6. Pick next area                  Repeat from step 2
```

Run status **before** picking the next area — this surfaces incomplete areas so you close gaps before moving on.

## License

MIT
