---
title: "set — Configure Shell/Script Behavior Options"
description: "Toggle shell options that change how a script behaves, especially the error-handling and debugging trio: -e, -u, -x, and -o pipefail."
relatedConcepts: ["18-shell-scripting/exit-codes-and-error-handling", "18-shell-scripting/debugging-scripts-set-x-shellcheck"]
relatedCommands: ["shellcheck"]
careerRelevance: ["devops", "sre", "linux-administrator", "software-engineering"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["set -e bash", "set -u bash", "set -x bash", "set -o pipefail", "bash strict mode"]
canonicalUrl: "/commands/set"
---

# set

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Software Engineering

> **TL;DR:** `set -euo pipefail` at the top of a script is the common "strict mode" combination:
> stop on any error (`-e`), treat unset variables as an error (`-u`), and catch failures anywhere
> in a pipeline (`pipefail`) — `-x` separately traces execution for debugging.

## Purpose

`set` is a shell builtin that toggles options controlling how the current shell or script behaves
— see
[Exit Codes and Error Handling](../docs/18-shell-scripting/exit-codes-and-error-handling.md) and
[Debugging Scripts](../docs/18-shell-scripting/debugging-scripts-set-x-shellcheck.md) for the full
concepts.

## Syntax

```
set [-OPTION | +OPTION | -o OPTION_NAME]
```

## Arguments

None — entirely option-driven; a plain `set` with no arguments lists all currently-set shell
variables and functions instead.

## Options

| Flag | Meaning |
|---|---|
| `-e` | Exit immediately if any command fails (returns non-zero), except inside conditions being tested |
| `-u` | Treat any reference to an unset variable as an error, instead of silently substituting an empty string |
| `-x` | Print each command to stderr as it executes, with variables already expanded |
| `-o pipefail` | Make a pipeline's exit status reflect the last command that actually failed, not just the final command in the pipe |
| `+OPTION` | Disable an option previously enabled (e.g. `set +x` turns off tracing) |

## Examples

```
$ set -e
```
Enable exit-on-error for the rest of the current script/shell.

```
$ set -x
```
Begin tracing every command as it executes.

```
$ set +x
```
Stop tracing.

```bash
#!/bin/bash
set -euo pipefail
```
The common "strict mode" opener at the top of a production script — stop on error, catch unset
variables, and catch pipeline failures.

```
$ bash -x script.sh
```
Trace an entire script's execution without modifying its source, by passing `-x` to `bash`
directly instead of adding `set -x` inside the file.

## Expected Output

```
$ set -x
$ name="prod"
+ name=prod
$ echo "Deploying to $name"
+ echo 'Deploying to prod'
Deploying to prod
```

## Exit Status

`set` itself returns `0` on success; a non-zero status typically indicates an invalid option name
was given.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Script exits unexpectedly partway through | `set -e` is enabled and a command failed | Expected behavior — check the failing command's exit status; use `if` to explicitly allow an expected failure |
| `unbound variable` error | `set -u` is enabled and a variable was referenced before being assigned | Assign a default (`${var:-default}`) or ensure the variable is set before use |
| Pipeline failure not caught despite set -e | `set -e` alone doesn't inspect every stage of a pipeline | Add `set -o pipefail` alongside `-e` |

## Security Considerations

`set -u` catching unset variables can prevent security-relevant mistakes, such as a script
silently operating on an empty path (e.g. `rm -rf "$dir"/*` when `$dir` was never set) — an empty,
unquoted `$dir` can expand dangerously. Strict mode is a real safety measure, not just a style
preference.

## Performance Considerations

Negligible overhead — `set -x` tracing adds output volume but not meaningful execution time; these
are behavior toggles, not performance-tuning options.

## Compatibility Notes

`set -o pipefail` is a Bash (and some other shells') extension, not POSIX `sh` — scripts intended
to run under strict POSIX `sh` cannot rely on it.

## Production Usage

`set -euo pipefail` is the near-universal opening line of production Bash scripts across most
DevOps/SRE teams — treated as a baseline safety net rather than an optional extra, alongside
running [`shellcheck`](shellcheck.md) before deployment.

## Related Commands

- [`shellcheck`](shellcheck.md) — static analysis that catches many of the same mistake classes
  `set -u`/`-e` guard against at runtime

## Related Concepts

- [Exit Codes and Error Handling](../docs/18-shell-scripting/exit-codes-and-error-handling.md)
- [Debugging Scripts: set -x, shellcheck](../docs/18-shell-scripting/debugging-scripts-set-x-shellcheck.md)
