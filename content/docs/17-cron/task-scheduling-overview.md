---
title: "Task Scheduling Overview"
description: "Why Linux systems need automated task scheduling, and the landscape of tools — cron, at, anacron, and systemd timers — that this module covers."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/reading-logs-during-a-production-incident"]
relatedTopics: ["crontab-syntax", "at-and-anacron", "systemd-timers"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#why-task-scheduling"]
relatedCheatsheet: "cron"
furtherReading: []
nextTopic: "17-cron/crontab-syntax"
prevTopic: "16-logs/reading-logs-during-a-production-incident"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["linux task scheduling", "cron overview", "automate linux tasks", "cron vs systemd timers"]
canonicalUrl: "/docs/cron/task-scheduling-overview"
---

# Task Scheduling Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** Linux automates recurring and one-off work with `cron` (recurring), `at` (one-off),
> `anacron` (recurring on machines that aren't always on), and systemd timers (the modern,
> logged alternative to all three). This module covers all four.

## What is it?

Task scheduling is running a command automatically at a specified time or interval, without a
human triggering it manually. Linux ships several tools for this, each suited to a different
situation.

## Why does it exist?

Production systems need work to happen on a schedule that no one is awake to trigger manually:
nightly backups, hourly cache cleanup, certificate renewal checks, log rotation. Without
scheduling, every recurring task becomes either a missed task or a person's manual chore.

## Where is it used?

- Backup jobs running nightly on database servers.
- Certificate renewal checks (`certbot renew` is commonly cron-scheduled).
- Cache/temp file cleanup on a fixed interval.
- Health-check or monitoring scripts running every few minutes.
- Batch/report generation jobs on servers, and on laptops/desktops that aren't always powered on
  (where `anacron` matters more than `cron`).

## How it works

> 📊 Diagram: a decision tree — "recurring or one-off?" branching to `cron`/systemd timer
> (recurring) vs `at` (one-off); then "always-on server or intermittently-on machine?" branching
> to `cron` vs `anacron`; then "want unit-level logging/dependencies?" branching to systemd timers
> as the modern alternative to all three.

This module covers four tools, each with a distinct niche:

| Tool | Use case | Depth in this module |
|---|---|---|
| `cron` | Recurring jobs on always-on systems (the default choice) | [crontab syntax](crontab-syntax.md), [crontab -e/-l/-r](crontab-e-l-r.md), [system-wide cron](system-wide-cron.md) |
| `at` | One-off, single-execution jobs at a future time | [at and anacron](at-and-anacron.md) |
| `anacron` | Recurring jobs on machines that may be powered off at the scheduled time (laptops, desktops) | [at and anacron](at-and-anacron.md) |
| systemd timers | Modern replacement for all of the above, with journal logging and unit dependencies | [systemd timers](systemd-timers.md) |

`cron` remains the most widely deployed scheduler across existing production systems, so this
module teaches it first and in the most depth, then covers systemd timers as the direction newer
systems are moving.

## Real-world example

A team inherits a server where a nightly backup silently stopped running weeks ago. Investigating
starts with "is this a cron job?" — checking `crontab -l` for the owning user and `/etc/cron.d/`
for system-wide entries, then confirming via logs (Module 16) whether the job ran and failed, or
never ran at all. This module's tools are exactly what that investigation depends on.

## Syntax

Each tool has its own syntax, covered on its dedicated page — no single syntax spans all four.

## Commands

No command example on this page — see [crontab syntax](crontab-syntax.md) for the first one.

## Production example

```
$ crontab -l
no crontab for deploy
```

An empty result here is often the first clue when a "scheduled" job never ran — it simply was
never scheduled for this user in the first place.

## Do / Don't

| Do | Don't |
|---|---|
| Pick the tool that matches the job (recurring vs one-off, always-on vs intermittent) | Default to `cron` for every case without considering `at` or `anacron` |
| Check logs to confirm a scheduled job actually ran | Assume a job ran just because it's scheduled |
| Consider systemd timers for new work on systemd-based systems | Assume `cron` is deprecated — it's still the most common tool in production today |

## Common mistakes

- Using `cron` for a job that should only run once, creating unnecessary recurring executions or
  race conditions if not removed after use — `at` exists for exactly this case.
- Scheduling a job with `cron` on a machine that's frequently powered off (a laptop), causing
  silently missed runs — `anacron` exists for exactly this case.
- Assuming a scheduled job ran successfully without checking logs or output.

## Best practices

- Match the tool to the job's actual shape (recurring/one-off, always-on/intermittent) rather than
  reaching for `cron` by habit.
- Always verify a new scheduled job actually fires as expected before trusting it unattended.
- On systemd-based systems, evaluate systemd timers for new scheduled work — they integrate with
  the journal and unit dependency system this documentation already covers (Modules 11 and 16).

## Exercises

1. List the four scheduling tools this module covers and, in one sentence each, when to use them.
2. Run `crontab -l` on your own system and note the result.
3. Explain why `anacron` exists as a separate tool from `cron`, rather than `cron` simply covering
   both cases.

## Quiz

**Q: Which tool is designed for a job that should run exactly once, not recurringly?**
<details><summary>Show answer</summary>
`at` — it schedules a single future execution, unlike `cron`, which is for recurring schedules.
</details>

**Q: Why might `anacron` be more appropriate than `cron` on a laptop?**
<details><summary>Show answer</summary>
`cron` only fires if the system is powered on at the scheduled time; `anacron` catches up on
missed recurring jobs the next time the machine is on, which matters for machines that aren't
always running.
</details>

**Q: What is the modern systemd-based alternative to cron, at, and anacron?**
<details><summary>Show answer</summary>
systemd timers — they provide journal logging and unit dependency integration that the older
tools lack.
</details>

## Interview questions

- When would you choose `at` over `cron`, and why? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Task scheduling automates recurring and one-off work without manual triggering.
- `cron` handles recurring jobs on always-on systems; `at` handles one-off future jobs; `anacron`
  handles recurring jobs on intermittently-on machines; systemd timers modernize all three.
- Matching the tool to the job's shape avoids silent failures and missed runs.
- Always verify a scheduled job actually ran — scheduling it is not the same as confirming it
  executed successfully.

## Related topics

- [crontab Syntax](crontab-syntax.md)
- [at and anacron](at-and-anacron.md)
- [systemd Timers](systemd-timers.md)
