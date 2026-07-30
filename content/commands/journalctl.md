---
title: "journalctl — Query the systemd Journal"
description: "Read, filter, and follow logs captured by systemd for any managed unit — plus disk usage management, persistence, and inspecting past boots."
relatedConcepts: ["11-services/journalctl-basics", "16-logs/journalctl-deep-dive"]
relatedCommands: ["systemctl", "logrotate"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
updatedAt: "2026-07-27"
keywords: ["journalctl command", "journalctl -u -f", "journalctl since until", "journalctl priority", "journalctl disk usage", "journalctl vacuum-size"]
canonicalUrl: "/commands/journalctl"
---

# journalctl

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `journalctl -u name -f` is the systemd-era `tail -f` for a specific service's logs.
> Add `--since`/`-p` to narrow by time or severity.

## Purpose

`journalctl` queries systemd's centralized journal — see
[journalctl Basics](../docs/11-services/journalctl-basics.md).

## Syntax

```
journalctl [OPTIONS]
```

## Arguments

None — entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `-u UNIT` | Filter to a specific unit |
| `-f` | Follow live (like `tail -f`) |
| `-b [N]` | Show logs from a specific boot (omitted/`0` = current, `-1` = previous, etc.) |
| `--since "TIME"` / `--until "TIME"` | Filter by time range |
| `-p LEVEL` | Filter by minimum priority (e.g. `err`, `warning`) |
| `-n N` | Show only the last N lines |
| `-r` | Reverse order, newest first |
| `--list-boots` | List every recorded boot |
| `--disk-usage` | Show total disk space used by the journal |
| `--vacuum-size=SIZE` / `--vacuum-time=TIME` | Shrink the journal to a size or age limit |
| `-o FORMAT` | Output format (`json`, `json-pretty`, `cat`, etc.) |
| `-k` | Show only kernel messages |

## Examples

```
$ journalctl -u nginx
```
Show all logged output for the `nginx` unit.

```
$ journalctl -u nginx -f
```
Follow live.

```
$ journalctl -u nginx --since "2026-07-25 09:00" --until "2026-07-25 10:00"
```
Show a specific time window.

```
$ journalctl -p err -b
```
Show error-and-above priority logs from the current boot, across all units.

```
$ journalctl -b -1 -u nginx
```
Show `nginx`'s logs from the *previous* boot — see
[journalctl Deep Dive](../docs/16-logs/journalctl-deep-dive.md).

```
$ journalctl --vacuum-size=500M
```
Shrink the journal down to 500MB, freeing disk space.

## Expected Output

```
$ journalctl -u nginx -n 3
Jul 25 09:00:12 web-01 systemd[1]: Started nginx.
Jul 25 09:00:12 web-01 nginx[1234]: nginx started successfully
Jul 25 09:15:03 web-01 nginx[1234]: worker process started
```

## Exit Status

`0` on success, non-zero if the journal can't be read or invalid options are given.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `No entries` | The unit name is wrong, or genuinely has no logged output in that range | Verify the exact unit name with `systemctl list-units` |
| `Failed to determine timestamp` | Invalid `--since`/`--until` format | Use a format like `"2026-07-25 09:00"` or relative terms like `"1 hour ago"` |
| Permission denied reading some entries | Regular users may see restricted journal access depending on configuration | Use `sudo journalctl` |

## Security Considerations

Journal entries can contain sensitive information depending on what services log — treat journal
access with the same care as any other log data when working in security-sensitive contexts.

## Performance Considerations

The journal can grow large over time; systemd manages retention automatically (configurable), but
very broad, unfiltered queries (`journalctl` with no flags) against a large journal can be slow —
scope queries with `-u`, `--since`, or `-n` when possible. Use `--disk-usage` and
`--vacuum-size`/`--vacuum-time` proactively to keep journal disk usage under control — see
[journalctl Deep Dive](../docs/16-logs/journalctl-deep-dive.md).

## Production Usage

`journalctl -u servicename -f` run in one terminal while restarting or deploying that service in
another is one of the most common live-troubleshooting patterns in systemd-managed environments.

## Related Commands

- [`systemctl`](systemctl.md) — `status` shows a brief log excerpt; `journalctl` gives the full,
  filterable view
- [`logrotate`](logrotate.md) — the equivalent size/retention management for traditional,
  file-based logs under `/var/log`

## Related Concepts

- [journalctl Basics](../docs/11-services/journalctl-basics.md)
- [journalctl Deep Dive](../docs/16-logs/journalctl-deep-dive.md)
