---
title: "which — Locate a Command's Executable"
description: "Show the full path of the executable that would run for a given command name — the standard way to debug 'wrong version is running' problems caused by PATH ordering."
relatedConcepts: ["04-bash/path-variable"]
relatedCommands: []
careerRelevance: ["linux-administrator", "devops", "software-engineering"]
difficulty: "must-know"
compatibility: [{"context": "GNU/Linux", "note": "which is a separate small utility, not a shell builtin; command -v is a POSIX-portable alternative available in every shell without depending on which being installed"}]
updatedAt: "2026-07-30"
keywords: ["which command linux", "which vs command -v", "find executable path linux", "which command not found"]
canonicalUrl: "/commands/which"
---

# which

🟢 Must Know · Relevant for: Linux Administrator · DevOps · Software Engineering

> **TL;DR:** `which {command}` prints the full path of the executable your shell would actually
> run for that name — the fastest way to confirm which of several same-named programs on `PATH`
> is the one that's running.

## Purpose

`which` searches `PATH`, in order, and prints the full path of the first matching executable it
finds for a given command name — resolving exactly what a bare command name actually runs,
which matters whenever more than one version of a program might be installed. See
[The PATH Variable](../docs/04-bash/path-variable.md) for why `PATH` order determines this in the
first place.

## Syntax

```
which [OPTIONS] COMMAND...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | One or more command names to resolve | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-a` | Show every matching executable on `PATH`, not just the first one that would run |

## Examples

```
$ which python3
```
Show the full path of the `python3` that would actually run.

```
$ which -a python3
```
Show every `python3` found on `PATH`, in search order — useful when multiple installations
(system package, pyenv, a virtual environment) might be competing.

```
$ command -v bash
```
The POSIX-portable equivalent, built into the shell itself rather than depending on a separate
`which` binary being installed.

## Expected Output

```
$ which -a python3
/home/user/.pyenv/shims/python3
/usr/bin/python3
```

The first line is the one that actually runs — pyenv's shim is earlier on `PATH` than the system
Python in this example.

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Found — the command exists somewhere on `PATH` |
| `1` | Not found — no executable by that name exists on `PATH` |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `which: no {command} in ({PATH list})` | The command genuinely isn't installed, or isn't on `PATH` | Confirm it's installed; if it is, check whether its install location is actually on `PATH` |
| `which` shows a path, but running the command behaves differently than expected | A shell alias or function with the same name intercepts it before `PATH` lookup even happens | `which` only searches `PATH` — it won't reveal an alias/function; use `type {command}` instead to see the full resolution including aliases |
| `which` command itself not found | Minimal system image without the `which` package installed | Use the POSIX-builtin `command -v {name}` instead, which works in every shell with no extra install |

## Security Considerations

`which`'s output depends entirely on the current user's `PATH` — a `PATH` that includes a
world-writable or unexpected directory ahead of standard system directories is a real privilege-
escalation and command-hijacking risk (a malicious binary shadowing a real one). Auditing `PATH`
order, not just trusting `which`'s answer, matters on any security-sensitive system.

## Performance Considerations

Effectively instantaneous — searching `PATH` for a single filename is a fast filesystem lookup with
no meaningful performance cost even with a long `PATH`.

## Compatibility Notes

`which` is a separate small utility program, not built into the shell itself, and isn't guaranteed
to be installed on every minimal system. `command -v` is a POSIX shell builtin that does the
equivalent lookup and is always available without depending on an external `which` binary — the
more portable choice inside scripts.

## Production Usage

`which -a` is the standard first step when "the wrong version is running" — multiple installed
versions of a language runtime, tool, or library competing on `PATH`, with the wrong one winning
due to directory order. In scripts, `command -v` is generally preferred over `which` specifically
because it doesn't depend on an external binary being present on every target system.

## Related Commands

No closely related canonical command page — see
[The PATH Variable](../docs/04-bash/path-variable.md) for the underlying mechanism `which` reports on.

## Related Concepts

- [The PATH Variable](../docs/04-bash/path-variable.md)
