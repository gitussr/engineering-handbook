---
title: "killall — Signal Processes by Exact Name"
description: "Send a signal to every process matching an exact name, instead of specifying PIDs individually."
relatedConcepts: ["10-processes/kill-killall-pkill-and-signals"]
relatedCommands: ["kill", "pkill"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["killall command linux", "killall vs kill", "killall by name"]
canonicalUrl: "/commands/killall"
---

# killall

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `killall processname` signals every process matching that exact name — useful when
> several instances of the same program are running and all need to go.

## Purpose

`killall` sends a signal to every process matching an exact name, rather than requiring a
specific PID — see
[kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md).

## Syntax

```
killall [-SIGNAL] NAME...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `NAME` | Exact process name to match | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-SIGNAL` | Signal to send (defaults to SIGTERM) |
| `-i` | Interactive — confirm before each kill |
| `-u USER` | Only match processes owned by a specific user |

## Examples

```
$ killall nginx
```
Send SIGTERM to every process named exactly `nginx`.

```
$ killall -9 stuck-process
```
Force-kill every matching process.

```
$ killall -i node
```
Confirm before killing each matching process — safer when unsure how many will match.

## Expected Output

`killall` produces no output on success by default; `-i` prompts per match.

## Exit Status

`0` if at least one process was signaled, `1` if no processes matched the name.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `killall: nginx: no process found` | No process with that exact name is running | Verify the exact name with `ps aux \| grep` |
| More processes killed than expected | Multiple unrelated processes happen to share the exact name | Use `-u` to scope by owner, or use `kill` with specific PIDs instead |

## Security Considerations

`killall` affects every matching process system-wide by default (not scoped to your own
processes unless you lack permission for others) — use `-u` to scope intentionally on
shared/multi-tenant systems.

## Performance Considerations

Not applicable.

## Production Usage

`killall -HUP nginx` is a common pattern for triggering a graceful config reload across every
Nginx worker process at once, without needing to enumerate individual PIDs.

## Related Commands

- [`kill`](kill.md) — signal a specific PID
- [`pkill`](pkill.md) — signal by name pattern instead of exact name

## Related Concepts

- [kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md)
