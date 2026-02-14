# product-incubation

A Claude Code plugin for systematic product and feature incubation — ensures cross-cutting concerns (security, observability, i18n, auth) are planned upfront, not bolted on later.

## What it does

Tracks 17 product areas across 5 groups with status markers on spec file headings. Supports both **product mode** (full 17 areas) and **feature mode** (focused subset of ~10 areas).

```
Product Status — My Project
══════════════════════════════════

WHY
  ✓ Problem & Vision ················ 100%  (2/2)
  ◐ User Scenarios ·················· 60%   (3/5)

HOW
  ◐ Security ························ 50%   (2/4)
  ○ Performance ·····················  0%   (0/1)

Gaps: Performance, Maintenance & Ops
Done: 12/24 (50%)  ·  In progress: 5/24
```

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
| `/product-incubation:init` | Scaffold docs structure — asks product or feature mode |
| `/product-incubation:status` | Read spec files and report area completeness + gaps |
| `/product-incubation:refresh` | Add missing requirement IDs and update status markers |

### `/product-incubation:init`

Scaffolds your project's documentation structure. Asks two questions:

1. **Scope mode** — Product (full 17 areas) or Feature (focused subset)
2. **Phase** — DISCOVER, SPECIFY, ARCHITECT, or BUILD+

Creates starter files for all relevant areas without overwriting existing docs.

**Example:**
```
> /product-incubation:init
What are you incubating? → Feature
Which domains? → Frontend, Backend
Which phase? → SPECIFY

Created 7 starter files for areas: USR, FUNC, DATA, API, UX, ARCH, DEC
```

### `/product-incubation:status`

Reads spec files using Glob/Grep/Read (no external dependencies):
- Finds requirement headings (`### FUNC-001: Title ✓`) in spec files
- Reads status from markers: `✓` = done, `◐` = in progress, no marker = not started
- Shows per-area completeness percentages and a compact dashboard

### `/product-incubation:refresh`

Adds missing requirement IDs and updates status markers on existing docs. Useful when adopting the plugin on a project that already has documentation.

Two modes, detected automatically:
- **First run** — Docs exist but lack `### PREFIX-NNN:` headings. Scans doc content to propose IDs and status markers.
- **Update** — IDs already exist. Uses `git` history to find recently changed requirements and proposes marker updates.

All changes are shown as diffs for approval before writing. Never deletes IDs or downgrades markers.

## Product vs Feature Mode

| | Product Mode | Feature Mode |
|---|---|---|
| **Use for** | New products, platforms, services | Features within existing products |
| **Areas** | All 17 | ~10 core areas |
| **Skips** | Nothing | VIS, KPI, OBS, OPS, ROAD |
| **Conditionally adds** | N/A | DEPLOY (if infra), I18N (if multi-locale) |

## The 17 Areas

| Group | # | Area | Prefix | Scope |
|-------|---|------|--------|-------|
| **WHY** | 1 | Problem & Vision | `VIS` | `product` |
| | 2 | User Scenarios | `USR` | `core` |
| | 3 | Success Metrics | `KPI` | `product` |
| **WHAT** | 4 | Functional Spec | `FUNC` | `core` |
| | 5 | Data Model | `DATA` | `core` `domain:backend` |
| | 6 | API Contract | `API` | `core` `domain:backend` |
| | 7 | UX/UI Spec | `UX` | `core` `domain:frontend` |
| **HOW** | 8 | Tech Architecture | `ARCH` | `core` |
| | 9 | Security | `SEC` | `core` |
| | 10 | Performance | `PERF` | `core` |
| | 11 | i18n / L10n | `I18N` | `domain:frontend` |
| | 12 | Decision Log | `DEC` | `core` |
| **QUALITY** | 13 | Test Strategy | `TEST` | `core` |
| | 14 | Observability | `OBS` | `product` |
| | 15 | Deployment | `DEPLOY` | `core` `domain:infra` |
| | 16 | Maintenance & Ops | `OPS` | `product` |
| **TRACKING** | 17 | Roadmap & Milestones | `ROAD` | `product` |

### Scope Tags

| Tag | Meaning |
|-----|---------|
| `core` | Always relevant (product or feature) |
| `product` | Product-level only — skip for feature work |
| `domain:frontend` | When work touches frontend |
| `domain:backend` | When work touches backend |
| `domain:infra` | When work touches deployment/infra |

> **Note:** Tech stack choices are decision records — use `DEC` prefix and the decision template.

## Status Tracking

Status lives in spec files as markers on requirement headings:

```markdown
### FUNC-001: User login ✓        # done
### FUNC-002: Password reset ◐    # in progress
### FUNC-003: OAuth integration    # not started
```

The status command reads these markers and reports a dashboard.

## Lifecycle Phases

```
DISCOVER (1-3) -> SPECIFY (4-7) -> ARCHITECT (8-12) -> BUILD -> VALIDATE (13-14) -> SHIP (15-17)
```

## Typical Workflow

```
1. /product-incubation:init        Scaffold baseline docs (product or feature mode)
         ↓
2. /product-incubation:refresh     Add requirement IDs to existing docs (if adopting)
         ↓
3. Brainstorm each area            Fill in specs with research + decisions
         ↓
4. Plan mode                       Create implementation plan from ROAD milestones
         ↓
5. Build                           Implement, mark requirements ◐ then ✓ in specs
         ↓
6. /product-incubation:refresh     Update markers based on project state
         ↓
7. /product-incubation:status      Check coverage, find gaps
         ↓
8. Pick next milestone             Repeat from step 4
```

Run status **before** picking the next milestone — this surfaces incomplete requirements so you close gaps before moving on.

## Prerequisites

- **Claude Code** with plugin support

## License

MIT
