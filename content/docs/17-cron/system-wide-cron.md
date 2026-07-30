---
title: "System-wide Cron: /etc/cron.d and /etc/crontab"
description: "How system-wide cron jobs differ from per-user crontabs — the extra user field, /etc/cron.d/, /etc/crontab, and the /etc/cron.{hourly,daily,weekly,monthly} directories."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["17-cron/crontab-e-l-r"]
relatedTopics: ["crontab-e-l-r", "cron-troubleshooting-and-logging"]
relatedCommands: ["crontab"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: []
relatedCheatsheet: "cron"
furtherReading: [{"label": "crontab(5) man page", "url": "https://man7.org/linux/man-pages/man5/crontab.5.html"}]
nextTopic: "17-cron/at-and-anacron"
prevTopic: "17-cron/crontab-e-l-r"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["etc cron.d", "etc crontab", "system-wide cron jobs", "cron.daily cron.hourly"]
canonicalUrl: "/docs/cron/system-wide-cron"
---

# System-wide Cron: /etc/cron.d and /etc/crontab

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** System-wide cron jobs live in `/etc/crontab` and `/etc/cron.d/*`, and unlike
> per-user crontabs, they have an extra field specifying which user the job runs as. Packages
> commonly drop their own scheduled jobs into `/etc/cron.d/`.

## What is it?

Cron scheduling that isn't owned by any single user's personal crontab: `/etc/crontab` (one file)
and `/etc/cron.d/` (a directory of files), both root-managed, plus the simpler
`/etc/cron.{hourly,daily,weekly,monthly}` directories.

## Why does it exist?

Some scheduled jobs belong to the system or to an installed package, not to a specific human
user's account — log rotation, package-manager maintenance, or an application's own scheduled
task installed alongside it. System-wide cron gives these jobs a home that doesn't depend on any
particular user's personal crontab existing or being edited.

## Where is it used?

- Packages that install their own scheduled maintenance job drop a file into `/etc/cron.d/`
  during installation (this is how `logrotate` itself is commonly scheduled, from
  [Module 16](../16-logs/logrotate.md)).
- System administrators managing jobs that should run as a specific service account, not as
  whichever human happens to run `crontab -e`.
- Configuration management tools (Ansible, Puppet, etc.) that need to declaratively manage
  scheduled jobs as files, rather than mutating a live per-user crontab.

## How it works

> 📊 Diagram: two side-by-side crontab line formats — a per-user crontab line (5 time fields +
> command) and a system-wide crontab line (5 time fields + **user** field + command) — with the
> extra user field highlighted, plus a small file-tree showing `/etc/crontab`, `/etc/cron.d/*`,
> and `/etc/cron.{hourly,daily,weekly,monthly}/` as siblings.

**The key difference from a per-user crontab: an extra user field.**

```
# Per-user crontab (crontab -e):
MINUTE HOUR DOM MONTH DOW COMMAND

# System-wide (/etc/crontab or /etc/cron.d/*):
MINUTE HOUR DOM MONTH DOW USER COMMAND
```

Because system-wide files can be edited by root on behalf of any user, cron needs to know which
user each line should run as — that's the extra field, positioned right after the five time
fields and before the command.

**The three system-wide locations:**

| Location | Purpose |
|---|---|
| `/etc/crontab` | A single file, root-owned, for system-wide jobs — same extra-user-field format |
| `/etc/cron.d/*` | A directory where packages and admins drop individual job files, same format as `/etc/crontab` — preferred over editing `/etc/crontab` directly because each package/purpose gets its own file |
| `/etc/cron.{hourly,daily,weekly,monthly}/` | Directories of executable scripts (no cron syntax at all) — anything placed here and made executable runs on that named interval automatically, driven by a single line in `/etc/crontab` that runs `run-parts` against each directory |

## Real-world example

A backup script installed by a package manager needs to run nightly as the `backup` service
account, not as whichever admin happens to be logged in. The package drops a file into
`/etc/cron.d/mybackup` containing `0 2 * * * backup /usr/local/bin/run-backup.sh` — the schedule,
the exact user it runs as, and the command, all declared in one file that survives independent of
any human's personal crontab.

## Syntax

```
MINUTE HOUR DAY_OF_MONTH MONTH DAY_OF_WEEK USER COMMAND
```

## Commands

No new command — system-wide cron files are edited directly (as root, with a regular text editor)
rather than through `crontab`; see [`crontab`](../../commands/crontab.md) for the per-user
equivalent covered on the previous page.

## Production example

```
$ cat /etc/cron.d/logrotate
0 3 * * * root /usr/sbin/logrotate /etc/logrotate.conf

$ ls /etc/cron.daily/
apt-compat  dpkg  logrotate  man-db
```

The first line shows a full system-wide crontab entry (with the `root` user field); the second
shows the simpler `cron.daily` pattern — plain executable scripts, no time-field syntax needed.

## Do / Don't

| Do | Don't |
|---|---|
| Give each package/purpose its own file under `/etc/cron.d/` | Pile unrelated jobs into a single shared `/etc/crontab` |
| Always include the user field on system-wide lines | Forget the user field — cron will reject or misinterpret the line without it |
| Use `/etc/cron.{hourly,daily,...}` for simple scripts with no specific time requirement | Use it for jobs that need a precise, non-standard schedule |

## Common mistakes

- Forgetting the extra user field when writing a system-wide crontab line — a line copy-pasted
  from a per-user crontab (no user field) will be misinterpreted or rejected in `/etc/cron.d/`.
- Editing `/etc/crontab` directly for a package-specific job instead of adding a dedicated file
  under `/etc/cron.d/`, making it harder to track which job belongs to which package.
- Placing a script in `/etc/cron.daily/` without making it executable — `run-parts` silently
  skips non-executable files.

## Best practices

- Prefer `/etc/cron.d/` with one file per job/package over editing `/etc/crontab` directly — it
  mirrors how most distro packages already do this.
- Name `/etc/cron.d/` files descriptively (matching the package or purpose) so their origin is
  obvious during an audit.
- Use the `/etc/cron.{hourly,daily,weekly,monthly}` directories for simple maintenance scripts
  that only need a standard interval, reserving explicit crontab syntax for anything more precise.

## Exercises

1. List the contents of `/etc/cron.d/` on your system and identify which package likely installed
   each file.
2. Explain the one syntactic difference between a per-user crontab line and a system-wide one.
3. Describe what `run-parts` does with the `/etc/cron.daily/` directory.

## Quiz

**Q: What extra field does a system-wide crontab line have that a per-user crontab line doesn't?**
<details><summary>Show answer</summary>
A user field, specifying which user the command runs as — positioned after the five time fields
and before the command.
</details>

**Q: Why might a package prefer dropping a file into /etc/cron.d/ over editing /etc/crontab?**
<details><summary>Show answer</summary>
Each package/purpose gets its own isolated file, making it clear which job belongs to which
package and avoiding merge conflicts in a single shared file.
</details>

**Q: What happens if a script in /etc/cron.daily/ isn't marked executable?**
<details><summary>Show answer</summary>
`run-parts` silently skips it — the script never runs, with no error surfaced by default.
</details>

## Interview questions

- Explain the difference between a per-user crontab and /etc/cron.d — when would you use each? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- System-wide cron files (`/etc/crontab`, `/etc/cron.d/*`) add a user field that per-user
  crontabs don't have.
- `/etc/cron.d/` is preferred over editing `/etc/crontab` directly — one file per job/package.
- `/etc/cron.{hourly,daily,weekly,monthly}` directories hold plain executable scripts, no cron
  syntax required, run via `run-parts`.
- Packages commonly install their own scheduled maintenance jobs into `/etc/cron.d/` during
  installation.

## Related topics

- [crontab -e, -l, -r](crontab-e-l-r.md)
- [Cron Troubleshooting and Logging](cron-troubleshooting-and-logging.md)
