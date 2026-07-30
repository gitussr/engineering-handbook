---
title: "nice — Run a Command with a Modified Priority"
description: "Launch a new process with a specified niceness value, from -20 (highest priority) to 19 (lowest)."
relatedConcepts: ["10-processes/nice-and-renice"]
relatedCommands: ["renice"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "good-to-know"
updatedAt: "2026-07-25"
keywords: ["nice command linux", "nice -n", "run process low priority linux"]
canonicalUrl: "/commands/nice"
---

# nice

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `nice -n 15 command` starts a command with lower priority (higher nice value) so it
> doesn't compete as aggressively for CPU. Lower numbers mean higher priority.

## Purpose

`nice` launches a new process with a specified niceness (CPU priority) value — see
[nice and renice](../docs/10-processes/nice-and-renice.md).

## Syntax

```
nice [-n VALUE] COMMAND [ARGS...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The command to run with modified priority | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-n VALUE` | Niceness value, -20 (highest priority) to 19 (lowest); defaults to 10 if `-n` is omitted but `nice` is used |

## Examples

```
$ nice -n 15 ./backup.sh
```
Run a script at low priority.

```
$ nice -n -5 ./urgent-task.sh
```
Run at higher-than-default priority (requires appropriate privileges for negative values).

## Expected Output

`nice` produces no output of its own — the wrapped command's normal output follows.

## Exit Status

Matches the wrapped command's own exit status.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `nice: cannot set niceness: Permission denied` | Attempted a negative value without sufficient privileges | Use `sudo`, or use a positive (lower-priority) value instead |

## Security Considerations

None specific — priority adjustment doesn't grant or remove access, only scheduling preference.

## Performance Considerations

This command exists specifically for performance/scheduling tuning — see
[nice and renice](../docs/10-processes/nice-and-renice.md) for when to use it.

## Production Usage

Batch jobs, backups, and other non-latency-sensitive scheduled work are commonly wrapped in
`nice -n [positive value]` as a standard, low-effort way to protect production services sharing
the same host from CPU contention.

## Related Commands

- [`renice`](renice.md) — change priority for an already-running process

## Related Concepts

- [nice and renice](../docs/10-processes/nice-and-renice.md)
