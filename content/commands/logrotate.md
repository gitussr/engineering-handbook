---
title: "logrotate — Rotate, Compress, and Prune Log Files"
description: "Run rotation rules from /etc/logrotate.conf and /etc/logrotate.d/*, either as normally scheduled or manually for testing."
relatedConcepts: ["16-logs/logrotate", "16-logs/var-log-directory-structure"]
relatedCommands: ["journalctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["logrotate command examples", "logrotate -d dry run", "logrotate -f force", "logrotate.d config"]
canonicalUrl: "/commands/logrotate"
---

# logrotate

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `logrotate /etc/logrotate.conf` applies every configured rotation rule — normally run
> automatically once a day via cron/systemd timer. `logrotate -d` dry-runs a config without
> changing anything; `logrotate -f` forces rotation immediately, regardless of schedule.

## Purpose

`logrotate` rotates, compresses, and prunes log files per configured rules — see
[logrotate](../docs/16-logs/logrotate.md) for the full concept.

## Syntax

```
logrotate [OPTIONS] CONFIG_FILE
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `CONFIG_FILE` | Path to a logrotate config (commonly `/etc/logrotate.conf`, which includes `/etc/logrotate.d/*`) | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-d` | Debug/dry-run — show what would happen without doing it |
| `-f` | Force rotation now, ignoring the configured schedule/size threshold |
| `-v` | Verbose output |
| `-s FILE` | Use a specific state file instead of the default |

## Examples

```
$ sudo logrotate -d /etc/logrotate.d/myapp
```
Dry-run a specific rule to see what would happen, without changing anything.

```
$ sudo logrotate /etc/logrotate.conf
```
Apply every configured rule normally (what the scheduled cron/timer job actually runs).

```
$ sudo logrotate -f /etc/logrotate.d/myapp
```
Force immediate rotation for testing, regardless of the configured schedule.

## Expected Output

```
$ sudo logrotate -d /etc/logrotate.d/myapp
reading config file /etc/logrotate.d/myapp
Handling 1 logs

rotating pattern: /var/log/myapp/app.log  after 1 days (14 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/myapp/app.log
  log needs rotating
rotating log /var/log/myapp/app.log, log->rotateCount is 14
```

## Exit Status

`0` on success, non-zero if the config file has a syntax error or a rotation step fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `error: ... does not exist -- skipping` | Missing `missingok` directive and the log file genuinely doesn't exist | Add `missingok` to the config block |
| Rule doesn't rotate when expected | Rotation only happens when `logrotate` itself is run (via cron/timer) and the condition (age/size) is met | Confirm the scheduling job is actually running; use `-f` to test immediately |
| Permission errors on rotated files | Ownership/permission settings in the config don't match what the application expects | Add explicit `create MODE OWNER GROUP` in the config block |

## Security Considerations

Rotated log files often retain the same sensitive content as the original — ensure rotated/
compressed copies keep appropriately restrictive permissions, not just the active log file.

## Performance Considerations

Rotation itself is lightweight; `compress` trades a small amount of CPU time during rotation for
significant disk space savings on older, archived copies.

## Production Usage

`logrotate -d` is the standard, safe way to validate a new or modified configuration before
trusting it to run unattended on the normal daily schedule.

## Related Commands

- [`journalctl`](journalctl.md) — the equivalent size/retention management for the systemd
  journal, via `--vacuum-size`/`--vacuum-time`

## Related Concepts

- [logrotate](../docs/16-logs/logrotate.md)
- [/var/log Directory Structure](../docs/16-logs/var-log-directory-structure.md)
