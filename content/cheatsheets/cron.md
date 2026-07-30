---
title: "Cron Cheat Sheet"
description: "A single-page quick reference for cron: crontab syntax, managing your crontab, system-wide cron, at/anacron, and the modern systemd timers alternative."
type: "cheatsheet"
module: "cron"
updatedAt: "2026-07-30"
keywords: ["cron cheat sheet", "crontab cheat sheet", "cron syntax cheat sheet", "crontab examples"]
canonicalUrl: "/cheatsheets/cron"
---

# Cron Cheat Sheet

The syntax and commands for scheduling recurring and one-off tasks on Linux.

## crontab syntax

```
* * * * * command-to-run
│ │ │ │ │
│ │ │ │ └── day of week (0-7, both 0 and 7 = Sunday)
│ │ │ └──── month (1-12)
│ │ └────── day of month (1-31)
│ └──────── hour (0-23)
└────────── minute (0-59)
```

| Field syntax | Meaning |
|---|---|
| `*` | Every value (e.g. every minute, every hour) |
| `*/15` | Every 15 units (e.g. every 15 minutes) |
| `1,15` | Specific values (e.g. the 1st and 15th) |
| `1-5` | A range (e.g. Monday through Friday for day-of-week) |

Full explanation → [Crontab Syntax](../docs/17-cron/crontab-syntax.md)

## Managing your crontab

| Command | Description |
|---|---|
| [`crontab`](../commands/crontab.md) `-e` | Edit your personal crontab |
| `crontab -l` | List your current crontab entries |
| `crontab -r` | Remove your entire crontab (no confirmation — be careful) |

Full explanation → [crontab -e, -l, -r](../docs/17-cron/crontab-e-l-r.md)

## System-wide cron

| Location | Description |
|---|---|
| `/etc/crontab` | System-wide crontab, includes a user field per line |
| `/etc/cron.d/` | Drop-in directory for package-managed system cron jobs |
| `/etc/cron.daily/`, `/etc/cron.hourly/` | Script directories run on a fixed schedule by `run-parts` |

Full explanation → [System-Wide Cron](../docs/17-cron/system-wide-cron.md)

## at and anacron

| Command | Description |
|---|---|
| [`at`](../commands/at.md) `14:00` | Schedule a one-off command to run once at a specific time |
| `atq` | List pending `at` jobs |
| `atrm {job}` | Cancel a pending `at` job |
| [`anacron`](../commands/anacron.md) | Runs missed periodic jobs after a machine that was off comes back on (laptops/desktops) |

Full explanation → [at and anacron](../docs/17-cron/at-and-anacron.md)

## systemd timers (modern alternative)

| Concept | Description |
|---|---|
| `{name}.timer` + `{name}.service` | A timer unit triggers a matching service unit |
| `OnCalendar=` | Calendar-based schedule (cron-like, but human-readable) |
| `Persistent=true` | Catch up on a missed run after downtime — cron can't do this natively |
| `systemctl list-timers` | List all active timers and their next run time |

Full explanation → [systemd Timers](../docs/17-cron/systemd-timers.md)

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| Works manually, fails under cron | Minimal cron environment — missing `PATH`, unset variables. Use absolute paths and set `PATH` explicitly. |
| No output/errors visible | Cron mails job output by default only if mail is configured — redirect output to a log file instead |
| Job "succeeded" but did nothing | A pipeline's exit status can hide an earlier failure — use `set -o pipefail` in the script |

Full explanation → [Cron Troubleshooting and Logging](../docs/17-cron/cron-troubleshooting-and-logging.md)

## Related topics

- [systemctl Cheat Sheet](systemctl.md)
- [Cron Module](../docs/17-cron/index.md)
- [Task Scheduling Overview](../docs/17-cron/task-scheduling-overview.md)
- [Cheat Sheets Hub](index.md)
