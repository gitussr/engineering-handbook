---
title: "pkill — Signal Processes by Name Pattern"
description: "Send a signal to every process whose name or command line matches a pattern, more flexible than killall's exact-name matching."
relatedConcepts: ["10-processes/kill-killall-pkill-and-signals"]
relatedCommands: ["kill", "killall"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["pkill command linux", "pkill vs killall", "pkill pattern matching"]
canonicalUrl: "/commands/pkill"
---

# pkill

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `pkill pattern` signals every process whose name matches a pattern — more flexible
> than `killall`'s exact-name matching, but that flexibility means it's also easier to
> accidentally match more than intended.

## Purpose

`pkill` sends a signal to processes matching a name pattern — see
[kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md) for
how it compares to `kill` and `killall`.

## Syntax

```
pkill [OPTIONS] PATTERN
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATTERN` | Pattern to match against process names | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-SIGNAL` | Signal to send (defaults to SIGTERM) |
| `-f` | Match against the full command line, not just the process name |
| `-u USER` | Only match processes owned by a specific user |
| `-l` | List (with `pgrep`, the read-only counterpart) matched process names alongside PIDs |

## Examples

```
$ pkill node
```
Signal every process whose name matches "node".

```
$ pkill -f "worker.py --queue=high"
```
Match against the full command line for more precise targeting.

```
$ pkill -u deploy python
```
Signal only a specific user's matching processes.

## Expected Output

`pkill` produces no output on success.

## Exit Status

`0` if at least one process matched and was signaled, `1` if none matched.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Unintended processes were signaled | Pattern matched more broadly than intended | Test first with `pgrep` (read-only equivalent) before running `pkill` |
| `pkill: killing pid ... failed: Operation not permitted` | Attempted to signal a process you don't own | Use `sudo` if appropriate, or scope with `-u` |

## Security Considerations

`pkill`'s pattern matching is more permissive than `killall`'s exact-name matching — always test
the pattern with `pgrep` first (same matching logic, but lists instead of kills) to confirm scope
before actually signaling anything.

## Performance Considerations

Not applicable.

## Production Usage

`pgrep pattern` followed by `pkill` with the same pattern (once the match is confirmed safe) is a
standard, safer two-step habit for anything beyond a trivially exact, well-known process name.

## Related Commands

- [`kill`](kill.md) — signal a specific PID
- [`killall`](killall.md) — signal by exact name

## Related Concepts

- [kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md)
