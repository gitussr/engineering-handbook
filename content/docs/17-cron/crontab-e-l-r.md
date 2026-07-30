---
title: "crontab -e, -l, -r"
description: "Editing, listing, and removing a user's personal crontab with the crontab command's three core flags."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["17-cron/crontab-syntax"]
relatedTopics: ["crontab-syntax", "system-wide-cron"]
relatedCommands: ["crontab"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#crontab-e-vs-etc-crontab"]
relatedCheatsheet: "cron"
furtherReading: []
nextTopic: "17-cron/system-wide-cron"
prevTopic: "17-cron/crontab-syntax"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["crontab -e", "crontab -l", "crontab -r", "edit user crontab", "list cron jobs"]
canonicalUrl: "/docs/cron/crontab-e-l-r"
---

# crontab -e, -l, -r

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `crontab -e` edits your personal crontab, `crontab -l` lists it, `crontab -r` removes
> it entirely. Each user has their own crontab, checked and run under their own permissions.

## What is it?

The three everyday flags for managing a per-user crontab: `-e` to edit, `-l` to list, `-r` to
remove.

## Why does it exist?

Every user on a system can schedule their own recurring jobs, running with that user's
permissions, without needing root access or editing a shared system file. These three flags are
the entire day-to-day interface for that per-user schedule.

## Where is it used?

Any time an engineer schedules a personal or application-owned recurring job — a deploy user
scheduling their own health checks, a database user scheduling their own backup script — without
needing to touch system-wide configuration.

## How it works

> 📊 Diagram: one box labeled "user's crontab file" (stored under `/var/spool/cron/`) with three
> labeled arrows into/out of it: `crontab -e` (opens it in `$EDITOR`, saves atomically on exit),
> `crontab -l` (reads and prints it, read-only), `crontab -r` (deletes it, no confirmation prompt
> by default).

- `crontab -e` opens the current user's crontab in `$EDITOR` (or a system default like `vi` if
  unset). On save and exit, `cron`'s daemon (`crond` or `cron`) picks up the change automatically
  — no restart needed.
- `crontab -l` prints the current user's crontab to standard output without opening an editor —
  the safe way to check what's scheduled.
- `crontab -r` deletes the current user's entire crontab immediately, with **no confirmation
  prompt** by default on most distributions — this is the single most dangerous flag in this
  trio.
- Each user's crontab is independent and stored separately (typically under
  `/var/spool/cron/crontabs/` or `/var/spool/cron/`, distro-dependent); jobs run as that user,
  with that user's environment and permissions, not root's, unless run from root's own crontab.

## Real-world example

An engineer intends to run `crontab -e` to add one more job to an existing schedule, but mistypes
`crontab -r` — instantly deleting every job that user had scheduled, with no prompt and no
built-in undo. Recovering means either restoring from a backup of the crontab file (if one exists)
or manually re-writing every job from memory or documentation — which is exactly why production
crontabs should be version-controlled or documented outside the live file itself.

## Syntax

```
crontab [-u USER] -e | -l | -r
```

## Commands

See [`crontab`](../../commands/crontab.md) for the full command reference.

## Production example

```
$ crontab -l
30 2 * * * /usr/local/bin/backup.sh

$ crontab -e
# opens $EDITOR with the current crontab; add a line, save, exit

$ crontab -l
30 2 * * * /usr/local/bin/backup.sh
*/5 * * * * /usr/local/bin/healthcheck.sh
```

Listing before and after `crontab -e` confirms the new job was actually saved.

## Do / Don't

| Do | Don't |
|---|---|
| Run `crontab -l` before and after editing to confirm changes | Assume `crontab -e` saved correctly without checking |
| Keep a backup or version-controlled copy of important crontabs | Treat the live crontab as the only copy of critical scheduling logic |
| Double-check before running `crontab -r` | Type `-r` when you meant `-e` without a moment's pause — there's no confirmation prompt |

## Common mistakes

- Confusing `-r` (remove) with `-e` (edit) — a single-character typo that deletes an entire
  schedule with no confirmation and no undo.
- Editing another user's crontab without `-u USER` (requires root) and being confused when
  changes don't appear — `crontab -e` without `-u` always targets the current user.
- Not verifying a scheduled job actually appears via `crontab -l` after editing, only discovering
  it was never saved when the job doesn't run.

## Best practices

- Keep a backup of production crontabs outside the live file — a version-controlled dotfiles
  repo, a configuration management tool (Ansible, etc.), or even a periodic `crontab -l >
  backup.txt`.
- Always list (`-l`) before and after edits to confirm the change took effect as intended.
- Use `crontab -u USER -l` (as root) to audit another user's schedule during an incident, rather
  than switching users just to check.

## Exercises

1. Run `crontab -l` on your own account and note whether any jobs are scheduled.
2. Add a temporary test job via `crontab -e`, then confirm it with `-l`.
3. Explain why `crontab -r` is considered the most dangerous of these three flags.

## Quiz

**Q: Which flag removes a user's entire crontab, with no confirmation prompt by default?**
<details><summary>Show answer</summary>
`crontab -r` — it deletes the whole crontab immediately, which is why it's easy to trigger
accidentally when meaning to type `-e`.
</details>

**Q: Does `cron` need to be restarted after `crontab -e` saves a change?**
<details><summary>Show answer</summary>
No — the cron daemon picks up changes to a user's crontab automatically on save.
</details>

**Q: What user does a job scheduled via `crontab -e` run as?**
<details><summary>Show answer</summary>
The user who owns that crontab — each user's crontab runs jobs under that user's own permissions
and environment, not root's (unless it's root's own crontab).
</details>

## Interview questions

- What's the difference between a per-user crontab (`crontab -e`) and `/etc/crontab`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `crontab -e` edits, `crontab -l` lists, `crontab -r` removes — each user's personal crontab.
- Changes via `crontab -e` take effect immediately, no daemon restart needed.
- `-r` has no confirmation prompt by default and deletes the entire crontab — the riskiest of the
  three flags.
- Each user's crontab runs jobs under that user's own permissions, independent of other users'
  schedules.

## Related topics

- [crontab Syntax](crontab-syntax.md)
- [System-wide cron](system-wide-cron.md)
