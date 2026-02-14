---
description: Initialize product incubation with OpenSpec — installs schema, configures scope/phase, generates manifest. Requires openspec CLI.
allowed-tools: [Read, Write, Glob, Grep, Bash, AskUserQuestion]
disable-model-invocation: true
---

# Product Incubation: Init

You are setting up the product incubation framework for a project using OpenSpec as the spec lifecycle engine.

## Steps

### 1. Read the SKILL.md

Read the `product-incubation` skill to load the 17-area checklist, scope tags, area types, and lifecycle phases.

### 2. Check prerequisites

**Check OpenSpec CLI:**

Run `which openspec` via Bash. If not found, stop and tell the user:

> OpenSpec CLI is required. Install it with `npm install -g @fission-ai/openspec`, then re-run this command.

**Check OpenSpec project:**

Use Glob to check what exists: `openspec/`, `openspec/schemas/product-incubation/`, `openspec/incubation.yaml`.

- If `openspec/` does NOT exist → run: `openspec init --tools claude`
- If `openspec/` exists but `openspec/schemas/product-incubation/` does NOT → schema install needed (step 5)
- If `openspec/` exists but `openspec/incubation.yaml` does NOT → manifest generation needed (step 6)
- If all three exist → skip setup, report "Already initialized" and show current manifest settings

Report what exists and what will be created.

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

### 5. Install schema

Run via Bash:

```
openspec schema fork spec-driven product-incubation
```

This creates a local copy at `openspec/schemas/product-incubation/`.

Then copy the plugin's custom schema files over the forked copy:

1. Read each file from the plugin's `schemas/product-incubation/` directory (schema.yaml and all templates)
2. Write them to `openspec/schemas/product-incubation/` (overwriting the forked spec-driven files)

This gives the project a product-incubation schema with the dual-format spec support (requirement + document areas).

### 6. Generate manifest

Create `openspec/incubation.yaml` with the full 17-area definition. Use the scope mode, domains, and phase from user answers.

**Manifest format:**

```yaml
schema: product-incubation
mode: {product | feature}
domains: [{selected domains, e.g., [frontend, backend]}]
phase: {DISCOVER | SPECIFY | ARCHITECT | BUILD+}
areas:
  - prefix: VIS
    capability: problem-vision
    scope: product
    group: WHY
    type: document
  - prefix: USR
    capability: user-scenarios
    scope: core
    group: WHY
    type: requirement
  - prefix: KPI
    capability: success-metrics
    scope: product
    group: WHY
    type: document
  - prefix: FUNC
    capability: functional-spec
    scope: core
    group: WHAT
    type: requirement
  - prefix: DATA
    capability: data-model
    scope: core
    group: WHAT
    type: requirement
  - prefix: API
    capability: api-contract
    scope: core
    group: WHAT
    type: requirement
  - prefix: UX
    capability: ux-spec
    scope: core
    group: WHAT
    type: requirement
  - prefix: ARCH
    capability: tech-architecture
    scope: core
    group: HOW
    type: requirement
  - prefix: SEC
    capability: security
    scope: core
    group: HOW
    type: requirement
  - prefix: PERF
    capability: performance
    scope: core
    group: HOW
    type: requirement
  - prefix: I18N
    capability: i18n-l10n
    scope: domain:frontend
    group: HOW
    type: requirement
  - prefix: DEC
    capability: decision-log
    scope: core
    group: HOW
    type: document
  - prefix: TEST
    capability: test-strategy
    scope: core
    group: QUALITY
    type: requirement
  - prefix: OBS
    capability: observability
    scope: product
    group: QUALITY
    type: requirement
  - prefix: DEPLOY
    capability: deployment
    scope: domain:infra
    group: QUALITY
    type: requirement
  - prefix: OPS
    capability: maintenance-ops
    scope: product
    group: QUALITY
    type: requirement
  - prefix: ROAD
    capability: roadmap
    scope: product
    group: TRACKING
    type: document
```

**IMPORTANT:**
- Always write all 17 areas to the manifest regardless of scope mode. The `scope` field on each area is used by the status command to filter display — the manifest is the complete definition.
- Areas MUST follow the exact canonical order shown above: VIS, USR, KPI, FUNC, DATA, API, UX, ARCH, SEC, PERF, I18N, DEC, TEST, OBS, DEPLOY, OPS, ROAD. The status dashboard relies on this ordering to group areas correctly.

### 7. Summary

Output a summary:

```
Product Incubation — Init Complete
═══════════════════════════════════

Mode: {Product | Feature (frontend, backend)}
Phase: {SELECTED_PHASE}
Schema: product-incubation (installed to openspec/schemas/)
Manifest: openspec/incubation.yaml (17 areas defined)

Active areas for this mode: {N} of 17
  {list active area prefixes}

Skipped (out of scope):
  {list skipped prefixes with reason, e.g., "OBS (product-only)"}

Next steps:
  1. Create your first change: `openspec change new <name>`
  2. Write a proposal covering the earliest area for your phase
  3. Run `/product-incubation:status` to check area coverage
```

## Rules

- **No spec files are created** — OpenSpec manages spec lifecycle via changes
- **Never overwrite** existing `openspec/` setup or `incubation.yaml`
- **Always install the custom schema** — the forked spec-driven base must be overwritten with product-incubation schema files
- **Manifest contains all 17 areas** — scope filtering happens at display time, not in the manifest
