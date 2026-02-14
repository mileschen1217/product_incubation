# product-incubation plugin

A Claude Code plugin for systematic product & feature incubation.

## Structure

```
.claude-plugin/plugin.json        # Plugin metadata
commands/                          # Slash commands (init, status, refresh, sync)
skills/product-incubation/         # SKILL.md + templates
README.md                          # User-facing docs
```

This is a **Claude Code plugin** — it contains markdown command/skill files, not application code. There are no tests to run, no build step, and no dependencies.

## Key Design Decisions

- **17 areas** grouped into WHY/WHAT/HOW/QUALITY/TRACKING — not "layers"
- **Scope tags** (`core`/`product`/`domain:x`) filter areas for product vs feature mode
- **No STACK area** — tech stack choices are decision records (DEC prefix)
- **DEC lives in HOW group** (area 12), not TRACKING
- **Root-level `commands/` and `skills/`** — standard plugin layout (NOT under `.claude/`)

## Editing Rules

- All files reference the same 17 prefixes: VIS, USR, KPI, FUNC, DATA, API, UX, ARCH, SEC, PERF, I18N, DEC, TEST, OBS, DEPLOY, OPS, ROAD
- When changing area definitions, update ALL files: SKILL.md, init.md, status.md, refresh.md, sync.md, README.md, spec-template.md
- Command frontmatter: no `name:` field, use `description:`, `allowed-tools:`, `disable-model-invocation: true`
- Templates use `{PREFIX}-{NNN}` format (not `{AREA}-{SEQ}`)

## Validation

After any change, verify:
1. `grep -r "STACK" --include="*.md" .` — should return nothing
2. `grep -r "18 area\|1-18\|15-18" --include="*.md" .` — should return nothing
3. All files agree on 17 areas, same prefixes, same scope tags
