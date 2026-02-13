# product-incubation

A Claude Code plugin for systematic product and feature incubation — ensures cross-cutting concerns (security, observability, i18n, auth) are planned upfront, not bolted on later.

## What it does

Tracks 17 product areas across 5 groups, with automatic traceability from specs to code to tests. Supports both **product mode** (full 17 areas) and **feature mode** (focused subset of ~10 areas).

```
Product Health Audit — My Project
══════════════════════════════════

WHY
  Problem & Vision [product] ·········· 100%
    ✓ done   VIS-001 Problem statement     (code ✓ test ✓)
    ✓ done   VIS-002 Target user           (code ✓ test ✓)

HOW
  Security [core] ····················· 75%
    ✓ done   SEC-001 JWT auth              (code ✓ test ✓)
    ✓ done   SEC-002 User scoping          (code ✓ test ✓)
    ◐ build  SEC-003 Input validation      (code ✓ test ✗)
    ○ todo   SEC-004 Threat model

  Performance [core] ·················· 0%
    (no requirements defined)

Gaps: Performance, Maintenance & Ops
Traceability: 24 defined, 18 in code, 14 in tests (58%)
```

## Install

```bash
claude plugin add ~/miles/claude_plugins/product-incubation    # local
claude plugin add <github-url>                                  # from GitHub
```

## Commands

| Command | Description |
|---------|-------------|
| `/product-incubation:init` | Scaffold docs structure — asks product or feature mode |
| `/product-incubation:audit` | Scan project and report area completeness + gaps |
| `/product-incubation:sync` | Push traceability status to Linear issues |

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

### `/product-incubation:audit`

Scans your project using Glob/Grep/Read (no external dependencies):
- Finds requirement IDs (`FUNC-001`, `SEC-003`) in spec files
- Finds `# REQ: FUNC-001` references in source code
- Finds `test_FUNC_001_*` patterns in test files
- Derives status automatically: spec only = todo, spec+code = build, spec+code+tests = done
- Shows scope tags so you know which areas apply to your mode

### `/product-incubation:sync`

Syncs traceability status to Linear. Requires Linear MCP server.
- Creates issues for new requirements
- Transitions issues when status changes (todo -> build -> done)
- Flags removed requirements for review (never auto-closes)

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

## Traceability System

Requirements are tracked by convention — no separate status file to maintain:

```
Spec file:  ### FUNC-001: CAD file upload
Code:       # REQ: FUNC-001
Test:       test_FUNC_001_cad_upload_extracts_blocks
```

The audit command scans all three locations and derives status automatically.

## Lifecycle Phases

```
DISCOVER (1-3) -> SPECIFY (4-7) -> ARCHITECT (8-12) -> BUILD -> VALIDATE (13-14) -> SHIP (15-17)
```

## Typical Workflow

```
1. /product-incubation:init        Scaffold baseline docs (product or feature mode)
         ↓
2. Brainstorm each area            Fill in specs with research + decisions
         ↓
3. Plan mode                       Create implementation plan from ROAD milestones
         ↓
4. Build                           Implement with REQ traceability tags
         ↓
5. /product-incubation:audit       Check coverage, close ◐ build gaps (missing tests)
         ↓
6. Pick next milestone             Repeat from step 3
```

Run audit **before** picking the next milestone — this surfaces code without tests (`◐ build` state) so you close gaps before accumulating test debt.

## Prerequisites

- **Claude Code** with plugin support
- **Linear MCP server** (for `/product-incubation:sync` only)

## License

MIT
