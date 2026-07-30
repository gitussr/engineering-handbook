---
title: "at — Schedule a One-Time Command"
description: "Queue a command to run once at a specified future time, and manage the pending queue with atq and atrm."
relatedConcepts: ["17-cron/at-and-anacron", "17-cron/task-scheduling-overview"]
relatedCommands: ["crontab", "anacron"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["at command linux", "atq", "atrm", "schedule one time job"]
canonicalUrl: "/commands/at"
---

# at

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `echo "command" | at TIME` queues a command to run once at that time. `atq` lists
> pending jobs, `atrm JOBNUMBER` cancels one.

## Purpose

`at` schedules a command for one-time future execution — see
[at and anacron](../docs/17-cron/at-and-anacron.md) for the full concept.

## Syntax

```
at TIME
atq
atrm JOBNUMBER
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `TIME` | When to run — accepts forms like `14:00`, `now + 30 minutes`, `09:00 tomorrow`, `noon` | Yes |
| `JOBNUMBER` | The queued job's ID, from `atq` output, used with `atrm` | Yes (for `atrm`) |

## Options

| Flag | Meaning |
|---|---|
| `-f FILE` | Read the command to run from a file instead of stdin |
| `-l` | Equivalent to `atq` — list pending jobs |
| `-r JOBNUMBER` | Equivalent to `atrm` — remove a pending job |
| `-c JOBNUMBER` | Print the exact command a pending job will run |

## Examples

```
$ echo "/usr/local/bin/cleanup.sh" | at 14:00
```
Queue a script to run once, today at 14:00 (or tomorrow at 14:00 if that time has already passed).

```
$ at now + 30 minutes
at> /usr/local/bin/notify.sh
at> <EOT>
```
Interactively queue a command 30 minutes from now (typed at the `at>` prompt, ended with Ctrl-D).

```
$ atq
```
List all pending jobs for the current user.

```
$ atrm 12
```
Cancel pending job number 12.

```
$ at -c 12
```
Print the full command job 12 will run, useful for confirming what's queued before it fires.

## Expected Output

```
$ echo "/usr/local/bin/cleanup.sh" | at 14:00
job 12 at Mon Jul 27 14:00:00 2026

$ atq
12    Mon Jul 27 14:00:00 2026 a deploy
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Job successfully queued (or listed/removed) |
| `1` | Error — invalid time specification, or `atd` service not running |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Garbled time` | Unparseable time expression | Use a supported form like `14:00`, `now + 1 hour`, `tomorrow` |
| `at: command not found` (rare) | The `at` package isn't installed | Install the `at` package for the distro |
| Job never runs at the scheduled time | The `atd` daemon isn't running | Check and start the `atd` service |

## Security Considerations

Like `crontab`, `at` access can be restricted per-user via `/etc/at.allow`/`/etc/at.deny` where
supported — relevant on shared systems where arbitrary future command execution is a real
privilege to control.

## Performance Considerations

Negligible — `at` simply queues a job for the `atd` daemon; the resource cost belongs entirely to
the job itself once it runs.

## Production Usage

`at` is commonly used for scheduling a delayed rollback or cleanup action tied to a specific
deployment window, then canceled via `atrm` if the deploy is confirmed healthy before the delayed
action would fire.

## Related Commands

- [`crontab`](crontab.md) — for recurring jobs, rather than `at`'s one-time execution
- [`anacron`](anacron.md) — recurring jobs on machines that aren't always powered on

## Related Concepts

- [at and anacron](../docs/17-cron/at-and-anacron.md)
- [Task Scheduling Overview](../docs/17-cron/task-scheduling-overview.md)
