---
description: Scaffold docs structure for a project using the 17-area product incubation checklist. Asks product or feature mode, then creates missing directories and starter spec files without overwriting existing docs.
allowed-tools: [Read, Write, Glob, Grep, AskUserQuestion]
disable-model-invocation: true
---

# Product Incubation: Init

You are scaffolding a project's documentation structure using the product incubation framework.

## Steps

### 1. Read the SKILL.md

Read the `product-incubation` skill to load the 17-area checklist, scope tags, artifact structure, and status conventions.

### 2. Scan existing docs

Use Glob to check what already exists:

```
docs/specs/**/*
docs/plans/**/*
docs/decisions/**/*
docs/architecture.md
docs/security.md
docs/observability.md
docs/development.md
docs/PROJECT-STATUS.md
```

Report what already exists so the user knows what will NOT be overwritten.

### 3. Ask scope mode

Use AskUserQuestion to ask:

> **What are you incubating?**
>
> - **Product** — New product/platform (full 17 areas)
> - **Feature** — Significant feature within an existing product (focused subset)

**If Feature mode:**

Ask a follow-up question:

> **Which domains does this feature touch?** (select all that apply)
>
> - **Frontend** — UI components, pages, user interaction
> - **Backend** — API, database, business logic
> - **Infra** — Deployment, CI/CD, config changes

Then determine the area set:
- **Base (always included):** `USR`, `FUNC`, `DATA`, `API`, `UX`, `ARCH`, `SEC`, `PERF`, `TEST`, `DEC`
- **Add `I18N`** if frontend is selected and the project has multi-locale support
- **Add `DEPLOY`** if infra is selected

### 4. Ask which phase

Use AskUserQuestion to ask:

> **Which phase is this project/feature in?**
>
> - **DISCOVER** — Starting fresh, need to define problem/vision/users (Areas 1-3)
> - **SPECIFY** — Problem is clear, need to spec features/data/API/UX (Areas 4-7)
> - **ARCHITECT** — Specs exist, need tech architecture/security/perf/i18n (Areas 8-12)
> - **BUILD+** — Architecture is set, need build/validate/ship scaffolding (Areas 13-17)

### 5. Create directories

Create any missing directories:

```
docs/specs/
docs/plans/
docs/decisions/
```

### 6. Generate starter files

Based on the selected phase and scope mode, create starter files for the relevant areas. Use the templates from the skill's `templates/` directory as a base.

**Skip any area not in the active area set** (feature mode filters out `product`-scoped areas and unselected domain areas).

**Phase priority — create files for the selected phase + all earlier phases:**

| Phase | Areas to scaffold |
|-------|-------------------|
| DISCOVER | 1-3 (VIS, USR, KPI) |
| SPECIFY | 1-7 (add FUNC, DATA, API, UX) |
| ARCHITECT | 1-12 (add ARCH, SEC, PERF, I18N, DEC) |
| BUILD+ | 1-17 (add TEST, OBS, DEPLOY, OPS, ROAD) |

**File mapping:**

| Area | Prefix | File | Content |
|------|--------|------|---------|
| Problem & Vision | VIS | `docs/specs/VIS-problem.md` | Vision & problem statement |
| User Scenarios | USR | `docs/specs/USR-scenarios.md` | User scenarios |
| Success Metrics | KPI | `docs/specs/KPI-metrics.md` | Success metrics |
| Functional Spec | FUNC | `docs/specs/FUNC-features.md` | Functional spec (use spec template) |
| Data Model | DATA | `docs/specs/DATA-model.md` | Data model |
| API Contract | API | `docs/specs/API-contract.md` | API contract |
| UX/UI Spec | UX | `docs/specs/UX-spec.md` | UX/UI spec |
| Tech Architecture | ARCH | `docs/architecture.md` | System architecture |
| Security | SEC | `docs/security.md` | Security model |
| Performance | PERF | `docs/specs/PERF-targets.md` | Performance targets |
| i18n / L10n | I18N | `docs/specs/I18N-plan.md` | i18n plan |
| Decision Log | DEC | `docs/decisions/` | Decision log directory (use decision template) |
| Test Strategy | TEST | `docs/specs/TEST-strategy.md` | Test strategy |
| Observability | OBS | `docs/observability.md` | Observability plan |
| Deployment | DEPLOY | `docs/specs/DEPLOY-plan.md` | Deployment plan |
| Maintenance & Ops | OPS | `docs/specs/OPS-runbooks.md` | Ops & maintenance |
| Roadmap | ROAD | `docs/PROJECT-STATUS.md` | Roadmap & milestones |

**IMPORTANT:** Never overwrite existing files. Only create files that don't exist yet.

Each starter file should have:
- Frontmatter with area prefix, title, date, status: draft
- Section headers matching the area's "Done when" criteria from SKILL.md
- TODO placeholders for content that needs to be filled in
- At least one requirement ID placeholder (e.g., `### VIS-001: [Define problem statement]`)

### 7. Summary

Output a summary:

```
Product Incubation — Init Complete
═══════════════════════════════════

Mode: {Product | Feature (frontend, backend)}
Phase: {SELECTED_PHASE}
Areas: {N} of 17

Created:
  ✓ docs/specs/VIS-problem.md
  ✓ docs/specs/USR-scenarios.md
  ✓ docs/specs/KPI-metrics.md
  ...

Already existed (untouched):
  · docs/architecture.md
  · docs/security.md
  ...

Skipped (out of scope for this mode):
  - OBS (product-only)
  - OPS (product-only)
  ...

Next steps:
  1. Fill in the first spec file — start with the earliest area
  2. Run `/product-incubation:status` to check completeness as you go
  3. Run `/product-incubation:refresh` to add requirement IDs to existing docs
```
