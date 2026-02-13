---
name: init
description: Scaffold docs structure for a project using the 18-area product incubation checklist. Creates missing directories and starter spec files without overwriting existing docs.
---

# Product Incubation: Init

You are scaffolding a project's documentation structure using the product incubation framework.

## Steps

### 1. Read the SKILL.md

Read the `product-incubation` skill to load the 18-area checklist, artifact structure, and traceability conventions.

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

### 3. Ask which phase

Use AskUserQuestion to ask:

> **Which phase is this project in?**
>
> - **DISCOVER** — Starting fresh, need to define problem/vision/users (Areas 1-3)
> - **SPECIFY** — Problem is clear, need to spec features/data/API/UX (Areas 4-7)
> - **ARCHITECT** — Specs exist, need tech architecture/security/perf/i18n (Areas 8-12)
> - **BUILD+** — Architecture is set, need build/validate/ship scaffolding (Areas 13-18)

### 4. Create directories

Create any missing directories:

```
docs/specs/
docs/plans/
docs/decisions/
```

### 5. Generate starter files

Based on the selected phase, create starter files for the relevant areas. Use the templates from the skill's `templates/` directory as a base.

**Phase priority — create files for the selected phase + all earlier phases:**

| Phase | Areas to scaffold |
|-------|-------------------|
| DISCOVER | 1-3 (VIS, USR, KPI) |
| SPECIFY | 1-7 (add FUNC, DATA, API, UX) |
| ARCHITECT | 1-12 (add ARCH, STACK, SEC, PERF, I18N) |
| BUILD+ | 1-18 (add TEST, OBS, DEPLOY, OPS, ROAD, DEC) |

**File mapping:**

| Area | File | Content |
|------|------|---------|
| VIS | `docs/specs/VIS-problem.md` | Vision & problem statement |
| USR | `docs/specs/USR-scenarios.md` | User scenarios |
| KPI | `docs/specs/KPI-metrics.md` | Success metrics |
| FUNC | `docs/specs/FUNC-features.md` | Functional spec (use spec template) |
| DATA | `docs/specs/DATA-model.md` | Data model |
| API | `docs/specs/API-contract.md` | API contract |
| UX | `docs/specs/UX-spec.md` | UX/UI spec |
| ARCH | `docs/architecture.md` | System architecture |
| STACK | `docs/decisions/YYYY-MM-DD-tech-stack.md` | Tech stack decision (use decision template) |
| SEC | `docs/security.md` | Security model |
| PERF | `docs/specs/PERF-targets.md` | Performance targets |
| I18N | `docs/specs/I18N-plan.md` | i18n plan |
| TEST | `docs/specs/TEST-strategy.md` | Test strategy |
| OBS | `docs/observability.md` | Observability plan |
| DEPLOY | `docs/specs/DEPLOY-plan.md` | Deployment plan |
| OPS | `docs/specs/OPS-runbooks.md` | Ops & maintenance |
| ROAD | `docs/PROJECT-STATUS.md` | Roadmap & milestones |
| DEC | (directory only) | Decision log directory |

**IMPORTANT:** Never overwrite existing files. Only create files that don't exist yet.

Each starter file should have:
- Frontmatter with area prefix, title, date, status: draft
- Section headers matching the area's "Done when" criteria from SKILL.md
- TODO placeholders for content that needs to be filled in
- At least one requirement ID placeholder (e.g., `### VIS-001: [Define problem statement]`)

### 6. Summary

Output a summary:

```
Product Incubation — Init Complete
═══════════════════════════════════

Phase: {SELECTED_PHASE}

Created:
  ✓ docs/specs/VIS-problem.md
  ✓ docs/specs/USR-scenarios.md
  ✓ docs/specs/KPI-metrics.md
  ...

Already existed (untouched):
  · docs/architecture.md
  · docs/security.md
  ...

Next steps:
  1. Fill in VIS-problem.md — start with the problem statement
  2. Use `superpowers:brainstorming` to explore each area before writing specs
  3. Run `/product-incubation:audit` to check completeness as you go
```
