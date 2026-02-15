---
description: Update the current lifecycle phase in the incubation manifest.
allowed-tools: [Read, Write, AskUserQuestion]
disable-model-invocation: true
---

# Product Incubation: Set Phase

You are updating the lifecycle phase in the project's incubation manifest.

## Steps

### 1. Read manifest

Read `openspec/incubation.yaml`. If it doesn't exist:

> No incubation manifest found. Run `/product-incubation:init` to set up first.

Stop here.

### 2. Show current state

Extract the current `phase:` value from the manifest. Display:

```
Current phase: {CURRENT_PHASE}

Lifecycle phases:
  DISCOVER → SPECIFY → ARCHITECT → BUILD → VALIDATE → SHIP
```

### 3. Ask new phase

Use AskUserQuestion to ask:

> **Which phase should this project move to?**
>
> - **DISCOVER** — Defining problem, vision, users, metrics (Areas 1-3)
> - **SPECIFY** — Specifying features, data, API, UX (Areas 4-7)
> - **ARCHITECT** — Designing architecture, security, performance, decisions (Areas 8-12)
> - **BUILD** — Implementing features, tracking work via OpenSpec changes
> - **VALIDATE** — Executing test strategy, setting up observability (Areas 13-14)
> - **SHIP** — Deployment, ops, runbooks, roadmap (Areas 15-17)

### 4. Update manifest

Update the `phase:` field in `openspec/incubation.yaml` to the selected phase. Use the Write tool to write the updated file, preserving all other content exactly as-is.

### 5. Confirm

Output:

```
Phase updated: {OLD_PHASE} → {NEW_PHASE}

Phase focus areas:
  {list the area prefixes relevant to the new phase}

Next steps:
  → Run `/product-incubation:status` to see area coverage for this phase
```

## Rules

- **Never create the manifest** — this command only updates an existing one
- **Preserve all other manifest content** — only change the `phase:` field
- **Use exact phase names** — DISCOVER, SPECIFY, ARCHITECT, BUILD, VALIDATE, SHIP
