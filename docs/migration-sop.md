# Migration SOP: Marker-Based → OpenSpec-Backed Plugin

Migrating a project from the old marker-based product-incubation plugin (v0.3.x) to the new OpenSpec-backed flow (v0.4.0+).

> **v0.5.0+ users:** The `/product-incubation:import` command automates most of this process. Run `/product-incubation:init` first, then `/product-incubation:import` — it scans your existing docs, maps them to the 17 areas, generates OpenSpec specs, and lets you review/approve each one. The manual steps below are only needed if you prefer full control or have edge cases the import command doesn't handle.

## Prerequisites

Before starting:

1. **OpenSpec CLI installed:** Run `which openspec`. If not found: `npm install -g @fission-ai/openspec`
2. **Plugin v0.4.0+ installed:** Verify the plugin source has `schemas/product-incubation/schema.yaml` and `commands/init.md` references OpenSpec (not `docs/specs/`)
3. **Git clean state:** Commit or stash any pending changes — migration creates new files and may delete old ones

## Step 1: Initialize OpenSpec

Run `/product-incubation:init` and follow the prompts. This will:

- Run `openspec init --tools claude` (creates `openspec/` directory)
- Fork and install the `product-incubation` schema
- Ask scope mode (Product/Feature)
- Generate `openspec/incubation.yaml` manifest with all 17 areas

After init, verify:

```
openspec/
  schemas/product-incubation/schema.yaml   # Custom schema
  incubation.yaml                          # Area manifest
  specs/                                   # Empty (done specs go here)
  changes/                                 # Empty (in-progress work goes here)
```

## Step 2: Inventory existing docs

Scan the old `docs/` structure. The old plugin used this file mapping:

| Old file | Area prefix | Capability (new) | Area type |
|----------|-------------|-------------------|-----------|
| `docs/specs/VIS-problem.md` | VIS | `problem-vision` | document |
| `docs/specs/USR-scenarios.md` | USR | `user-scenarios` | requirement |
| `docs/specs/KPI-metrics.md` | KPI | `success-metrics` | document |
| `docs/specs/FUNC-features.md` | FUNC | `functional-spec` | requirement |
| `docs/specs/DATA-model.md` | DATA | `data-model` | requirement |
| `docs/specs/API-contract.md` | API | `api-contract` | requirement |
| `docs/specs/UX-spec.md` | UX | `ux-spec` | requirement |
| `docs/architecture.md` | ARCH | `tech-architecture` | requirement |
| `docs/security.md` | SEC | `security` | requirement |
| `docs/specs/PERF-targets.md` | PERF | `performance` | requirement |
| `docs/specs/I18N-plan.md` | I18N | `i18n-l10n` | requirement |
| `docs/decisions/*.md` | DEC | `decision-log` | document |
| `docs/specs/TEST-strategy.md` | TEST | `test-strategy` | requirement |
| `docs/observability.md` | OBS | `observability` | requirement |
| `docs/specs/DEPLOY-plan.md` | DEPLOY | `deployment` | requirement |
| `docs/specs/OPS-runbooks.md` | OPS | `maintenance-ops` | requirement |
| `docs/PROJECT-STATUS.md` | ROAD | `roadmap` | document |

For each file that exists, note its status markers:

- `✓` on requirement headings = **done** → will become an archived spec in `openspec/specs/`
- `◐` on requirement headings = **in progress** → will become a change spec in `openspec/changes/`
- No marker / skeleton only = **not started** → skip, don't migrate empty scaffolding

## Step 3: Determine migration status per area

For each old file, classify the **overall area status** by its markers:

| Old marker state | New location |
|------------------|--------------|
| All requirements `✓` (or rich content for document areas) | `openspec/specs/<capability>/spec.md` |
| Any `◐` or mix of `✓` and unmarked | `openspec/changes/migration/specs/<capability>/spec.md` |
| All unmarked / skeleton / empty | Skip — do not migrate |

## Step 4: Migrate done areas to `openspec/specs/`

For each area classified as **done**:

1. Create directory: `openspec/specs/<capability>/`
2. Create `openspec/specs/<capability>/spec.md`
3. Convert content to the OpenSpec format based on area type:

**For requirement areas** (FUNC, DATA, API, UX, SEC, PERF, USR, TEST, I18N, OBS, DEPLOY, OPS, ARCH):

Convert old `### PREFIX-NNN: Title ✓` headings to OpenSpec requirement format:

```markdown
## ADDED Requirements

### Requirement: <title from old heading, without prefix or marker>
<content from old section — rewrite with SHALL/MUST language>

#### Scenario: <derive from acceptance criteria or content>
- **WHEN** <condition>
- **THEN** <expected outcome>
```

Rules:
- Strip the `PREFIX-NNN:` ID and `✓`/`◐` markers from headings
- Every requirement MUST have at least one `#### Scenario:` with WHEN/THEN
- Use SHALL/MUST for normative statements
- Derive scenarios from old acceptance criteria, or write them from the requirement description

**For document areas** (VIS, KPI, DEC, ROAD):

Convert to area-specific section format. **Important:** Each `### Requirement:` block must include a one-line SHALL statement before the sections to pass OpenSpec validation.

```markdown
## ADDED Requirements

### Requirement: <title>
This requirement SHALL define the <area-specific content>.

#### <Area-specific section 1>
<content>

#### <Area-specific section 2>
<content>
```

Area-specific sections:
- VIS: Problem Statement / Target Users / Market Context
- KPI: Metric / Target / Measurement Method
- DEC: Context / Decision / Consequences / Alternatives Considered
- ROAD: Phase / Milestones / Priority / Timeline

## Step 5: Migrate in-progress areas to `openspec/changes/`

For areas classified as **in progress**:

1. Create a migration change: `openspec new change migration` (or use an existing change)
2. Create `openspec/changes/migration/specs/<capability>/spec.md`
3. Convert content using the same format rules as Step 4
4. For requirements that were `✓` in a mixed file, still include them — the entire area migrates together

After all in-progress areas are migrated, they'll show as `◐` in the status dashboard. When work completes, archive the change: `openspec archive migration`

## Step 6: Migrate decision records

Old decision records lived at `docs/decisions/YYYY-MM-DD-*.md` using MADR format.

Convert to a single OpenSpec spec for the `decision-log` capability:

1. Target: `openspec/specs/decision-log/spec.md` (if all decisions are finalized) or `openspec/changes/migration/specs/decision-log/spec.md` (if decisions are still evolving)
2. Format — DEC is a document area:

```markdown
## ADDED Requirements

### Requirement: <decision short title>
<decision description>

#### Context
<what motivated this decision>

#### Decision
<what was decided>

#### Consequences
<positive and negative outcomes>

#### Alternatives Considered
<what else was evaluated and why rejected>
```

3. Repeat `### Requirement:` for each decision record

## Step 7: Verify migration

Run `/product-incubation:status` and check the dashboard:

- Areas with archived specs should show `✓`
- Areas with change specs should show `◐`
- Unmigrated/empty areas should show `○`
- The total count should match your inventory from Step 2

Cross-check:

```
# Count archived specs — should match "done" areas
ls openspec/specs/*/spec.md | wc -l

# Count in-progress specs — should match "in progress" areas
ls openspec/changes/*/specs/*/spec.md 2>/dev/null | wc -l
```

## Step 8: Clean up old artifacts

After verifying the migration is correct:

1. **Remove old spec files:**
   ```
   rm -rf docs/specs/
   rm -f docs/architecture.md docs/security.md docs/observability.md docs/PROJECT-STATUS.md
   rm -rf docs/decisions/
   ```

2. **Keep non-spec docs:** Do NOT remove `docs/plans/`, `docs/development.md`, or other docs that aren't part of the 17-area mapping.

3. **Commit the migration:**
   ```
   git add openspec/
   git add -u docs/
   git commit -m "Migrate product incubation from marker-based to OpenSpec-backed flow"
   ```

## Rollback

If migration goes wrong:

1. `git checkout -- docs/` to restore old files
2. `rm -rf openspec/` to remove OpenSpec artifacts
3. Re-run with the old plugin version

## Quick Reference: Prefix → Capability

| Prefix | Capability name | Type |
|--------|----------------|------|
| VIS | `problem-vision` | document |
| USR | `user-scenarios` | requirement |
| KPI | `success-metrics` | document |
| FUNC | `functional-spec` | requirement |
| DATA | `data-model` | requirement |
| API | `api-contract` | requirement |
| UX | `ux-spec` | requirement |
| ARCH | `tech-architecture` | requirement |
| SEC | `security` | requirement |
| PERF | `performance` | requirement |
| I18N | `i18n-l10n` | requirement |
| DEC | `decision-log` | document |
| TEST | `test-strategy` | requirement |
| OBS | `observability` | requirement |
| DEPLOY | `deployment` | requirement |
| OPS | `maintenance-ops` | requirement |
| ROAD | `roadmap` | document |
