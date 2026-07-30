---
title: "top — Display Live Process Activity"
description: "A continuously refreshing view of running processes, CPU, and memory usage, with interactive sort and kill shortcuts."
relatedConcepts: ["10-processes/ps-top-htop", "20-monitoring/top-htop-vmstat-iostat"]
relatedCommands: ["ps", "kill"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-28"
keywords: ["top command linux", "top sort by cpu", "top sort by memory", "top interactive commands", "top batch mode script", "top -b -n 1"]
canonicalUrl: "/commands/top"
---

# top

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `top` shows a live, auto-refreshing process list. Press `P` to sort by CPU, `M` for
> memory, `k` to kill a process by PID, `q` to quit.

## Purpose

`top` displays a continuously updating view of running processes and system resource usage — see
[ps, top, htop](../docs/10-processes/ps-top-htop.md) for when to reach for it over `ps`.

## Syntax

```
top [OPTIONS]
```

## Arguments

None.

## Options

| Flag | Meaning |
|---|---|
| `-p PID` | Monitor only a specific PID |
| `-u USER` | Show only a specific user's processes |
| `-d SECONDS` | Set the refresh interval |
| `-b` | Batch mode — non-interactive output, suitable for redirecting to a file or piping to another command |
| `-n COUNT` | Number of iterations before exiting (commonly paired with `-b`) |

## Interactive keys (while running)

| Key | Effect |
|---|---|
| `P` | Sort by CPU usage |
| `M` | Sort by memory usage |
| `k` | Kill a process (prompts for PID and signal) |
| `q` | Quit |
| `1` | Toggle per-core CPU breakdown |

## Examples

```
$ top
```
Start the live view with default settings.

```
$ top -u deploy
```
Show only processes owned by a specific user.

```
$ top -b -n 1 | head -15
```
Run non-interactively for a single iteration and print the result — useful inside a
[shell script](../docs/18-shell-scripting/real-automation-scripts.md) or a scheduled
[monitoring](../docs/20-monitoring/top-htop-vmstat-iostat.md) check, where an interactive,
continuously-refreshing display isn't usable.

## Expected Output

An interactive, full-screen, continuously refreshing display — not meaningfully representable as
a static snippet; header shows system-wide load average, CPU/memory summary, followed by a
sortable process list.

## Exit Status

`0` on normal quit (`q`).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Display looks garbled in some terminals | Terminal/`TERM` compatibility issue (see [Module 03](../docs/03-terminal/terminal-vs-shell-vs-console.md)) | Try a different terminal emulator or fix the `TERM` variable |
| Can't tell what's actually using the most CPU | Not sorted | Press `P` to sort by CPU immediately |

## Security Considerations

Killing a process via `top`'s `k` shortcut has the same consequences as `kill` directly (Module
10's next topic) — double-check the PID before confirming.

## Performance Considerations

`top` itself has negligible overhead; a very short refresh interval (`-d`) on a very busy system
can add slight, usually negligible, extra load.

## Production Usage

`top` (or `htop`) is typically the first tool an engineer opens when a server "feels slow," before
reaching for any more specialized monitoring tool — a fast, always-available first look.

## Related Commands

- [`ps`](ps.md) — one-time snapshot instead of a live view
- [`kill`](kill.md) — the underlying mechanism behind `top`'s `k` shortcut

## Related Concepts

- [ps, top, htop](../docs/10-processes/ps-top-htop.md)
- [top, htop, vmstat, iostat](../docs/20-monitoring/top-htop-vmstat-iostat.md)
