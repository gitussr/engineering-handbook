---
title: "systemd Timers (Modern Alternative to Cron)"
description: "How .timer units pair with .service units to schedule work under systemd, with journal logging and dependency management that cron and anacron lack."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["17-cron/at-and-anacron"]
relatedTopics: ["at-and-anacron", "cron-troubleshooting-and-logging"]
relatedCommands: ["systemctl", "journalctl"]
careerRelevance: ["devops", "sre", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#systemd-timers-vs-cron"]
relatedCheatsheet: "cron"
furtherReading: [{"label": "systemd.timer(5) man page", "url": "https://www.freedesktop.org/software/systemd/man/systemd.timer.html"}]
nextTopic: "17-cron/cron-troubleshooting-and-logging"
prevTopic: "17-cron/at-and-anacron"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["systemd timers vs cron", "systemd.timer", "OnCalendar", "systemctl list-timers"]
canonicalUrl: "/docs/cron/systemd-timers"
---

# systemd Timers (Modern Alternative to Cron)

🔴 Expert · Relevant for: DevOps · SRE · Cloud · Platform

> **TL;DR:** A systemd timer (`.timer` unit) triggers a matching `.service` unit on a schedule —
> the modern alternative to `cron`, `at`, and `anacron` combined, with journal logging and unit
> dependency management built in.

## What is it?

A systemd unit type (`.timer`) that triggers another unit (typically a `.service`) on a schedule,
using the same unit-file system already covered in [Module 11](../11-services/index.md).

## Why does it exist?

`cron` jobs are opaque to the rest of the system — no structured logging beyond whatever the job
itself writes, no dependency awareness (can't easily say "run only after the network is up" or
"don't run if a related service is down"), and no built-in way to query "did this job's last run
succeed?" systemd timers fix all three by making scheduled work a first-class systemd unit,
subject to the same journal logging, dependency ordering, and status querying as any other
service.

## Where is it used?

Systems already standardized on systemd for service management (most modern distros) that want
scheduled jobs to integrate with the same tooling — `systemctl status`, `journalctl -u`,
dependency ordering — rather than living in a separate, less observable subsystem.

## How it works

> 📊 Diagram: two paired boxes — a `.timer` unit (`OnCalendar=` schedule) with an arrow labeled
> "activates" pointing to a `.service` unit of the same base name — both feeding into the systemd
> journal, contrasted against a `cron` job (dotted box, no journal connection, no dependency
> arrows) off to the side.

A systemd timer always pairs with a service unit of the matching name (`backup.timer` triggers
`backup.service`, unless overridden with `Unit=`):

```ini
# /etc/systemd/system/backup.timer
[Unit]
Description=Run backup nightly

[Timer]
OnCalendar=*-*-* 02:30:00
Persistent=true

[Install]
WantedBy=timers.target
```

```ini
# /etc/systemd/system/backup.service
[Unit]
Description=Backup job

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```

Enabling and starting the **timer** (not the service directly) activates the schedule:

```
$ sudo systemctl enable --now backup.timer
```

Key advantages over `cron`/`anacron`:

| Feature | cron | systemd timer |
|---|---|---|
| Logging | Whatever the job itself writes | Full journal integration (`journalctl -u backup.service`) |
| Catch-up after downtime | Only via separate `anacron` | Built in via `Persistent=true` |
| Dependency ordering | None | Full systemd dependency system (`After=`, `Requires=`, etc.) |
| Status query | None built-in | `systemctl status backup.timer` / `list-timers` |

## Real-world example

A team migrating from `cron` to systemd timers for their nightly backup gains an immediate
debugging improvement: instead of grepping a custom log file the backup script itself wrote (or
worse, nothing at all if it crashed before logging), `journalctl -u backup.service` shows exactly
when the job ran, its exit code, and any output — the same investigative pattern already taught
for every other systemd service in Modules 11 and 16, now applying to scheduled work too.

## Syntax

```
[Timer]
OnCalendar=CALENDAR_EXPRESSION
OnBootSec=DURATION
OnUnitActiveSec=DURATION
Persistent=true|false
```

## Commands

No new command page — systemd timers are managed entirely through
[`systemctl`](../../commands/systemctl.md) (already covered in Module 11) and inspected with
[`journalctl`](../../commands/journalctl.md), the same as any other systemd unit.

## Production example

```
$ systemctl list-timers
NEXT                        LEFT     LAST                         PASSED  UNIT           ACTIVATES
Mon 2026-07-28 02:30:00 UTC 8h left  Sun 2026-07-27 02:30:00 UTC  15h ago backup.timer   backup.service

$ journalctl -u backup.service -n 5
Jul 27 02:30:01 web-01 systemd[1]: Starting Backup job...
Jul 27 02:31:44 web-01 backup.sh[9931]: Backup completed: 4.2GB
Jul 27 02:31:44 web-01 systemd[1]: backup.service: Deactivated successfully.
```

`list-timers` shows exactly when a job last ran and next runs, and `journalctl -u` shows its
actual execution log — both unavailable in equivalent detail from raw `cron`.

## Do / Don't

| Do | Don't |
|---|---|
| Enable and start the `.timer` unit, not the `.service` directly | Manually start the `.service` expecting it to recur — it won't, without the timer |
| Set `Persistent=true` for jobs that must catch up after downtime | Assume a missed run (system was off) is automatically caught up without this setting |
| Use `journalctl -u` to check a scheduled job's execution history | Rely on custom application logging alone when journal integration is available for free |

## Common mistakes

- Starting the `.service` unit directly instead of the `.timer`, causing the job to run once but
  never recur on schedule.
- Forgetting `Persistent=true` on a machine that isn't always on, losing the `anacron`-equivalent
  catch-up behavior by default.
- Writing an incorrect `OnCalendar=` expression and not validating it — `systemd-analyze
  calendar "EXPRESSION"` checks this before deploying.

## Best practices

- Validate `OnCalendar=` expressions with `systemd-analyze calendar` before enabling a new timer.
- Use `Type=oneshot` for the paired service unit — timer-triggered jobs should run to completion
  and exit, not persist as long-running services.
- Prefer systemd timers over `cron` for new scheduled work on systemd-based systems specifically
  when journal integration or dependency ordering adds real value — for a trivial script with no
  dependencies, plain `cron` remains simpler and is still the more common tool in existing
  production environments.

## Exercises

1. Write a minimal `.timer`/`.service` pair that runs a script every hour.
2. Run `systemctl list-timers` on a systemd-based system and identify what's currently scheduled.
3. Explain what `Persistent=true` does and which older tool's behavior it replaces.

## Quiz

**Q: What two unit types pair together to create a scheduled systemd job?**
<details><summary>Show answer</summary>
A `.timer` unit (the schedule) and a `.service` unit (the actual job) — enabling the timer
activates the service on schedule.
</details>

**Q: What does Persistent=true do on a timer unit?**
<details><summary>Show answer</summary>
It catches up a missed run after the system was off at the scheduled time — the systemd
equivalent of what `anacron` provides for traditional cron.
</details>

**Q: How do you check a systemd-timer-triggered job's execution history?**
<details><summary>Show answer</summary>
`journalctl -u SERVICE_NAME` — the same journal-based investigation used for any other systemd
service.
</details>

## Interview questions

- Why might a team migrate scheduled jobs from cron to systemd timers? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A systemd timer (`.timer`) triggers a matching service (`.service`) on a schedule — enable and
  start the timer, not the service, for recurring behavior.
- systemd timers add journal logging, dependency ordering, and status querying that `cron` lacks
  natively.
- `Persistent=true` replaces `anacron`'s catch-up behavior for systemd-scheduled jobs.
- `cron` remains simpler and more common for straightforward jobs; systemd timers earn their
  complexity when journal integration or dependencies genuinely matter.

## Related topics

- [at and anacron](at-and-anacron.md)
- [Cron Troubleshooting and Logging](cron-troubleshooting-and-logging.md)
