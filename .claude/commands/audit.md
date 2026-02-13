---
name: audit
description: Scan project artifacts and report area-level completeness with per-requirement kanban. Shows traceability from specs to code to tests.
---

# Product Incubation: Audit

You are scanning this project's artifacts to produce a product health audit report. No external tools needed — use Glob, Grep, and Read to scan files directly.

## Steps

### 1. Load area definitions

Read the `product-incubation` skill for the 18-area checklist and prefix definitions.

The prefixes are: `VIS`, `USR`, `KPI`, `FUNC`, `DATA`, `API`, `UX`, `ARCH`, `STACK`, `SEC`, `PERF`, `I18N`, `TEST`, `OBS`, `DEPLOY`, `OPS`, `ROAD`, `DEC`.

### 2. Scan spec files for requirement IDs

Search `docs/` for requirement ID definitions. IDs follow the pattern `{PREFIX}-{NNN}` (e.g., `FUNC-001`, `SEC-003`).

Look for these patterns:
- `### {PREFIX}-{NNN}:` (heading-style definition in spec files)
- `{PREFIX}-{NNN}` in frontmatter `decision:` fields

Use Grep with pattern: `[A-Z]{2,6}-\d{3}` across `docs/**/*.md`

Collect all unique requirement IDs and which spec file defines them.

### 3. Scan source code for REQ references

Search source code (excluding `docs/`, `node_modules/`, `.git/`, `tests/`, `e2e/`) for traceability comments:

- Pattern: `REQ:\s*{PREFIX}-{NNN}` (Python/JS/TS comment references)
- Pattern: `{PREFIX}-{NNN}` in code comments

Use Grep with pattern: `REQ:\s*[A-Z]{2,6}-\d{3}` across source files.

Collect which requirement IDs appear in code.

### 4. Scan test files for test references

Search test files (`tests/`, `**/*.test.*`, `**/*.spec.*`, `e2e/`) for:

- Pattern: `test_{PREFIX}_{NNN}_` (Python test naming)
- Pattern: `{PREFIX}-{NNN}` or `{PREFIX}_{NNN}` in test descriptions/names

Use Grep with pattern: `[A-Z]{2,6}[-_]\d{3}` across test files.

Collect which requirement IDs appear in tests.

### 5. Determine status for each requirement

For each requirement ID found in step 2:

| Condition | Status | Symbol |
|-----------|--------|--------|
| In spec only | `todo` | `○` |
| In spec + code (but not tests) | `build` | `◐` |
| In spec + code + tests | `done` | `✓` |

### 6. Calculate area percentages

For each of the 18 areas:
- Count requirements with status `done` vs total requirements
- Percentage = `done / total * 100` (round to nearest integer)
- If no requirements defined for an area, show `(no requirements defined)`

### 7. Output the report

Format the report EXACTLY like this (output directly to terminal, do NOT write to a file):

```
Product Health Audit — {Project Name}
══════════════════════════════════════

Layer 1: WHY
  Problem & Vision ·················· {pct}%
    ✓ done   VIS-001 {short title}          (code ✓ test ✓)
    ◐ build  VIS-002 {short title}          (code ✓ test ✗)
    ○ todo   VIS-003 {short title}

  User Scenarios ···················· {pct}%
    ...

  Success Metrics ··················· {pct}%
    ...

Layer 2: WHAT
  Functional Spec ··················· {pct}%
    ...

  Data Model ························ {pct}%
    ...

  API Contract ····················· {pct}%
    ...

  UX/UI Spec ······················· {pct}%
    ...

Layer 3: HOW
  Tech Architecture ················· {pct}%
    ...

  Tech Stack ························ {pct}%
    ...

  Security ·························· {pct}%
    ...

  Performance ······················· {pct}%
    ...

  i18n / L10n ······················· {pct}%
    ...

Layer 4: QUALITY
  Test Strategy ····················· {pct}%
    ...

  Observability ····················· {pct}%
    ...

  Deployment ························ {pct}%
    ...

  Maintenance & Ops ················· {pct}%
    ...

Layer 5: TRACKING
  Roadmap & Milestones ·············· {pct}%
    ...

  Decision Log ····················· {pct}%
    ...

═══════════════════════════════════════
Summary
  Defined: {n} requirements across {m}/18 areas
  In code: {c} ({c/n}%)
  In tests: {t} ({t/n}%)
  Full traceability (spec+code+test): {d} ({d/n}%)

Gaps: {list areas with 0 requirements}
Next: {1-2 suggested actions based on gaps}
```

### 8. Rules

- **Do NOT create or modify any files.** This command is read-only.
- **Do NOT write the report to a file** unless the user explicitly asks for it.
- The short title for each requirement comes from the heading text after the ID (e.g., `### FUNC-001: CAD file upload` → title is "CAD file upload").
- If a requirement ID appears in code but NOT in specs, flag it as an "orphan reference" at the bottom of the report.
- If the `docs/` directory doesn't exist, report "No docs directory found. Run `/product-incubation:init` to scaffold."
- Use the project name from the nearest CLAUDE.md, package.json, or directory name.
