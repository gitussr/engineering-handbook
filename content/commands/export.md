---
title: "export — Mark a Variable for Export to Child Processes"
description: "Export a shell variable so it becomes an environment variable inherited by child processes."
relatedConcepts: ["04-bash/environment-variables"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["export command linux", "export bash variable", "environment variable export"]
canonicalUrl: "/commands/export"
---

# export

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `export VAR=value` turns a plain shell variable into an environment variable that
> child processes (scripts, programs) can see. Without `export`, the variable stays local to your
> current shell.

## Purpose

`export` marks a shell variable so it's passed into the environment of any child process started
from that shell — the mechanism behind the shell-variable-vs-environment-variable distinction
covered in [Environment Variables](../docs/04-bash/environment-variables.md).

## Syntax

```
export VAR=value
export VAR
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `VAR=value` | Set and export a variable in one step | No (can export an already-set variable) |
| `VAR` | Export a variable that already has a value set | No |

## Options

`export` is a shell built-in with no options in common use for this documentation's purposes.

## Examples

```
$ export MY_VAR=hello
```
Set and export in one step.

```
$ MY_VAR=hello
$ export MY_VAR
```
Set first, export separately — equivalent result.

```
$ export -p
```
List all currently exported variables (`-p` = print).

## Expected Output

`export` produces no output on success. `export -p` lists exported variables:

```
$ export -p
declare -x HOME="/home/deploy"
declare -x PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin"
declare -x MY_VAR="hello"
```

## Exit Status

`0` on success. Non-zero generally only occurs with invalid syntax.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| A child process/script can't see a variable | Forgot `export` — the variable is a local shell variable only | Add `export` before the variable name |
| Variable disappears in a new terminal session | `export` only affects the current shell session; it's not persisted | Add the export line to a shell config file (see Shell Config Files) |

## Security Considerations

Exported variables are visible to every child process, including third-party tools and scripts —
avoid exporting sensitive values you don't want a called program's logging or error output to
potentially expose.

## Performance Considerations

Negligible.

## Production Usage

CI/CD pipelines and container orchestration inject secrets and configuration as exported
environment variables specifically because `export` is the universal mechanism every program,
in every language, knows how to read.

## Related Commands

None documented yet — `env` and `printenv` (for inspecting the full environment) are forward
references pending their own canonical pages.

## Related Concepts

- [Environment Variables](../docs/04-bash/environment-variables.md)
