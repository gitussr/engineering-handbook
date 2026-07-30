---
title: "shellcheck — Static Analysis for Shell Scripts"
description: "Analyze a shell script for common bugs — unquoted variables, wrong test operators, unreachable code — without running it."
relatedConcepts: ["18-shell-scripting/debugging-scripts-set-x-shellcheck", "18-shell-scripting/real-automation-scripts"]
relatedCommands: ["set"]
careerRelevance: ["devops", "sre", "linux-administrator", "software-engineering"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["shellcheck command", "shellcheck bash script", "SC2086", "bash static analysis tool"]
canonicalUrl: "/commands/shellcheck"
---

# shellcheck

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Software Engineering

> **TL;DR:** `shellcheck script.sh` analyzes a script without running it and flags known mistake
> patterns — most commonly unquoted variables — each with an `SC####` code linking to a detailed
> explanation.

## Purpose

`shellcheck` statically analyzes shell scripts for common bugs before they run — see
[Debugging Scripts](../docs/18-shell-scripting/debugging-scripts-set-x-shellcheck.md) for the full
concept.

## Syntax

```
shellcheck [OPTIONS] SCRIPT...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SCRIPT` | One or more script file paths to analyze | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-s SHELL` | Force a specific shell dialect (`bash`, `sh`, `dash`, `ksh`) instead of auto-detecting from the shebang |
| `-e CODE` | Exclude a specific `SC####` check from the results |
| `-f FORMAT` | Output format (`tty` default, `json`, `checkstyle`, etc. — useful for CI integration) |
| `-x` | Follow `source`d files for more complete analysis |

## Examples

```
$ shellcheck deploy.sh
```
Analyze a single script, printing any findings to the terminal.

```
$ shellcheck *.sh
```
Analyze every script in the current directory.

```
$ shellcheck -e SC2086 deploy.sh
```
Analyze while excluding a specific check (use sparingly — excluding checks hides real issues).

```
$ shellcheck -f json deploy.sh > results.json
```
Output findings as JSON, commonly used to feed results into a CI pipeline step.

## Expected Output

```
$ shellcheck deploy.sh

In deploy.sh line 4:
cp $file /backup/
   ^-- SC2086 (info): Double quote to prevent globbing and word splitting.

For more information:
  https://www.shellcheck.net/wiki/SC2086 -- Double quote to prevent globbing...
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | No issues found |
| `1` | One or more issues found |
| `2`+ | A `shellcheck` internal error (e.g. file not found) |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `shellcheck: command not found` | Not installed | Install via the distro's package manager ([Module 14](../docs/14-package-managers/index.md)) |
| Wrong dialect's rules applied | Script lacks a shebang, or uses a shebang `shellcheck` can't map automatically | Pass `-s bash` (or the correct shell) explicitly |
| Findings feel excessive on a legacy script | A script with many pre-existing issues surfaces many findings at once | Address the highest-severity ones first, rather than excluding checks wholesale |

## Security Considerations

Several `shellcheck` findings (especially unquoted variables, `SC2086`) directly prevent
injection-style bugs where an unexpectedly-formed variable value is word-split or glob-expanded
into unintended arguments — running it is a real, low-cost security practice, not just a style
tool.

## Performance Considerations

Negligible — analysis of a typical script completes in a fraction of a second; running it in CI
adds no meaningful pipeline time.

## Production Usage

Most teams run `shellcheck` automatically in CI (or as a pre-commit hook) against every shell
script in a repository, treating a clean `shellcheck` pass as a merge requirement rather than an
optional manual check.

## Related Commands

- [`set`](set.md) — runtime error-handling (`-e`, `-u`, `pipefail`) that complements
  `shellcheck`'s static, pre-execution analysis

## Related Concepts

- [Debugging Scripts: set -x, shellcheck](../docs/18-shell-scripting/debugging-scripts-set-x-shellcheck.md)
- [Real Automation Scripts](../docs/18-shell-scripting/real-automation-scripts.md)
