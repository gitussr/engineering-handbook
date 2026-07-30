---
title: "Disk Full Issues"
description: "How to diagnose a full disk systematically — which filesystem, which directory, which file — and free space safely without deleting something that matters."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/disk-usage-df-du", "33-troubleshooting/troubleshooting-methodology"]
relatedTopics: ["33-troubleshooting/high-cpu-memory-issues"]
relatedCommands: ["df", "du"]
careerRelevance: ["linux-administrator", "sre", "devops"]
relatedLabs: ["intermediate/diagnose-high-disk-usage-and-free-space-safely", "production/recover-a-cloud-instance-from-a-full-disk"]
relatedInterviewQuestions: ["scenario-based#disk-full-troubleshooting-steps"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "33-troubleshooting/high-cpu-memory-issues"
prevTopic: "33-troubleshooting/troubleshooting-methodology"
estimatedReadingTime: 7
updatedAt: "2026-07-30"
keywords: ["disk full linux troubleshooting", "no space left on device", "free up disk space linux", "diagnose disk usage"]
canonicalUrl: "/docs/troubleshooting/disk-full-issues"
---

# Disk Full Issues

🟢 Must Know · Relevant for: Linux Administrator · SRE · DevOps

> **TL;DR:** Work top-down — `df -h` tells you *which* filesystem is full, `du -sh */` from a
> likely directory tells you *what's* filling it. Free space by removing or truncating what's
> genuinely safe, never by guessing at the biggest files.

## What is it?

A systematic approach to answering two questions in order: which filesystem is actually full, and
what specifically is consuming the space — before freeing anything.

## Why does it exist?

A full disk breaks things in ways that don't look like a disk problem at first: writes fail,
services crash on startup, even SSH sessions can become unstable if the disk is completely full.
Left undiagnosed, the instinct to delete "big-looking" files can destroy something that matters —
an active database file, a log a process still has open, a release archive nobody documented as
safe to remove.

## Where is it used?

Any alert or symptom tracing back to `No space left on device`, a service failing to write a log
or temp file, or a monitoring alert on filesystem usage percentage.

## How it works

> 📊 Diagram: a drill-down flowchart — `df -h` at the top identifying the full filesystem, an
> arrow down to `du -sh /var/* /opt/* ...` (top-level directories), then another arrow down into
> the largest directory found, ending at specific files — each level narrowing from "which
> filesystem" to "which exact file."

1. **Confirm which filesystem is full** with `df -h` — a server can have multiple mounted
   filesystems, and only one of them may actually be the problem.
2. **Narrow down top-level directories** with `du -sh /path/* 2>/dev/null | sort -rh | head -10`
   starting from a likely root (`/var`, `/opt`, `/home`) rather than guessing.
3. **Drill into the largest offender** the same way, one level deeper, until you find specific
   files or directories responsible.
4. **Decide what's actually safe to remove** — an oversized, still-growing log is usually safe to
   truncate; anything that looks intentional (release archives, database files, backups) needs a
   judgment call, not an automatic delete.
5. **Confirm the fix** by re-running `df -h` and seeing the percentage actually drop.

## Real-world example

A monitoring alert fires at 94% disk usage on an app server. The fast, wrong move is deleting the
largest file found by a single `du` command without checking what it is. The right move: `df -h`
confirms `/` is the full filesystem, `du -sh /var/* /opt/*` shows an oversized debug log and a
directory of old release archives are the two largest consumers — the log is safely truncated,
but the archives get a quick check with the team before anything is deleted, since one of them
turns out to be a required rollback target for the current release.

## Commands

See [`df`](../../commands/df.md) and [`du`](../../commands/du.md) for the exact flags used
throughout this page.

## Production example

```
$ df -h
Filesystem   Size  Used Avail Use% Mounted on
/dev/sda1     20G   19G  1.0G  94% /

$ sudo du -sh /var/* /opt/* 2>/dev/null | sort -rh | head -5
1.2G    /var/log
800M    /opt/old-releases
150M    /var/lib/docker

$ sudo du -sh /var/log/* 2>/dev/null | sort -rh | head -5
900M    /var/log/app-debug.log

$ sudo truncate -s 0 /var/log/app-debug.log
$ df -h
Filesystem   Size  Used Avail Use% Mounted on
/dev/sda1     20G   18G  2.0G  90%   /
```

Truncating (not deleting) the log file matters if a running process still has it open — deleting
the file wouldn't free space until that process closes or restarts, while truncating frees it
immediately.

## Do / Don't

| Do | Don't |
|---|---|
| Start with `df -h` to confirm which filesystem is actually full | Assume `/` is the problem without checking |
| Drill down with `du -sh */` from a likely directory | Run one broad `du -sh /` and wait minutes for a tree you can't act on |
| Truncate an oversized log a process may have open, rather than deleting it | `rm` a file a running process still holds — space isn't freed until the process closes it |
| Treat archives/backups as a judgment call | Delete anything just because it's large |

## Common mistakes

- Deleting the largest file found without checking what it is — "large" and "safe to delete" are
  not the same thing.
- Running `rm` on a log file a process still has open, then being confused that disk usage didn't
  actually drop.
- Checking only `/` when a server has multiple mounted filesystems and a different one is the
  actual problem.
- Freeing space without addressing why it filled up — the same alert fires again days later.

## Best practices

- Work top-down: `df` first, then `du` drilling from the top, never guessing a path directly.
- Prefer `truncate -s 0` over `rm` for an oversized log file that a process may still have open.
- Set a disk-usage alert well below 100% (e.g. 80%) so this is a scheduled cleanup, not a live
  incident.
- Once resolved, add log rotation or a retention policy if the root cause was unbounded log growth.

## Exercises

1. Fill a test file with `dd if=/dev/zero of=/tmp/testfile bs=1M count=500` and practice the
   `df` → `du` drill-down to find and remove it.
2. Explain in one sentence why `truncate` can be safer than `rm` for a large, actively-written log file.
3. Write the one-line `du` command you'd run to rank the top 10 largest directories under `/var`.

## Quiz

**Q: What's the first command to run when a disk-full alert fires, and why?**
<details><summary>Show answer</summary>
`df -h` — it tells you which specific mounted filesystem is actually full, since a server can have
several, and there's no point drilling into the wrong one.
</details>

**Q: Why might `rm`-ing a large log file not immediately free disk space?**
<details><summary>Show answer</summary>
If a running process still has the file open, the space isn't released until that process closes
the file handle (often only on restart) — `truncate -s 0` frees the space immediately instead.
</details>

## Interview questions

- A server alerts on 100% disk usage but you don't know what's consuming the space — walk through
  your diagnostic steps. → [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#disk-full-troubleshooting-steps)

## Key Takeaways

- `df -h` identifies which filesystem is full; `du -sh */` drilling down identifies what's filling it.
- Truncate a log a process may have open rather than deleting it.
- Treat backups/archives as a judgment call, not an automatic delete target.
- Fix the root cause (usually unbounded log growth) after freeing space, not just the symptom.

## Related topics

- [High CPU / Memory Issues](high-cpu-memory-issues.md)
- [Storage](../15-storage/index.md)
- [Cron (log rotation scheduling)](../17-cron/index.md)
