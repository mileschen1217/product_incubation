# Plugin Builder Skill & Command — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create a personal skill + command in `~/.claude/` that helps create and edit Claude Code plugins without trial-and-error.

**Architecture:** Two files — a SKILL.md cheatsheet that auto-activates during plugin work, and a scaffold-plugin.md command that generates correct plugin skeletons.

**Tech Stack:** Markdown only (no application code)

---

### Task 1: Create the plugin-builder skill

**Files:**
- Create: `~/.claude/skills/plugin-builder/SKILL.md`

**Step 1: Write the skill file**

Create `~/.claude/skills/plugin-builder/SKILL.md` with these sections:

1. **Frontmatter** — name: `plugin-builder`, description with trigger phrases ("create a plugin", "plugin.json", "marketplace.json", "plugin structure")

2. **plugin.json schema** — Required: `name` (kebab-case, regex `/^[a-z][a-z0-9]*(-[a-z0-9]+)*$/`). Recommended: version, description, author, repository, license. Component path overrides: commands, agents, hooks, mcpServers (must start with `./`, no `../`).

3. **marketplace.json schema** — Full schema with `$schema` field. Source field rules: `"./"` for same-repo plugin, `{"source": "url", "url": "https://..."}` for remote. Name convention for marketplace.

4. **Directory layout** — Standard structure diagram. Rule: commands/skills/agents/hooks at root, only plugin.json and marketplace.json inside `.claude-plugin/`.

5. **Command files format** — No `name:` in frontmatter. Filename = command name. Fields: description (required), allowed-tools, model, disable-model-invocation, argument-hint.

6. **Skill files format** — Must be `SKILL.md` not README.md. Path: `skills/<name>/SKILL.md`. Frontmatter: name, description, version.

7. **Agent files format** — Frontmatter: name, description with `<example>` blocks, tools, model, color.

8. **Hooks format** — `hooks/hooks.json` structure. Use `${CLAUDE_PLUGIN_ROOT}`. Hook types: command, prompt. Events list.

9. **MCP servers** — `.mcp.json` at root. Server types.

10. **Install instructions template** — Two-step marketplace flow for README.

11. **Gotchas checklist** — Every known pitfall:
    - `source: "."` vs `"./"`
    - `name:` in command frontmatter
    - SKILL.md vs README.md
    - Commands inside .claude-plugin/ (wrong)
    - Missing `$schema` in marketplace.json
    - Plugin name with underscores (must be kebab-case)
    - Hardcoded paths (use `${CLAUDE_PLUGIN_ROOT}`)

Use the product-incubation plugin (`/home/moxa/miles/claude_plugins/product-incubation/`) as a real working example throughout.

**Step 2: Verify the skill loads**

Run: `ls ~/.claude/skills/plugin-builder/SKILL.md`
Expected: file exists

**Step 3: Commit**

```bash
git add ~/.claude/skills/plugin-builder/SKILL.md
git commit -m "feat: add plugin-builder skill cheatsheet"
```

---

### Task 2: Create the scaffold-plugin command

**Files:**
- Create: `~/.claude/commands/scaffold-plugin.md`

**Step 1: Write the command file**

Create `~/.claude/commands/scaffold-plugin.md` with:

1. **Frontmatter** — description, allowed-tools: [Write, Bash, AskUserQuestion, Glob], argument-hint: `<plugin-name>`

2. **Validation step** — Check `$ARGUMENTS` is kebab-case. Error if not.

3. **Interactive questions** — Use AskUserQuestion:
   - Components needed: commands, skills, agents, hooks, MCP (multi-select)
   - Include marketplace.json? (yes/no)
   - Author name
   - Short description

4. **File generation rules** — For each file, specify exact content:

   **Always generated:**
   - `.claude-plugin/plugin.json` — with name, version "0.1.0", description, author, repository placeholder, license MIT
   - `README.md` — with plugin name, description, install instructions (marketplace flow if marketplace.json included, otherwise note about manual install)
   - `CLAUDE.md` — minimal: plugin name, structure overview, editing rules placeholder
   - `LICENSE` — MIT with current year and author name

   **If marketplace.json requested:**
   - `.claude-plugin/marketplace.json` — with `$schema`, name, owner, metadata, plugins array with `source: "./"`

   **If components selected (directories only):**
   - `commands/` — create empty directory
   - `skills/` — create empty directory
   - `agents/` — create empty directory
   - `hooks/hooks.json` — skeleton with empty hooks object
   - `.mcp.json` — skeleton with empty object

5. **Summary** — Print what was generated and next steps

**Step 2: Verify the command file exists**

Run: `ls ~/.claude/commands/scaffold-plugin.md`
Expected: file exists

**Step 3: Commit**

```bash
git add ~/.claude/commands/scaffold-plugin.md
git commit -m "feat: add /scaffold-plugin command"
```

---

### Task 3: Verify both work together

**Step 1:** Start a new Claude Code session and verify:
- `/scaffold-plugin` appears in command list
- `plugin-builder` skill appears in skills list

**Step 2:** Test with a dry run: `/scaffold-plugin test-plugin`

**Step 3:** Clean up test output if needed
