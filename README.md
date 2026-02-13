# obsidian-official-cli-skills

Agent skill for the [Obsidian CLI](https://obsidian.md) (1.12+). Guides coding agents to use Obsidian CLI safely — avoiding silent failures in tasks, tags, search, properties, and file creation.

[![license MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

## Install

```bash
npx skills add jackal092927/obsidian-official-cli-skills
```

Or manually:

<details>
<summary>Claude Code</summary>

```bash
mkdir -p .claude/skills/obsidian-cli
curl -o .claude/skills/obsidian-cli/SKILL.md \
  https://raw.githubusercontent.com/jackal092927/obsidian-official-cli-skills/main/SKILL.md
```

</details>

<details>
<summary>Codex / Cursor / other agents</summary>

```bash
# Codex
mkdir -p .codex/skills/obsidian-cli
curl -o .codex/skills/obsidian-cli/SKILL.md \
  https://raw.githubusercontent.com/jackal092927/obsidian-official-cli-skills/main/SKILL.md

# Or copy SKILL.md into your agent's context/system prompt
```

</details>

## Prerequisites

1. [Obsidian](https://obsidian.md) **1.12+** installed
2. Enable CLI: **Settings > General > Command line interface** > follow the registration prompt
3. Restart terminal (`obsidian` should be in PATH)

## Why this exists

Obsidian CLI is powerful, but several defaults silently produce wrong results for agents. From 57 scenario tests:

- **13** silent failures (22.8%) — command succeeds but returns wrong/empty data
- **24** material issues (42.1%) — skill-guided usage is safer or gives better output

| Trap | What goes wrong | Fix |
|------|----------------|-----|
| Task scope | `tasks todo` → 0 results (scoped to "active file" = nothing) | `tasks all todo` |
| Tag scope | `tags counts` → empty | `tags all counts` |
| Property format | `properties format=json` → returns YAML | `properties format=tsv` |
| Search format | `search query="x"` → plain text | `search query="x" format=json matches` |
| Create silent | `create name="x" content="y"` → opens Obsidian UI | add `silent` flag |
| Exit codes | error message but `$?` = 0 | parse output for `Error:` |

Full evidence: [`HIGH_RISK_REPORT.md`](HIGH_RISK_REPORT.md)

## What's included

| File | Covers |
|------|--------|
| `SKILL.md` | 34 commands with correct syntax, gotcha warnings, safety rules, error handling |
| `HIGH_RISK_REPORT.md` | 5 severe issues with reproduction commands and raw CLI evidence |
| `reports/` | Raw stdout captures and command matrix from benchmark runs |

## Updating

To update the skill after a new release:

```bash
npx skills add jackal092927/obsidian-official-cli-skills
```

Or re-run the manual curl command — it overwrites the existing `SKILL.md`.

## Contributing

If you find a new gotcha or a command that changed behavior:

1. Reproduce the issue and capture CLI output
2. Open an issue with: naive command, expected output, actual output
3. PRs welcome — edit `SKILL.md` directly

## License

MIT
