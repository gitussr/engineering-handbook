---
title: "Cron Troubleshooting and Logging"
description: "Why a cron job that works when run manually often fails when cron runs it — environment differences, where cron logs to, and a repeatable debugging sequence."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["17-cron/systemd-timers"]
relatedTopics: ["system-wide-cron", "systemd-timers"]
relatedCommands: ["crontab", "journalctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#cron-job-not-running"]
relatedCheatsheet: "cron"
furtherReading: []
nextTopic: "18-shell-scripting/writing-your-first-script"
prevTopic: "17-cron/systemd-timers"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["cron job not running", "cron path environment variable", "cron logs var log syslog", "MAILTO cron"]
canonicalUrl: "/docs/cron/cron-troubleshooting-and-logging"
---

# Cron Troubleshooting and Logging

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** A cron job that works fine when run manually often fails under `cron` because cron
> jobs run with a minimal environment — check `PATH`, check logs (`/var/log/cron`,
> `/var/log/syslog`, or `journalctl -u cron`), and redirect job output somewhere you'll actually
> see it.

## What is it?

The systematic way to diagnose why a scheduled `cron` job isn't running, or isn't producing the
expected result — the single most common practical skill this module builds toward.

## Why does it exist?

"It works when I run it manually" followed by "it doesn't work under cron" is one of the most
common tickets involving scheduling — and it has a small, well-known set of root causes. Knowing
that set turns a potentially long debugging session into a five-minute checklist.

## Where is it used?

Any time a scheduled job has silently stopped working, or a new job doesn't behave as expected —
the exact scenario introduced back in [Task Scheduling Overview](task-scheduling-overview.md)'s
real-world example, now given a full systematic answer.

## How it works

> 📊 Diagram: a troubleshooting flowchart — "job not running as expected" branching to four
> checks in order: (1) is it actually scheduled? `crontab -l` / `/etc/cron.d/`, (2) did it fire?
> check logs, (3) did it fail due to environment (PATH, working directory)?, (4) where did its
> output go? — each branch pointing to its specific fix.

**The standard cron debugging sequence:**

1. **Confirm the job is actually scheduled.** Check `crontab -l` for the right user, and
   `/etc/cron.d/`/`/etc/crontab` for system-wide jobs (Module's earlier topics). A mistyped
   schedule or a job installed for the wrong user is the single most common root cause.
2. **Check whether cron even attempted to run it.** Cron logs its own activity (not the job's
   output) to `/var/log/cron` (RHEL-family) or `/var/log/syslog` (Debian/Ubuntu-family), or via
   `journalctl -u cron`/`journalctl -u crond` on systemd-based systems — see
   [Module 16](../16-logs/index.md) for the general log-investigation skills this builds on.
3. **Suspect the environment next.** Cron jobs run with a minimal environment — often a bare
   `PATH` like `/usr/bin:/bin`, no shell profile sourced, and the job's working directory is not
   guaranteed to be what you expect. A script that calls a tool by name (relying on a `PATH` set
   in `~/.bashrc`) can fail under cron while working fine in an interactive shell. Fix by using
   absolute paths in the script, or explicitly setting `PATH` at the top of the crontab.
4. **Check where output went.** By default, cron emails a job's stdout/stderr to the crontab
   owner (via `MAILTO=`, if mail delivery is configured) — on many modern servers, no mail system
   is configured, so that output silently disappears. Redirect explicitly instead:
   `command >> /var/log/myjob.log 2>&1`.

## Real-world example

A deploy script works perfectly when an engineer runs it by hand, but silently fails every night
under `cron`. Following the sequence: the job is confirmed scheduled (`crontab -l` shows it),
`/var/log/syslog` confirms cron did fire it, but the script called `aws` by bare name — which
existed in the engineer's interactive `PATH` (set via `~/.bashrc`, never sourced by cron) but not
in cron's minimal `PATH`. Switching to the absolute path `/usr/local/bin/aws` (or explicitly
setting `PATH=` in the crontab) fixes it — a textbook case of step 3 in the sequence above.

## Syntax

No new syntax — this page applies syntax and tools already covered earlier in this module and in
Module 16.

## Commands

No new command — see [`crontab`](../../commands/crontab.md) and
[`journalctl`](../../commands/journalctl.md), both already covered.

## Production example

```
$ crontab -l
PATH=/usr/local/bin:/usr/bin:/bin
MAILTO=""
30 2 * * * /usr/local/bin/deploy-check.sh >> /var/log/deploy-check.log 2>&1

$ tail /var/log/deploy-check.log
Jul 27 02:30:01 Starting deploy check...
Jul 27 02:30:03 All services healthy.
```

A production-ready crontab entry: explicit `PATH`, output redirected to a durable log file rather
than relying on mail delivery, and `2>&1` capturing errors alongside normal output.

## Do / Don't

| Do | Don't |
|---|---|
| Use absolute paths for commands inside cron-run scripts | Rely on a `PATH` that's only set in an interactive shell's profile |
| Redirect job output explicitly to a log file | Assume mail delivery is configured and silently trust `MAILTO` |
| Check logs (`/var/log/cron`, `/var/log/syslog`, or `journalctl -u cron`) before assuming the job never fired | Assume a "missing" result means the job never ran, without checking whether it ran and failed |

## Common mistakes

- Assuming a script that works interactively will work identically under cron, without accounting
  for the minimal environment cron provides.
- Not redirecting job output anywhere durable, then having no evidence when the job fails
  silently.
- Debugging by guessing instead of following the fixed sequence (scheduled? fired? environment?
  output?) — wasting time checking things out of order.

## Best practices

- Set an explicit `PATH=` at the top of any crontab with jobs that call external tools.
- Always redirect cron job output (`>> logfile 2>&1`) rather than depending on system mail
  delivery, which is frequently unconfigured on modern servers.
- When a script must behave identically interactively and under cron, test it non-interactively
  (e.g. `env -i /bin/sh script.sh`) rather than only running it by hand.

## Exercises

1. Write a one-line crontab entry that sets `PATH` explicitly and redirects output to a log file.
2. Explain why a script calling a bare command name (`aws`, `node`, etc.) can work interactively
   but fail under cron.
3. Describe the four-step debugging sequence for a cron job that isn't behaving as expected.

## Quiz

**Q: Why might a script that works fine when run manually fail under cron?**
<details><summary>Show answer</summary>
Cron jobs run with a minimal environment — often a bare `PATH` and no sourced shell profile —
so commands that rely on an interactive shell's environment can fail to be found or behave
differently.
</details>

**Q: Where does cron's own activity log typically go on a Debian/Ubuntu-family system?**
<details><summary>Show answer</summary>
`/var/log/syslog` (or `journalctl -u cron` on systemd-based systems) — this logs whether cron
attempted to run the job, not the job's own output.
</details>

**Q: What's the recommended way to capture a cron job's output instead of relying on MAILTO?**
<details><summary>Show answer</summary>
Redirect stdout and stderr explicitly in the crontab line itself, e.g. `>> /var/log/myjob.log
2>&1`, since mail delivery is frequently unconfigured on modern servers.
</details>

## Interview questions

- A cron job works when run manually but fails on schedule — walk through how you'd debug it. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Follow a fixed sequence: confirm it's scheduled, confirm cron fired it (via logs), suspect the
  environment (especially `PATH`), then check where output went.
- Cron jobs run with a minimal environment — absolute paths and explicit `PATH=` avoid the most
  common class of "works manually, fails under cron" bugs.
- Redirect job output explicitly rather than relying on `MAILTO`/mail delivery, which is often
  unconfigured.
- This page is the applied troubleshooting synthesis of every other topic in this module.

## Related topics

- [System-wide cron](system-wide-cron.md)
- [systemd Timers](systemd-timers.md)
- [Module 18: Shell Scripting](../18-shell-scripting/index.md)
