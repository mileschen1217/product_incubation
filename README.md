# product-incubation

A Claude Code plugin for systematic product incubation — ensures cross-cutting concerns (security, observability, i18n, auth) are planned upfront, not bolted on later.

## What it does

Tracks 18 product areas across 5 layers, with automatic traceability from specs to code to tests:

```
Product Health Audit — My Project
══════════════════════════════════

Layer 1: WHY
  Problem & Vision ·················· 100%
    ✓ done   VIS-001 Problem statement     (code ✓ test ✓)
    ✓ done   VIS-002 Target user           (code ✓ test ✓)

Layer 3: HOW
  Security ·························· 75%
    ✓ done   SEC-001 JWT auth              (code ✓ test ✓)
    ✓ done   SEC-002 User scoping          (code ✓ test ✓)
    ◐ build  SEC-003 Input validation      (code ✓ test ✗)
    ○ todo   SEC-004 Threat model

  Performance ······················· 0%
    (no requirements defined)

Gaps: Performance, Maintenance & Ops
Traceability: 24 defined, 18 in code, 14 in tests (58%)
```

## Install

```bash
claude plugins add ~/miles/claude_plugins/product-incubation   # local
claude plugins add <github-url>                                 # from GitHub
```

## Commands

| Command | Description |
|---------|-------------|
| `/product-incubation:init` | Scaffold docs structure from the 18-area checklist |
| `/product-incubation:audit` | Scan project and report area completeness + gaps |
| `/product-incubation:sync` | Push traceability status to Linear issues |

### `/product-incubation:init`

Scaffolds your project's documentation structure. Asks which phase you're in and creates starter files for all relevant areas without overwriting existing docs.

### `/product-incubation:audit`

Scans your project using Glob/Grep/Read (no external dependencies):
- Finds requirement IDs (`FUNC-001`, `SEC-003`) in spec files
- Finds `# REQ: FUNC-001` references in source code
- Finds `test_FUNC_001_*` patterns in test files
- Derives status automatically: spec only = todo, spec+code = build, spec+code+tests = done

### `/product-incubation:sync`

Syncs traceability status to Linear. Requires Linear MCP server.
- Creates issues for new requirements
- Transitions issues when status changes (todo → build → done)
- Flags removed requirements for review (never auto-closes)

## The 18 Areas

| Layer | # | Area | Prefix |
|-------|---|------|--------|
| **WHY** | 1 | Problem & Vision | `VIS` |
| | 2 | User Scenarios | `USR` |
| | 3 | Success Metrics | `KPI` |
| **WHAT** | 4 | Functional Spec | `FUNC` |
| | 5 | Data Model | `DATA` |
| | 6 | API Contract | `API` |
| | 7 | UX/UI Spec | `UX` |
| **HOW** | 8 | Tech Architecture | `ARCH` |
| | 9 | Tech Stack | `STACK` |
| | 10 | Security | `SEC` |
| | 11 | Performance | `PERF` |
| | 12 | i18n / L10n | `I18N` |
| **QUALITY** | 13 | Test Strategy | `TEST` |
| | 14 | Observability | `OBS` |
| | 15 | Deployment | `DEPLOY` |
| | 16 | Maintenance & Ops | `OPS` |
| **TRACKING** | 17 | Roadmap & Milestones | `ROAD` |
| | 18 | Decision Log | `DEC` |

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
DISCOVER (1-3) → SPECIFY (4-7) → ARCHITECT (8-12) → BUILD → VALIDATE (13-14) → SHIP (15-18)
```

## Prerequisites

- **Claude Code** with plugin support
- **Linear MCP server** (for `/product-incubation:sync` only)

## License

MIT
