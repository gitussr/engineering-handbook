---
title: "Core Directories Explained"
description: "What actually belongs in /etc, /var, /home, /usr, /bin, /opt, /tmp, /proc, and /sys — the full reference Modules 01 and 03 pointed here for."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["05-file-system/fhs-deep-dive"]
relatedTopics: ["filesystem-types", "proc-and-sys-virtual-filesystems"]
relatedCommands: ["ls"]
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#etc-vs-var-vs-usr"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "05-file-system/filesystem-types"
prevTopic: "05-file-system/fhs-deep-dive"
estimatedReadingTime: 8
updatedAt: "2026-07-25"
keywords: ["etc directory linux", "var directory linux", "usr vs bin linux", "opt directory linux", "tmp vs var tmp"]
canonicalUrl: "/docs/file-system/core-directories-explained"
---

# Core Directories Explained

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `/etc` = config, `/var` = data that changes (logs, caches, databases), `/home` = user
> files, `/usr` = installed software, `/bin`+`/sbin` = essential binaries (now usually symlinked
> into `/usr`), `/opt` = third-party/manually-installed software, `/tmp` = scratch space cleared
> on reboot. `/proc` and `/sys` get their own dedicated page.

## What is it?

The full-depth reference for the top-level directories previewed in
[Linux Philosophy and the FHS](../01-linux-basics/linux-philosophy-and-fhs.md) — what actually
lives in each one, and the distinctions that matter in daily professional work.

## Why does it exist?

Knowing a directory's *name* isn't the same as knowing what actually belongs there and why —
several of these directories have easily-confused near-duplicates (`/tmp` vs `/var/tmp`, `/bin`
vs `/usr/bin`, `/etc` vs `/opt`) where picking the wrong one is a real, recurring mistake.

## Where is it used?

Every deployment, every debugging session, every piece of automation touches these directories
directly. This reference is what you come back to whenever "where should this file actually go"
comes up.

## How it works

> 📊 Diagram: a full annotated tree of `/` with each directory covered below labeled with a
> one-line purpose and its most common gotcha, so the whole reference is visible at a glance.

| Directory | Contains | Common gotcha |
|---|---|---|
| `/etc` | System-wide configuration files, plain text, no binaries | Deeply nested by application (`/etc/nginx/`, `/etc/ssh/`) — always check the app's own subdirectory first |
| `/var` | Data that changes at runtime: logs (`/var/log`), caches, spool files, some databases (`/var/lib`) | Can fill up disk if logs aren't rotated — see [Module 16: Logs](../16-logs/index.md) |
| `/home` | Per-user personal directories (`/home/username`) | Root's home is `/root`, not `/home/root` — a common beginner mix-up |
| `/usr` | Installed software and its supporting files — the bulk of a typical system's files live here | On modern distros, `/bin` and `/sbin` are symlinks into `/usr/bin`/`/usr/sbin` (the "unified /usr" layout) |
| `/bin`, `/sbin` | Essential command binaries needed even in single-user/rescue mode | Historically separate from `/usr/bin`; now usually merged via symlink on modern distros |
| `/opt` | Third-party or manually-installed software, self-contained under `/opt/vendorname/` | Not for anything installed via the distro's package manager — that goes through `/usr` |
| `/tmp` | Temporary files, typically cleared on reboot | Different from `/var/tmp`, which persists across reboots — don't assume `/tmp` survives a restart |
| `/proc`, `/sys` | Virtual filesystems exposing live kernel/process information — nothing here is real files on disk | Full coverage in [/proc and /sys Virtual Filesystems](proc-and-sys-virtual-filesystems.md) |

## Real-world example

A developer manually installs a third-party monitoring agent that didn't come from the distro's
package manager. Following FHS convention, it goes under `/opt/monitoring-agent/` rather than
being scattered into `/usr` (reserved for package-manager-installed software) — this keeps a
clean line between "what apt/dnf manages" and "what was installed by hand," which matters
enormously the next time someone tries to clean up or audit what's on the system.

## Commands

- [`ls`](../../commands/ls.md) — inspect any of these directories directly

## Production example

```
$ ls /etc/nginx/
nginx.conf  sites-available  sites-enabled  conf.d
$ ls /var/log/nginx/
access.log  error.log
$ ls /opt/
monitoring-agent
```

Three directories, three different purposes, all confirmed in seconds with the same command.

## Do / Don't

| Do | Don't |
|---|---|
| Put manually-installed third-party software under `/opt/vendorname/` | Scatter manual installs into `/usr` alongside package-managed software |
| Remember root's home is `/root`, not `/home/root` | Assume every user, including root, lives under `/home` |
| Treat `/tmp` as cleared on reboot | Store anything in `/tmp` you need to survive a restart — use `/var/tmp` instead |

## Common mistakes

- Confusing `/tmp` (often cleared on reboot) with `/var/tmp` (persists across reboots) and losing
  data unexpectedly.
- Looking for root's home directory in `/home/root` instead of `/root`.
- Installing third-party software directly into `/usr` by hand, muddying the line between what
  the package manager controls and what was installed manually.
- Not checking `/var/log` disk usage until it's already caused an outage — see
  [Module 16: Logs](../16-logs/index.md) for log rotation.

## Best practices

- Use `/opt/vendorname/` for anything installed outside the package manager.
- Treat `/tmp` as ephemeral by default; use `/var/tmp` explicitly when persistence across reboots
  actually matters.
- When investigating an unfamiliar service, check `/etc/{service}/` and `/var/log/{service}/`
  first — this reference is what tells you those are the right places to look.

## Exercises

1. List the contents of `/etc`, `/var`, and `/opt` on any Linux system and identify at least one
   real example in each.
2. Explain in one sentence the difference between `/tmp` and `/var/tmp`.
3. Explain why `/bin` and `/usr/bin` are often the same thing on a modern distro.

## Quiz

**Q: Where does root's home directory live?**
<details><summary>Show answer</summary>
`/root` — not `/home/root`, a common beginner assumption.
</details>

**Q: Where should manually-installed, non-package-manager software go?**
<details><summary>Show answer</summary>
`/opt/vendorname/` — keeping it separate from `/usr`, which is reserved for package-manager-installed software.
</details>

## Interview questions

- What's the difference between `/tmp` and `/var/tmp`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/etc` = config, `/var` = changing data, `/home` = user files, `/usr` = installed software,
  `/opt` = manual/third-party installs, `/tmp` = ephemeral scratch space.
- Root's home is `/root`, not under `/home`.
- `/tmp` and `/var/tmp` differ specifically in reboot persistence — a real, common mix-up.
- `/proc` and `/sys` are different enough (virtual, not real files) to warrant their own page.

## Related topics

- [Filesystem Types](filesystem-types.md)
- [/proc and /sys Virtual Filesystems](proc-and-sys-virtual-filesystems.md)
- [Module 16: Logs](../16-logs/index.md)
