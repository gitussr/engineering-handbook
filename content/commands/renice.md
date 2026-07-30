---
title: "renice — Change Priority of a Running Process"
description: "Adjust the niceness of a process that's already running, without restarting it."
relatedConcepts: ["10-processes/nice-and-renice"]
relatedCommands: ["nice"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "good-to-know"
updatedAt: "2026-07-25"
keywords: ["renice command linux", "change process priority linux", "renice -p"]
canonicalUrl: "/commands/renice"
---

# renice

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `renice -n VALUE -p PID` changes an already-running process's priority without
> restarting it — the tool for "this is already running and hogging CPU, deprioritize it now."

## Purpose

`renice` changes the niceness of a process that's already running — see
[nice and renice](../docs/10-processes/nice-and-renice.md) for the full niceness scale.

## Syntax

```
renice -n VALUE -p PID
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `VALUE` | New niceness value | Yes |
| `PID` | The process to change | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-p PID` | Target a specific process ID |
| `-u USER` | Target all processes owned by a user |
| `-g PGRP` | Target a process group |

## Examples

```
$ renice -n 15 -p 24831
```
Lower the priority of an already-running process.

```
$ sudo renice -n -5 -p 24831
```
Raise priority (requires root for negative values).

## Expected Output

```
$ renice -n 15 -p 24831
24831 (process ID) old priority 0, new priority 15
```

## Exit Status

`0` on success, non-zero if the PID doesn't exist or the operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `renice: failed to set priority for 24831: Permission denied` | Attempted to raise priority (negative value) without root | Use `sudo`, or apply a positive value instead |
| `renice: 24831: no such process` | The PID doesn't exist (may have already exited) | Verify with `ps -p PID` first |

## Security Considerations

Same boundary as `nice`: only root can decrease niceness (raise priority) below 0, preventing
users from freely outcompeting each other's processes for CPU.

## Performance Considerations

The direct purpose of this command — see
[nice and renice](../docs/10-processes/nice-and-renice.md).

## Production Usage

`renice` is the fast fix once `top`/`htop` (Module 10's earlier topic) reveals an already-running
process consuming disproportionate CPU but not urgent enough to kill — deprioritize it in place
rather than interrupting it.

## Related Commands

- [`nice`](nice.md) — set priority at launch time instead of after the fact

## Related Concepts

- [nice and renice](../docs/10-processes/nice-and-renice.md)
