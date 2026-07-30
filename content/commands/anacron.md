---
title: "anacron — Run Missed Recurring Jobs After Downtime"
description: "Run day-granularity recurring jobs configured in /etc/anacrontab, catching up runs that were missed while the machine was powered off."
relatedConcepts: ["17-cron/at-and-anacron"]
relatedCommands: ["crontab", "at"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["anacron command", "anacrontab", "anacron vs cron", "run missed cron jobs"]
canonicalUrl: "/commands/anacron"
---

# anacron

🟡 Good to Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `anacron` runs jobs configured in `/etc/anacrontab` on day-granularity periods,
> catching up any run that was missed because the machine was off — most distros already use it
> to drive `/etc/cron.daily`/`weekly`/`monthly`.

## Purpose

`anacron` runs recurring jobs that tolerate imprecise timing, catching up missed runs after
downtime — see [at and anacron](../docs/17-cron/at-and-anacron.md) for the full concept.

## Syntax

```
anacron [OPTIONS] [JOB...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `JOB` | Run only the specified job identifier(s) from `/etc/anacrontab`, instead of all of them | No |

## Options

| Flag | Meaning |
|---|---|
| `-f` | Force jobs to run even if they aren't currently due |
| `-n` | Run jobs immediately without the configured random/fixed delay |
| `-d` | Run in the foreground (don't fork to background), useful for testing |
| `-t FILE` | Use a specific anacrontab file instead of the default |
| `-u` | Update job timestamps without actually running the jobs |

## Examples

```
$ cat /etc/anacrontab
```
View the configured periods, delays, and jobs (typically edited directly as root, not passed as
command-line arguments day to day).

```
$ sudo anacron -f -d cron.daily
```
Force the `cron.daily` job to run immediately, in the foreground, ignoring whether it's currently
due — useful for testing a new entry.

```
$ sudo anacron -n
```
Run all due jobs immediately, skipping the normal startup delay — used mainly for testing.

## Expected Output

```
$ sudo anacron -f -d cron.daily
Anacron started on 2026-07-27
Will run job `cron.daily' in 5 min.
Jobs will be executed sequentially
Job `cron.daily' started
Job `cron.daily' terminated
Normal exit (0 jobs run)
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | All due jobs ran successfully |
| Non-zero | One or more jobs failed, or the anacrontab couldn't be read |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Job never seems to run | `anacron` itself isn't triggered — it typically relies on being invoked at boot or via a system timer/cron entry, not running continuously as a daemon | Confirm the distro's mechanism for invoking `anacron` (often via `/etc/cron.d/anacron` or a systemd timer) is active |
| `Can't find anacrontab` | Missing or misplaced `/etc/anacrontab` | Confirm the file exists at the expected path for the distro |

## Security Considerations

Same class of consideration as `cron`/`crontab` — jobs in `/etc/anacrontab` typically run as root
by default unless configured otherwise, so entries should be reviewed with the same care as any
other root-run scheduled job.

## Performance Considerations

`anacron` deliberately staggers job start times (a configurable delay) to avoid every job firing
at the exact same moment after boot, which matters on machines with many queued jobs recovering
from extended downtime.

## Production Usage

Most distros already use `anacron` under the hood to drive `/etc/cron.daily`, `cron.weekly`, and
`cron.monthly` — engineers rarely write raw `/etc/anacrontab` entries themselves and instead just
rely on this existing mechanism, dropping scripts into those directories.

## Related Commands

- [`crontab`](crontab.md) — for jobs with precise time-of-day requirements, rather than
  `anacron`'s day-granularity, catch-up-tolerant model
- [`at`](at.md) — one-time jobs, rather than `anacron`'s recurring model

## Related Concepts

- [at and anacron](../docs/17-cron/at-and-anacron.md)
