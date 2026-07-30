---
title: "at and anacron"
description: "at for one-off scheduled jobs and anacron for recurring jobs on machines that aren't always powered on — the two tools that fill cron's gaps."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["17-cron/system-wide-cron"]
relatedTopics: ["task-scheduling-overview", "systemd-timers"]
relatedCommands: ["at", "anacron"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#at-vs-cron"]
relatedCheatsheet: "cron"
furtherReading: [{"label": "at(1) man page", "url": "https://man7.org/linux/man-pages/man1/at.1.html"}]
nextTopic: "17-cron/systemd-timers"
prevTopic: "17-cron/system-wide-cron"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["at command linux", "schedule one time job linux", "anacron vs cron", "anacrontab"]
canonicalUrl: "/docs/cron/at-and-anacron"
---

# at and anacron

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `at` schedules a command to run once at a specific future time; `anacron` runs
> recurring jobs that were missed because the machine was powered off, catching them up the next
> time it's on. Neither replaces `cron` — they cover cases `cron` handles poorly.

## What is it?

Two tools that complement `cron`: `at` for single, one-time future execution, and `anacron` for
recurring jobs on machines that aren't guaranteed to be powered on at the scheduled time.

## Why does it exist?

`cron` assumes two things that aren't always true: that a job should recur indefinitely, and that
the machine will be powered on at the exact scheduled moment. `at` handles jobs that genuinely
should run only once. `anacron` handles machines — laptops, desktops, dev boxes — that are
routinely off at the scheduled time, where a missed `cron` run would otherwise just never happen.

## Where is it used?

- `at`: scheduling a one-off maintenance window action, a delayed rollback, or a reminder task
  that shouldn't recur.
- `anacron`: system maintenance tasks on laptops and desktops (many distros run
  `/etc/cron.daily/` through `anacron` rather than pure `cron` for exactly this reason) — ensuring
  daily/weekly/monthly jobs still happen even if the machine was off at the exact scheduled hour.

## How it works

> 📊 Diagram: two timelines. Top timeline: `at` — a single point marked "job scheduled for 14:00"
> with one execution mark at 14:00, nothing before or after. Bottom timeline: `anacron` — a
> machine powered off during a scheduled daily slot, then powered on hours later, with an
> execution mark appearing shortly after power-on instead of at the missed original time.

**`at`** queues a one-time job:

```
$ echo "/usr/local/bin/cleanup.sh" | at 14:00
$ at now + 30 minutes
$ at 09:00 tomorrow
```

The job runs once, at (or shortly after) the specified time, then is removed from the queue.
Pending jobs can be listed with `atq` and removed with `atrm`.

**`anacron`** doesn't use time-of-day scheduling at all — it works in day-granularity intervals,
configured in `/etc/anacrontab`:

```
# period  delay  job-identifier  command
1         5      cron.daily      run-parts /etc/cron.daily
7         10     cron.weekly     run-parts /etc/cron.weekly
```

Each line means "run this job every N days, and if the machine was off when it was due, run it
`delay` minutes after the next boot instead of skipping it." `anacron` tracks the last-run
timestamp per job so it knows what was missed.

## Real-world example

A developer's laptop is asleep every night at 2 AM, the time a `cron`-scheduled backup was set
for — so the backup silently never runs, week after week, because `cron` only fires at the exact
scheduled moment and does nothing if the machine is off. Switching that job to `anacron` (or
relying on the distro's existing `anacron`-driven `/etc/cron.daily/`) means it instead runs a few
minutes after the laptop next boots, whenever that is — catching up the missed run instead of
losing it entirely.

## Syntax

```
at TIME
anacron [OPTIONS]
```

## Commands

See [`at`](../../commands/at.md) and [`anacron`](../../commands/anacron.md) for full references.

## Production example

```
$ echo "/usr/local/bin/deploy-rollback.sh" | at now + 1 hour
job 12 at Mon Jul 27 15:30:00 2026

$ atq
12    Mon Jul 27 15:30:00 2026 a deploy

$ atrm 12
```

Scheduling a one-off rollback an hour out, confirming it's queued, then canceling it once the
deploy is confirmed healthy — exactly the kind of single-shot, cancelable job `cron` isn't built
for.

## Do / Don't

| Do | Don't |
|---|---|
| Use `at` for genuinely one-off future actions | Use `cron` with a plan to manually remove the job after it runs once |
| Use `anacron` (or a distro's existing `anacron`-driven daily/weekly/monthly setup) on intermittently-on machines | Assume a `cron` job "ran eventually" if the machine was off at the scheduled time — it simply didn't run |
| Check `atq` before assuming a queued `at` job is still pending | Forget a queued `at` job exists and lose track of pending one-off actions |

## Common mistakes

- Using `cron` for a one-time job and forgetting to remove the crontab entry afterward, causing
  it to fire again on the next matching schedule.
- Assuming a `cron`-scheduled job on a laptop "just didn't happen" without realizing `anacron`
  exists specifically to solve that problem.
- Losing track of pending `at` jobs because `atq` was never checked — an old queued job firing
  unexpectedly later.

## Best practices

- Reach for `at` explicitly when a task is genuinely single-execution — it self-documents that
  intent better than a `cron` job someone has to remember to delete.
- On any machine that isn't guaranteed to be always-on, prefer `anacron`-based scheduling (or the
  distro's existing `cron.daily`/`cron.weekly` via `anacron`) over raw `cron` for recurring
  maintenance.
- Periodically audit `atq` on shared systems — stale queued jobs are easy to lose track of.

## Exercises

1. Schedule a harmless one-off command with `at now + 2 minutes`, then check `atq` before it
   fires.
2. Explain in one sentence why a laptop should prefer `anacron` over `cron` for daily maintenance.
3. Read `/etc/anacrontab` on a system that has one and identify the period, delay, and job fields.

## Quiz

**Q: What happens to an `at` job after it runs?**
<details><summary>Show answer</summary>
It's removed from the queue automatically — `at` jobs are strictly one-time, unlike `cron` jobs.
</details>

**Q: Why does anacron use day-granularity periods instead of exact times?**
<details><summary>Show answer</summary>
Because it's designed for machines that aren't reliably powered on at any exact time — it tracks
whether a job is overdue by day-count and catches it up after the next boot, rather than trying to
hit a precise clock time.
</details>

**Q: How do you cancel a pending `at` job?**
<details><summary>Show answer</summary>
List pending jobs with `atq` to find the job number, then remove it with `atrm JOBNUMBER`.
</details>

## Interview questions

- When would you use `at` instead of `cron`, and why? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `at` schedules a single one-time job; it's removed from the queue automatically after running.
- `anacron` runs recurring jobs in day-granularity periods, catching up missed runs after the
  machine is next on — built for intermittently-powered machines.
- Neither tool replaces `cron` — they cover cases (one-off jobs, unreliable uptime) `cron` handles
  poorly.
- `atq`/`atrm` manage pending `at` jobs; `/etc/anacrontab` configures `anacron`'s periods.

## Related topics

- [Task Scheduling Overview](task-scheduling-overview.md)
- [systemd Timers](systemd-timers.md)
