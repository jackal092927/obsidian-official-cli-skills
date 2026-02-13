# obsidian-official-cli-skills

Agent skill for the [Obsidian CLI](https://obsidian.md) (1.12+). Guides coding agents to use Obsidian CLI safely — avoiding silent failures in tasks, tags, search, properties, and file creation.

[![license MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

## Why do you need this!

> [!WARNING]
> **Without this skill, your agent will silently get wrong answers from Obsidian CLI.**
> Commands succeed with exit code 0 — but return empty or incorrect data.

From 57 scenario tests against Obsidian CLI 1.12 (tested Feb. 13, 2026):

- **13 silent failures (22.8%)** — command "succeeds" but returns wrong/empty data
- **24 material issues (42.1%)** — skill-guided usage is meaningfully safer

| Trap | What happens without the skill | With skill |
|------|-------------------------------|------------|
| Task scope | `tasks todo` → **0 results** (scoped to "active file" = nothing) | `tasks all todo` |
| Tag scope | `tags counts` → **empty** | `tags all counts` |
| Property format | `properties format=json` → **returns YAML** | `properties format=tsv` |
| Search format | `search query="x"` → **plain text, no structure** | `search query="x" format=json matches` |
| Create silent | `create name="x" content="y"` → **opens Obsidian UI** | add `silent` flag |
| Exit codes | error message but **`$?` = 0** | parse output for `Error:` |

Every one of these looks like it worked. None of them did. Full evidence: [`HIGH_RISK_REPORT.md`](HIGH_RISK_REPORT.md)

## Install

### Recommended: Claude Code plugin marketplace (auto-updates)

```
/plugin marketplace add jackal092927/obsidian-official-cli-skills
/plugin install obsidian-cli@obsidian-official-cli-skills
```

Auto-updates on startup — no manual action needed after initial install.

### Cross-agent: npx (Claude Code, Codex, Cursor, etc.)

```bash
npx skills add jackal092927/obsidian-official-cli-skills
```

Works with 20+ AI coding agents that support the [Agent Skills](https://agentskills.io) standard.

### Manual

<details>
<summary>Claude Code</summary>

```bash
mkdir -p .claude/skills/obsidian-cli
curl -o .claude/skills/obsidian-cli/SKILL.md \
  https://raw.githubusercontent.com/jackal092927/obsidian-official-cli-skills/main/skills/obsidian-cli/SKILL.md
```

</details>

<details>
<summary>Codex / Cursor / other agents</summary>

```bash
# Codex
mkdir -p .codex/skills/obsidian-cli
curl -o .codex/skills/obsidian-cli/SKILL.md \
  https://raw.githubusercontent.com/jackal092927/obsidian-official-cli-skills/main/skills/obsidian-cli/SKILL.md

# Or copy SKILL.md into your agent's context/system prompt
```

</details>

## Updating

| Method | How to update |
|--------|--------------|
| Plugin marketplace | Automatic at startup — no action needed |
| npx | `npx skills update` |
| Manual | Re-run the curl command above (overwrites existing SKILL.md) |

## Prerequisites

1. [Obsidian](https://obsidian.md) **1.12+** installed
2. Enable CLI: **Settings > General > Command line interface** > follow the registration prompt
3. Restart terminal (`obsidian` should be in PATH)

## What's included

| File | Covers |
|------|--------|
| `skills/obsidian-cli/SKILL.md` | 34 commands with correct syntax, gotcha warnings, safety rules, error handling |
| `HIGH_RISK_REPORT.md` | 5 severe issues with reproduction commands and raw CLI evidence |
| `reports/` | Raw stdout captures and command matrix from benchmark runs |

## Contributing

If you find a new gotcha or a command that changed behavior:

1. Reproduce the issue and capture CLI output
2. Open an issue with: naive command, expected output, actual output
3. PRs welcome — edit `skills/obsidian-cli/SKILL.md` directly

## License

MIT
