---
title: "Cron"
description: "Module 17 of the Linux roadmap — task scheduling with cron, crontab syntax and management, system-wide cron, at, anacron, and systemd timers."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
type: "module-index"
nextTopic: "17-cron/task-scheduling-overview"
updatedAt: "2026-07-27"
canonicalUrl: "/docs/cron"
---

# Cron

Module 17 of 34 · Stage: Intermediate · Previous: [16 Logs](../16-logs/index.md)

[Module 16](../16-logs/index.md) covered how to read what already happened on a system. This
module covers making things happen automatically in the first place: `cron`'s five-field
schedule syntax, managing a personal crontab, system-wide cron for package- and service-owned
jobs, `at` and `anacron` for the cases plain `cron` handles poorly, systemd timers as the modern
alternative, and a repeatable troubleshooting sequence for when a scheduled job doesn't behave as
expected.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Task Scheduling Overview](task-scheduling-overview.md) | 🟢 Must Know |
| [crontab Syntax](crontab-syntax.md) | 🟢 Must Know |
| [crontab -e, -l, -r](crontab-e-l-r.md) | 🟢 Must Know |
| [System-wide Cron: /etc/cron.d and /etc/crontab](system-wide-cron.md) | 🟡 Good to Know |
| [at and anacron](at-and-anacron.md) | 🟡 Good to Know |
| [systemd Timers (Modern Alternative to Cron)](systemd-timers.md) | 🔴 Expert |
| [Cron Troubleshooting and Logging](cron-troubleshooting-and-logging.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Choose the right scheduling tool (`cron`, `at`, `anacron`, or a systemd timer) for a given job's
  shape.
- Read and write crontab time-field syntax, including ranges, lists, steps, and special strings.
- Manage a personal crontab with `crontab -e`/`-l`/`-r`, and know why `-r` is dangerous.
- Explain the extra user field in system-wide cron files and when to use `/etc/cron.d/` over
  editing `/etc/crontab` directly.
- Schedule one-off jobs with `at` and understand `anacron`'s catch-up behavior for
  intermittently-on machines.
- Build a basic systemd `.timer`/`.service` pair and explain its advantages over `cron`.
- Follow a repeatable sequence to debug a cron job that isn't running as expected.

## Known, intentional gaps in this module

- Canonical command pages exist for [`crontab`](../../commands/crontab.md),
  [`at`](../../commands/at.md), and [`anacron`](../../commands/anacron.md) — the three commands
  explicitly named in this module's roadmap bullets.
- systemd timers reuse [`systemctl`](../../commands/systemctl.md) and
  [`journalctl`](../../commands/journalctl.md), both already covered in Modules 11 and 16 — no new
  command page was created for them, consistent with how unit-file topics were handled in
  Module 11.
- `atq`/`atrm` (querying and removing pending `at` jobs) are documented as options on the
  [`at`](../../commands/at.md) command page rather than as separate canonical pages — they're
  thin wrappers around the same job queue `at` creates.

**Previous module:** [16 Logs](../16-logs/index.md)
**Next module:** [18 Shell Scripting →](../18-shell-scripting/index.md)
