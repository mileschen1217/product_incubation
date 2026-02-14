# product-incubation plugin

A Claude Code plugin for systematic product & feature incubation, backed by OpenSpec.

## Structure

```
.claude-plugin/plugin.json        # Plugin metadata
.claude-plugin/marketplace.json   # Marketplace listing
schemas/product-incubation/       # OpenSpec schema (schema.yaml + templates)
commands/                          # Slash commands (init, status)
skills/product-incubation/         # SKILL.md
README.md                          # User-facing docs
```

This is a **Claude Code plugin** — it contains markdown command/skill files and an OpenSpec schema, not application code. There are no tests to run, no build step, and no dependencies.

## Key Design Decisions

- **17 areas** grouped into WHY/WHAT/HOW/QUALITY/TRACKING — not "layers"
- **Scope tags** (`core`/`product`/`domain:x`) filter areas for product vs feature mode
- **Two area types**: requirement (13 areas, WHEN/THEN scenarios) and document (4 areas, section-based)
- **OpenSpec-backed status** — file lifecycle (specs/ = done, changes/ = in progress) replaces markers
- **No STACK area** — tech stack choices are decision records (DEC prefix)
- **DEC lives in HOW group** (area 12), not TRACKING
- **Root-level `commands/`, `skills/`, `schemas/`** — standard plugin layout (NOT under `.claude/`)

## Editing Rules

- All files reference the same 17 prefixes: VIS, USR, KPI, FUNC, DATA, API, UX, ARCH, SEC, PERF, I18N, DEC, TEST, OBS, DEPLOY, OPS, ROAD
- When changing area definitions, update ALL files: SKILL.md, init.md, status.md, schema.yaml, README.md
- Command frontmatter: no `name:` field, use `description:`, `allowed-tools:`, `disable-model-invocation: true`
- Schema templates live in `schemas/product-incubation/templates/` (not under skills/)

## Validation

After any change, verify:
1. `grep -r "STACK" --include="*.md" .` — should return nothing
2. `grep -r "18 area\|1-18\|15-18" --include="*.md" .` — should return nothing
3. All files agree on 17 areas, same prefixes, same scope tags
4. `grep -r "refresh" --include="*.md" commands/ skills/` — should return nothing (refresh command removed)
