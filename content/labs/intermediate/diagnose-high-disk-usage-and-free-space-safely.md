---
title: "Lab: Diagnose High Disk Usage and Free Space Safely"
description: "An intermediate Linux lab, framed as a real incident ticket: a server is nearly out of disk space, and you have to find the actual cause and free space without deleting the wrong thing."
type: "lab"
tier: "intermediate"
careerRelevance: ["linux-administrator", "devops", "sre"]
updatedAt: "2026-07-30"
keywords: ["disk usage lab linux", "df du exercise", "free disk space safely lab", "intermediate linux lab storage"]
canonicalUrl: "/labs/intermediate/diagnose-high-disk-usage-and-free-space-safely"
---

# Lab: Diagnose High Disk Usage and Free Space Safely

🟡 Intermediate · Relevant for: Linux Administrator, DevOps, SRE

## Scenario

**Ticket #LX-203:** "Monitoring just alerted that `/` is at 94% disk usage on the app server. We
don't know what's eating the space. Find out what's consuming it and free up space — but don't
just start deleting things, some of what's on there matters."

## Environment Setup

Reproduce a disk-usage puzzle yourself:

```bash
sudo mkdir -p /var/log/bigapp /opt/old-releases
sudo dd if=/dev/zero of=/var/log/bigapp/debug.log bs=1M count=200 2>/dev/null
sudo dd if=/dev/zero of=/opt/old-releases/release-v1.tar.gz bs=1M count=150 2>/dev/null
sudo dd if=/dev/zero of=/opt/old-releases/release-v2.tar.gz bs=1M count=150 2>/dev/null
```

## Tasks

1. Confirm overall disk usage and identify which filesystem is actually the problem.
2. Narrow down which top-level directories are consuming the most space.
3. Drill in further to find the specific largest files/directories.
4. Free space by removing or archiving only what's genuinely safe to remove — the oversized debug
   log is a clear candidate; the old release archives need a judgment call, not an automatic delete.
5. Confirm the disk usage percentage actually drops after cleanup.

## Hints

<details><summary>Show hint</summary>
`df -h` tells you *that* a filesystem is full; `du -sh */` from a likely starting point (like
`/var` or `/opt`) tells you *what's* filling it. Work top-down rather than guessing a path.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Confirm which filesystem is full
df -h
# Filesystem   Size  Used Avail Use% Mounted on
# /dev/sda1     20G   19G  1.0G  95% /

# Narrow down from the top
sudo du -sh /var/* /opt/* 2>/dev/null | sort -rh | head -10
# 200M    /var/log/bigapp
# 300M    /opt/old-releases

# Drill into the largest offender
sudo du -sh /var/log/bigapp/* /opt/old-releases/* 2>/dev/null | sort -rh

# The oversized debug log is safe to truncate (not delete the file — a process may have it open):
sudo truncate -s 0 /var/log/bigapp/debug.log

# The old release archives are a judgment call — confirm with the team/README whether they're
# still needed before deleting; for this lab, assume only the oldest is safe to remove:
sudo rm /opt/old-releases/release-v1.tar.gz

# Confirm the improvement
df -h
```

The key judgment call: a bloated log file is almost always safe to truncate, but old release
archives, database files, or anything that looks intentional deserves a second look — "big" and
"safe to delete" are not the same thing.
</details>

## What You Learned

- The `df` → `du -sh */` → drill-down workflow for finding what's actually consuming disk space.
- Why `truncate` is often safer than `rm` for an oversized log a process might still have open.
- That freeing disk space is a judgment call, not a mechanical "delete the biggest files" exercise.

## Related Modules

- [Storage](../../docs/15-storage/index.md)

## Related topics

- [Linux Commands Cheat Sheet](../../cheatsheets/linux-commands.md)
- [Scenario-Based Interview Questions: disk full troubleshooting](../../interview-questions/scenario-based.md#disk-full-troubleshooting-steps)
- [Labs Hub](../index.md)
