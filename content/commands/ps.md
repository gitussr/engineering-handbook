---
title: "ps — Report a Snapshot of Current Processes"
description: "List running processes with either BSD-style (aux) or UNIX-style (-ef) syntax, and filter/search the output."
relatedConcepts: ["10-processes/ps-top-htop", "10-processes/pid-ppid-and-process-tree"]
relatedCommands: ["top", "kill", "grep"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: [{"context": "GNU ps (Linux)", "note": "Supports both BSD-style (ps aux) and UNIX-style (ps -ef) flags. BSD/macOS ps supports a different, more limited flag set."}]
updatedAt: "2026-07-25"
keywords: ["ps aux", "ps -ef", "ps command linux", "find process by name"]
canonicalUrl: "/commands/ps"
---

# ps

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `ps aux` lists every process, BSD-style. `ps -ef` does the same, UNIX-style, with
> PPID more prominent. `ps aux | grep name` is the standard way to find a specific process.

## Purpose

`ps` prints a snapshot of currently running processes — see
[ps, top, htop](../docs/10-processes/ps-top-htop.md) for when to reach for it over `top`.

## Syntax

```
ps [OPTIONS]
```

## Arguments

None — `ps` is entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `aux` | All processes, BSD-style, user-oriented columns (no dash, by convention) |
| `-ef` | All processes, UNIX-style, PPID prominent |
| `-p PID` | Show only a specific PID |
| `--sort=-%cpu` | Sort by CPU usage, descending |

## Examples

```
$ ps aux
```
List every process, BSD-style.

```
$ ps -ef
```
List every process, UNIX-style.

```
$ ps aux | grep nginx
```
Find a specific process by name.

```
$ ps -p 1234
```
Show just one specific process by PID.

## Expected Output

```
$ ps aux | grep nginx
root      1234  0.0  0.1  55432  8192 ?        Ss   09:00   0:00 nginx: master process
```

Columns (BSD style): user, PID, %CPU, %MEM, VSZ, RSS, TTY, STAT (state, see
[Process Lifecycle and States](../docs/10-processes/process-lifecycle-and-states.md)), START,
TIME, COMMAND.

## Exit Status

`0` on success, non-zero on invalid options.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Empty grep result for a process you know is running | Process name doesn't match the grep pattern exactly, or the grep command itself shows up as a match | Adjust the pattern, or use `pgrep` for cleaner name-based matching |
| Confusing column layout | Mixed BSD-style and UNIX-style flag conventions | Stick to one style (`aux` or `-ef`) consistently |

## Security Considerations

`ps aux`/`ps -ef` can reveal command-line arguments of other users' processes (including,
historically, passwords passed as arguments) — a real reason to avoid passing secrets as
command-line arguments rather than environment variables or files.

## Performance Considerations

Negligible — `ps` reads already-available kernel process data (via `/proc`, Module 05), it
doesn't scan the system.

## Production Usage

`ps aux | grep processname` remains one of the most-typed command combinations in daily
operations work, despite `pgrep` existing as a more purpose-built alternative — worth knowing
both.

## Related Commands

- [`top`](top.md) — live view instead of a snapshot
- [`kill`](kill.md) — act on a PID found via `ps`

## Related Concepts

- [ps, top, htop](../docs/10-processes/ps-top-htop.md)
- [PID, PPID, and the Process Tree](../docs/10-processes/pid-ppid-and-process-tree.md)
