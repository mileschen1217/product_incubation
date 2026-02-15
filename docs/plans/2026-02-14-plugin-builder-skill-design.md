# Plugin Builder Skill & Command — Design

## Goal

Personal tooling in `~/.claude/` to help create and edit Claude Code plugins without trial-and-error. Two components:

1. **Skill** (`~/.claude/skills/plugin-builder/SKILL.md`) — Cheatsheet that auto-activates during plugin work. Prevents format/schema mistakes.
2. **Command** (`~/.claude/commands/scaffold-plugin.md`) — `/scaffold-plugin <name>` generates a correct plugin skeleton.

## Skill: plugin-builder

**Location:** `~/.claude/skills/plugin-builder/SKILL.md`

**Trigger:** Activates when Claude detects plugin-related work — creating, editing, debugging plugins, or when user mentions "plugin", "marketplace.json", "plugin.json", etc.

**Content sections:**

1. **plugin.json schema** — Required fields, name validation (kebab-case regex), optional fields (version, description, author, repository, license, keywords), component path overrides
2. **marketplace.json schema** — name, owner, metadata, plugins array. Source field: `"./"` for same-repo, `{"source": "url", "url": "..."}` for remote. Marketplace name convention.
3. **Directory layout** — Commands/skills/agents/hooks at plugin root, NOT inside `.claude-plugin/`. Only `plugin.json` and `marketplace.json` go in `.claude-plugin/`.
4. **Command files** — No `name:` in frontmatter. Filename = command name. Required: `description`. Optional: `allowed-tools`, `model`, `disable-model-invocation`, `argument-hint`.
5. **Skill files** — Must be `SKILL.md` (not README.md). Lives in `skills/<name>/SKILL.md`. Frontmatter: name, description, version.
6. **Agent files** — Frontmatter: name, description (with `<example>` blocks), tools, model, color.
7. **Hooks** — `hooks/hooks.json` format. Use `${CLAUDE_PLUGIN_ROOT}` for paths. Hook types: command, prompt. Events: PreToolUse, PostToolUse, SessionStart, etc.
8. **MCP servers** — `.mcp.json` at root. Server types: stdio, SSE, HTTP, WebSocket.
9. **Path rules** — Must start with `./`, no `../`, no absolute paths, forward slashes only.
10. **Install instructions** — Two-step: `marketplace add` then `plugin install`. Format for README.
11. **Gotchas checklist** — All known pitfalls from real experience.

## Command: scaffold-plugin

**Location:** `~/.claude/commands/scaffold-plugin.md`

**Usage:** `/scaffold-plugin my-plugin-name`

**Flow:**
1. Validate plugin name (kebab-case)
2. Ask: what components? (commands, skills, agents, hooks, MCP) — multi-select
3. Ask: include marketplace.json? (yes/no)
4. Ask: author name, description
5. Generate files

**Generated files (always):**
- `.claude-plugin/plugin.json` — filled with name, version, description, author
- `README.md` — with correct install instructions
- `CLAUDE.md` — minimal project rules
- `LICENSE` — MIT

**Generated files (conditional):**
- `.claude-plugin/marketplace.json` — if distribution requested
- `commands/` — empty directory, if commands selected
- `skills/` — empty directory, if skills selected
- `agents/` — empty directory, if agents selected
- `hooks/hooks.json` — skeleton config, if hooks selected
- `.mcp.json` — skeleton config, if MCP selected

**Not generated:** No example/template files in commands/, skills/, agents/. Those are created later by Claude with the skill cheatsheet guiding correct formats.

## What This Prevents

Based on real issues encountered:
- `source: "."` instead of `source: "./"` in marketplace.json
- Wrong install instructions (using `/plugin add` instead of marketplace flow)
- Author name in wrong field
- Incorrect directory nesting (putting commands inside .claude-plugin/)
- Missing marketplace.json for distribution
- Wrong command frontmatter (including `name:` field)
