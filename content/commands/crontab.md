---
title: "crontab — Manage a User's Cron Jobs"
description: "Edit, list, and remove the current (or another) user's scheduled cron jobs."
relatedConcepts: ["17-cron/crontab-syntax", "17-cron/crontab-e-l-r", "17-cron/system-wide-cron"]
relatedCommands: ["at", "anacron", "systemctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["crontab command", "crontab -e", "crontab -l", "crontab -r", "crontab -u"]
canonicalUrl: "/commands/crontab"
---

# crontab

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `crontab -e` edits your scheduled jobs, `crontab -l` lists them, `crontab -r` deletes
> them all — see [crontab Syntax](../docs/17-cron/crontab-syntax.md) for the five-field schedule
> format itself.

## Purpose

`crontab` manages a per-user table of scheduled `cron` jobs — see
[crontab -e, -l, -r](../docs/17-cron/crontab-e-l-r.md) for the full concept.

## Syntax

```
crontab [-u USER] [-e | -l | -r | FILE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | Replace the crontab entirely with the contents of this file, instead of opening an editor | No |

## Options

| Flag | Meaning |
|---|---|
| `-e` | Edit the crontab in `$EDITOR` |
| `-l` | List the crontab to stdout |
| `-r` | Remove the entire crontab — no confirmation prompt by default |
| `-u USER` | Operate on another user's crontab (requires root) |
| `-i` | Prompt for confirmation before `-r` deletes (safer, not default on most distros) |

## Examples

```
$ crontab -l
```
List the current user's scheduled jobs.

```
$ crontab -e
```
Open the current user's crontab in `$EDITOR` for editing.

```
$ sudo crontab -u backup -l
```
List the `backup` user's crontab (requires root to view another user's).

```
$ crontab -r
```
Delete the current user's entire crontab immediately — no confirmation by default.

```
$ crontab -i -r
```
Delete the current user's entire crontab, but prompt for confirmation first.

## Expected Output

```
$ crontab -l
30 2 * * * /usr/local/bin/backup.sh
*/5 * * * * /usr/local/bin/healthcheck.sh
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | Error — no crontab exists for `-l`/`-e` on some implementations, invalid syntax on `-e` save, or permission denied |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `no crontab for USER` | No crontab has ever been created for that user | Expected on a fresh account — `crontab -e` creates one on first save |
| `crontab: you cannot change root's crontab` | Insufficient permission to edit another user's crontab | Run with `sudo`, or edit as that user directly |
| Edits under `-e` don't seem to save | Editor exited without a successful save, or a syntax error was rejected | Re-run `crontab -e`; malformed lines are typically flagged before the file is accepted |

## Security Considerations

Any user with `crontab` access can schedule arbitrary commands to run as themselves — on
multi-user systems, restricting who has `crontab` access (via `/etc/cron.allow` /
`/etc/cron.deny` where supported) is a real access-control boundary, not just a convenience
feature.

## Performance Considerations

`crontab` itself is lightweight; performance concerns belong to the scheduled jobs themselves, not
to `crontab` as a tool.

## Production Usage

Experienced engineers back up important crontabs outside the live file (version control,
configuration management) rather than treating `crontab -l` output as the only record — `crontab
-r` has no undo.

## Related Commands

- [`at`](at.md) — one-time scheduled jobs, rather than `crontab`'s recurring jobs
- [`anacron`](anacron.md) — recurring jobs on machines that aren't always powered on
- [`systemctl`](systemctl.md) — manages systemd timers, the modern alternative to `crontab`

## Related Concepts

- [crontab Syntax](../docs/17-cron/crontab-syntax.md)
- [crontab -e, -l, -r](../docs/17-cron/crontab-e-l-r.md)
- [System-wide cron](../docs/17-cron/system-wide-cron.md)
