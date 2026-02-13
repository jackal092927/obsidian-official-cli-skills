# Obsidian CLI Skill: High-Risk Evidence Report

Date: 2026-02-13
CLI: 1.12.1
Vault: (tested in Obsidian Sandbox and a personal vault)

## Purpose
This report only documents severe, motivation-critical issues that can mislead coding agents.

## Metrics
Scenario suite totals:
- Silent failures: `13/57` (`22.8%`)
- Material issues: `24/57` (`42.1%`)

## Severe Issue 1: Task scope default is misleading

Naive:
```bash
obsidian tasks todo total
```
Observed: `0`

Skill-guided:
```bash
obsidian tasks all todo total
```
Observed: correct vault-wide task count (non-zero)

Evidence:
- `reports/raw/cmp_015_tasks_direct_direct_tasks.stdout.txt`
- `reports/raw/cmp_015_tasks_skill_skill_guided_tasks.stdout.txt`

## Severe Issue 2: Tag scope default is misleading

Naive:
```bash
obsidian tags counts
```
Observed: `No tags found.`

Skill-guided:
```bash
obsidian tags all counts
```
Observed: full vault tag table (correct count)

Evidence:
- `reports/raw/cmp_013_tags_direct_direct_tags.stdout.txt`
- `reports/raw/cmp_013_tags_skill_skill_guided_tags.stdout.txt`

## Severe Issue 3: `properties format=json` does not return JSON

Naive:
```bash
obsidian properties path=<file> format=json
```
Observed: YAML-like output, not JSON.

Skill-guided:
```bash
obsidian properties path=<file> format=tsv
```
Observed: tabular `key<TAB>value`, stable for parsing.

Evidence:
- `reports/raw/cmp_011_properties_direct_direct_properties.stdout.txt`
- `reports/raw/cmp_011_properties_skill_skill_guided_properties.stdout.txt`

## Severe Issue 4: Search default output is hard for agents to consume

Naive:
```bash
obsidian search query="benchmark"
```
Observed: plain text path list.

Skill-guided:
```bash
obsidian search query="benchmark" format=json matches
```
Observed: structured JSON with file/matches/line numbers.

Evidence:
- `reports/raw/cmp_012_search_direct_direct_search.stdout.txt`
- `reports/raw/cmp_012_search_skill_skill_guided_search.stdout.txt`

## Severe Issue 5: Error text with exit code 0

Command:
```bash
obsidian base:views
```
Observed output includes:
`Error: Active file is not a base file: ...`

Observed exit code in matrix: `0`.

Risk:
Shell automation relying only on `$?` misses failures.

Evidence:
- `reports/raw/cov_005_direct_base_views.stdout.txt`
- `reports/obsidian-cli-command-matrix.csv` (`scenario_id=cov_005`)

## Conclusion
A lightweight skill layer is justified because the highest-risk failures are default-behavior traps, not syntax mistakes. Skill guidance prevents these traps without adding runtime complexity.
