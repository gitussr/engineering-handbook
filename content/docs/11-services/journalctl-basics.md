---
title: "journalctl Basics"
description: "systemd's centralized log — one command to read logs for any service, live-follow them, or filter by time, instead of hunting through separate log files."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["11-services/writing-a-systemd-unit-file"]
relatedTopics: ["service-dependencies-and-targets"]
relatedCommands: ["journalctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#journalctl-follow"]
relatedCheatsheet: "systemctl"
furtherReading: []
nextTopic: "11-services/service-dependencies-and-targets"
prevTopic: "11-services/writing-a-systemd-unit-file"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["journalctl basics", "journalctl -u", "journalctl -f", "journalctl since"]
canonicalUrl: "/docs/services/journalctl-basics"
---

# journalctl Basics

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `journalctl -u servicename` shows that service's logs. Add `-f` to follow live
> (like `tail -f`, Module 06). systemd captures logs from every service it manages centrally —
> `journalctl` is how you read them, instead of hunting for a separate log file per service.

## What is it?

`journalctl` reads systemd's journal — a centralized, structured log store that systemd
automatically captures output into for every service it manages, replacing the need to locate a
separate log file for each one.

## Why does it exist?

Before systemd, every daemon wrote logs wherever it chose (usually somewhere under `/var/log`,
Module 05), with inconsistent formats and no unified way to query across them. systemd captures
stdout/stderr from every service it manages directly into one structured journal, queryable with
one consistent tool regardless of which service you're investigating.

## Where is it used?

Every service troubleshooting session — checking why a service failed to start, watching live
output during a deployment, or correlating events across multiple services around the same time.

## How it works

> 📊 Diagram: several services shown each writing output into one central "systemd journal"
> store, with `journalctl` as the single query tool reading from it, filtered by unit name, time
> range, or boot — contrasted with the pre-systemd model of separate log files per service.

| Flag | Effect |
|---|---|
| `-u NAME` | Show logs for a specific unit only |
| `-f` | Follow live, like `tail -f` (Module 06) |
| `-b` | Show logs since the current boot only |
| `--since "TIME"` | Show logs since a specific time |
| `-p err` | Show only error-priority and above |
| `-n N` | Show only the last N lines |

`journalctl -u nginx -f` is the direct systemd-era equivalent of `tail -f
/var/log/nginx/error.log` — same live-watching workflow from Module 06, applied to the journal
instead of a plain file.

## Real-world example

A service fails to start after a deployment. `systemctl status` (already covered) shows a
truncated error, but `journalctl -u myapp -n 50` shows the full recent log output, revealing the
actual startup exception — the full-context view `status`'s brief summary alone doesn't provide.

## Commands

- [`journalctl`](../../commands/journalctl.md) — full syntax and examples

## Production example

```
$ journalctl -u nginx -n 50
$ journalctl -u nginx -f
$ journalctl -u nginx --since "1 hour ago" -p err
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `journalctl -u name` to scope to a specific service immediately | Scroll through the entire unfiltered journal looking for one service's entries |
| Use `-f` to watch live output during a deployment or restart | Repeatedly re-run `journalctl` to check for new entries |
| Use `--since`/`-p` to narrow a large journal down to what matters | Read through hours of unfiltered logs manually |

## Common mistakes

- Not scoping with `-u`, producing an overwhelming, unfiltered view of every unit's logs mixed
  together.
- Forgetting `-f` exists and manually re-running `journalctl` repeatedly instead of following
  live, missing the `tail -f`-equivalent convenience.
- Not using `--since`/`-p err` to narrow scope, wasting time scrolling through irrelevant entries.

## Best practices

- Default to `journalctl -u name` as the starting point for any service-specific investigation.
- Use `-f` for live deployment/restart watching, the same instinct as `tail -f` from Module 06.
- Combine `--since` and `-p` to narrow quickly when investigating a specific time window or
  severity.

## Exercises

1. Run `journalctl -u` against any running service and read its recent log output.
2. Follow a service's logs live with `-f` while restarting it in another terminal.
3. Filter a service's logs to only the last hour with `--since`.

## Quiz

**Q: What's the journalctl equivalent of `tail -f` on a specific service's logs?**
<details><summary>Show answer</summary>
`journalctl -u servicename -f`
</details>

**Q: Why does journalctl exist instead of just using separate log files per service?**
<details><summary>Show answer</summary>
systemd captures output from every service it manages into one centralized, structured journal,
queryable consistently regardless of which service you're investigating — replacing inconsistent
per-service logging locations and formats.
</details>

## Interview questions

- How would you view the last hour of error-level logs for a specific systemd service? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `journalctl -u name` scopes to a specific service; `-f` follows live.
- systemd centralizes logging for every managed service into one structured journal.
- `--since` and `-p` narrow a large journal down to a specific time window or severity.
- This is the direct systemd-era counterpart to Module 06's `tail -f` habit.

## Related topics

- [Service Dependencies and Targets](service-dependencies-and-targets.md)
- [Module 16: Logs](../16-logs/index.md)
