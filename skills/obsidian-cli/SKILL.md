---
name: obsidian-cli
description: >
  Use the official Obsidian CLI (1.12+) for vault operations.
  Prefer CLI for index-powered ops (search, backlinks, tags, tasks, properties, bases).
  Fall back to file tools when Obsidian is not running or for simple read/write.
triggers:
  - obsidian cli
  - vault search
  - backlinks
  - vault tags
  - vault tasks
  - property set
  - base query
---

# Obsidian CLI — Agent Reference

## When to Use CLI vs File Tools

**Use CLI** when you need Obsidian's index or app features:
search, backlinks, links, tags, tasks, properties, bases, templates, outline, orphans, unresolved links

**Use file tools** (Read/Write/Edit/Grep/Glob) for:
simple file read/write, bulk text replacement, grep across files — no app dependency

Rule of thumb: if Obsidian's index adds value, use CLI. If it's plain text manipulation, use file tools.

## Syntax Basics

```
obsidian <command> [param=value ...] [flag ...]
```

- **Vault targeting**: `vault="My Vault"` as first param, or run from inside vault dir
- **File targeting**: `path=exact/path.md` (vault-relative) vs `file=name` (link-style resolution)
- **Params**: `key=value` — quote values with spaces: `name="My Note"`
- **Flags**: boolean switches, no `=` — e.g. `silent`, `overwrite`, `counts`, `total`
- **Multiline**: `\n` for newline, `\t` for tab in content strings
- **Structured output**: `format=json` (search, base:query), `format=tsv` (properties, tags)
- **Clipboard**: append `--copy` to copy output

## Key Commands

### Read & Write
```sh
obsidian read path=<path>                                    # read file content
obsidian append path=<path> content="<text>"                 # append to file
obsidian prepend path=<path> content="<text>"                # prepend after frontmatter
obsidian create name=<name> content="<text>" silent          # create file (safe — won't overwrite)
obsidian create name=<name> template=<template> overwrite silent  # create from template
obsidian move path=<from> to=<to>                            # move/rename file
obsidian delete path=<path>                                  # move to system trash (safe)
```
Gotchas:
- `create` without `silent` opens the file in Obsidian's UI — always add `silent` during agent operations.
- `create` doesn't auto-create directories — use `mkdir -p` via Bash first if the parent folder doesn't exist.
- `create` with `template=` may place the file in the template's configured folder, ignoring `path=`. Verify the actual path with `search` or `files` after creation.

### Daily Notes
```sh
obsidian daily                          # open today's daily note
obsidian daily:read                     # read daily note content
obsidian daily:append content="<text>"  # append to daily note
obsidian daily:prepend content="<text>" # prepend to daily note
```

### Properties (Frontmatter)
```sh
obsidian property:set name=<key> value=<val> path=<path>              # set property
obsidian property:set name=<key> value=<val> type=<type> path=<path>  # set with explicit type
obsidian property:read name=<key> path=<path>                         # read one property
obsidian property:remove name=<key> path=<path>                       # remove property
obsidian properties path=<path>                                       # list all properties
obsidian properties path=<path> format=tsv                            # list as TSV (key\tvalue)
```

### Search
```sh
obsidian search query="<text>"                          # search vault (file paths)
obsidian search query="<text>" path=<folder> limit=10   # scoped search
obsidian search query="<text>" format=json              # JSON array of paths
obsidian search query="<text>" format=json matches      # structured JSON with line numbers (preferred)
obsidian search query="<text>" matches                  # text format with matching lines
```
Tip: `format=json matches` returns `[{"file":"path","matches":[{"line":N,"text":"..."}]}]` — use this for programmatic search.

### Tags & Tasks
```sh
obsidian tags all counts                            # list ALL vault tags with counts
obsidian tags all counts sort=count                 # sorted by frequency
obsidian tag name=<tag>                             # files with specific tag
obsidian tasks all todo                              # vault-wide open tasks
obsidian tasks all done                              # vault-wide completed tasks
obsidian tasks daily                                 # tasks from daily note
obsidian tasks path=<path>                           # tasks in file/folder
obsidian task ref="<path>:<line>" toggle            # toggle task status
obsidian task ref="<path>:<line>" done              # mark done
```
Note: `tags` without `all` lists tags for the active/specified file only.
Note: `tasks` without a scope (`all`, `daily`, `path=`) defaults to the active file — use `tasks all` for vault-wide results.

### Links & Graph
```sh
obsidian backlinks path=<path>          # incoming links to file
obsidian backlinks path=<path> counts   # with link counts
obsidian links path=<path>              # outgoing links from file
obsidian unresolved                     # broken/unresolved links
obsidian orphans                        # files with no incoming links
obsidian deadends                       # files with no outgoing links
```

### Structure
```sh
obsidian files folder=<path> ext=md     # list files in folder
obsidian folders                        # list all folders
obsidian outline path=<path>            # heading structure of file
```

### Bases
```sh
obsidian bases                                          # list .base files
obsidian base:query path=<path> format=json             # query a base
obsidian base:query path=<path> view=<name> format=json # query specific view
```

### Templates
```sh
obsidian templates                        # list available templates
obsidian template:read name=<name>        # read template source
obsidian template:read name=<name> resolve # render with variables filled
```

## Safety Rules

**Never run without explicit user request:**
- `eval` — arbitrary code execution
- `delete permanent` — bypasses trash
- `plugin:install` / `plugin:uninstall` — modifies plugin state
- `dev:cdp` — Chrome DevTools protocol access

**Prefer safe patterns:**
- Use `append` / `prepend` over full file overwrite
- `create` without `overwrite` flag is safe (won't replace existing files)
- Confirm with user before bulk property changes, file moves, or deletes

## Diagnostics
```sh
obsidian vault                   # confirm CLI connection — returns vault name, path, file count
obsidian version                 # show CLI version (e.g. "1.12.1 (installer 1.11.4)")
```

## Error Handling

| Symptom | Likely cause | Action |
|---------|-------------|--------|
| `obsidian version` fails | CLI not installed or not on PATH | Fall back to file tools |
| Command hangs or times out | Obsidian app not running | Start Obsidian or use file tools |
| "Unknown command" | CLI version too old | Run `obsidian help` to check available commands |
| Empty results from search/tags | Vault index not ready | Wait a moment, retry, or use Grep as fallback |

**General fallback**: if CLI is unavailable, use Read/Write/Edit/Grep/Glob for file operations. The CLI requires the Obsidian desktop app to be running.
