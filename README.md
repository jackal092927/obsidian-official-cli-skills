# Obsidian-CLI-Skills

A single-file skill that guides coding agents to use Obsidian CLI safely and wisely.

## Why this exists

Obsidian CLI is powerful, but several defaults are risky for agents:
- command succeeds yet returns misleading data
- output format is not what agents expect
- some errors still return exit code `0`

From a focused scenario suite (`57` scenarios):
- `13/57` silent failures (`22.8%`)
- `24/57` material issues where skill-guided usage is safer/better (`42.1%`)

## High-risk examples

| Risk | Naive | Skill-guided |
|---|---|---|
| Task scope trap | `obsidian tasks todo` -> `0`/empty | `obsidian tasks all todo` |
| Tag scope trap | `obsidian tags counts` -> empty | `obsidian tags all counts` |
| Property format trap | `properties format=json` -> YAML | `properties format=tsv` |
| Search parseability trap | `search query="x"` -> text lines | `search query="x" format=json matches` |
| Exit-code trap | error text + exit code `0` | parse `Error:` in output, not only `$?` |

See evidence: [`HIGH_RISK_REPORT.md`](HIGH_RISK_REPORT.md).

## Install

### Claude Code
```bash
mkdir -p .claude/skills/obsidian-cli
curl -o .claude/skills/obsidian-cli/SKILL.md \
  https://raw.githubusercontent.com/jackal092927/obsidian-official-cli-skills/main/SKILL.md
```

### Codex
```bash
mkdir -p .codex/skills/obsidian-cli
curl -o .codex/skills/obsidian-cli/SKILL.md \
  https://raw.githubusercontent.com/jackal092927/obsidian-official-cli-skills/main/SKILL.md
```

## Scope

This project intentionally ships:
- `SKILL.md` (the guidance)
- a minimal severe-issue evidence report

It does not require a wrapper binary or runtime dependency.
