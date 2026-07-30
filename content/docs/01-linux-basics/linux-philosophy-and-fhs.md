---
title: "Linux Philosophy and the Filesystem Hierarchy Standard"
description: "The design principles behind Linux, and a first look at the standard directory layout every distro follows."
module: "01-linux-basics"
moduleTitle: "Linux Basics"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["01-linux-basics/kernel-vs-os-vs-distro"]
relatedTopics: ["linux-in-industry"]
relatedCommands: ["ls"]
careerRelevance: ["devops", "linux-administrator", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#fhs"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "Filesystem Hierarchy Standard (FHS) 3.0 spec", "url": "https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html"}]
nextTopic: "01-linux-basics/linux-in-industry"
prevTopic: "01-linux-basics/gui-vs-cli"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["linux philosophy", "unix philosophy", "filesystem hierarchy standard", "fhs", "everything is a file"]
canonicalUrl: "/docs/linux-basics/linux-philosophy-and-fhs"
---

# Linux Philosophy and the Filesystem Hierarchy Standard

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Software Engineering

> **TL;DR:** Linux favors small, composable tools and treats almost everything as a file. The
> Filesystem Hierarchy Standard (FHS) is the shared directory layout (`/etc` = config, `/var` =
> logs/variable data, `/home` = user files) that makes every distro navigable the same way.

## What is it?

The Unix/Linux philosophy is a set of design principles — small tools that do one thing well,
composability via pipes, "everything is a file." The Filesystem Hierarchy Standard (FHS) is the
specification that defines where things live on disk so any Linux system is navigable the same
way.

## Why does it exist?

Without an agreed layout, every distro and every application would put files wherever it wanted,
making systems impossible to reason about or automate consistently. The FHS gives every distro
maintainer, sysadmin, and automation script a shared, predictable map of the filesystem.

## Where is it used?

Every single Linux system you will ever touch professionally follows this layout closely enough
that muscle memory transfers directly: config in `/etc`, logs in `/var/log`, user home
directories in `/home`, temporary files in `/tmp`. Deployment scripts, monitoring tools, and
backup systems all assume this structure.

## How it works

> 📊 Diagram: a simple tree diagram of `/` with its major children labeled — `/bin`, `/etc`,
> `/home`, `/var`, `/usr`, `/tmp`, `/opt`, `/proc`, `/sys` — each with a one-word purpose label.
> Full depth on each directory is covered in Module 05.

Two philosophy principles matter most in practice at this stage:

- **Everything is a file** — devices, processes, and configuration are all represented as files
  or file-like objects, which is why so many Linux tools work by reading/writing text.
- **Small tools, composed together** — `grep`, `sort`, `wc` etc. each do one job; you combine
  them with pipes rather than reaching for one giant tool. This shapes how Linux command-line
  work is done from Module 04 onward.

A quick preview of the top-level layout (full detail in [Module 05: File System](../05-file-system/index.md)):

| Directory | Purpose |
|---|---|
| `/etc` | System-wide configuration files |
| `/var` | Variable data — logs, caches, spool files |
| `/home` | Per-user personal directories |
| `/usr` | Installed software and its resources |
| `/bin`, `/sbin` | Essential command binaries |
| `/tmp` | Temporary files, cleared periodically |
| `/opt` | Optional/third-party software |
| `/proc`, `/sys` | Virtual filesystems exposing kernel/process info |

## Real-world example

A new hire is asked to find why an Nginx deployment is failing, on a server they've never logged
into before, at a company that's never onboarded them on "where things are." They don't need to
ask — FHS convention alone tells them to check `/etc/nginx/` for config and `/var/log/nginx/` for
logs. That predictability, not tribal knowledge, is what lets a new engineer be productive on day
one.

## Commands

- [`ls`](../../commands/ls.md) — list directory contents (full detail in Module 03/06)

## Production example

```
$ ls /etc/nginx/
nginx.conf  sites-available  sites-enabled  conf.d
$ ls /var/log/nginx/
access.log  error.log
```

No documentation needed to find these — FHS convention alone tells you where to look.

## Do / Don't

| Do | Don't |
|---|---|
| Put custom third-party software under `/opt/appname` | Invent a non-standard location for app data "temporarily" |
| Check `/etc/{service}/` and `/var/log/{service}/` first when debugging | Assume every distro deviates significantly from FHS |

## Common mistakes

- Putting application data or logs in non-standard locations "temporarily" — it breaks the
  assumptions every monitoring/backup tool makes and becomes permanent technical debt.
- Assuming every distro follows FHS with zero variation — most do closely, but some directories
  (e.g. `/opt` usage, `/srv`) vary slightly by convention; check when it matters.
- Confusing `/bin` and `/usr/bin` as meaningfully different on modern distros — on most current
  systems `/bin` is now a symlink to `/usr/bin` (unified `/usr` layout).

## Best practices

- When installing custom or third-party software manually, follow FHS conventions (`/opt/appname`
  is the standard place) rather than inventing your own layout.
- When debugging an unfamiliar service, check `/etc/{service}/` and `/var/log/{service}/` first —
  it's right most of the time.

## Exercises

1. List what you'd expect to find in `/etc`, `/var`, and `/home` without looking it up.
2. Explain the "everything is a file" principle in one sentence with an example.
3. Explain why a shared filesystem layout matters for automation scripts.

## Quiz

**Q: Where would you expect to find a web server's configuration file?**
<details><summary>Show answer</summary>
Under `/etc/` — e.g. `/etc/nginx/nginx.conf` for Nginx, `/etc/apache2/` for Apache.
</details>

**Q: What does "everything is a file" mean in Linux?**
<details><summary>Show answer</summary>
Devices, processes, and system information are represented through the filesystem (e.g. `/dev`,
`/proc`), so the same tools used to read/write regular files also work on hardware and kernel
data.
</details>

## Interview questions

- What is the FHS and why does it matter for automation? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- The Unix philosophy favors small, composable tools and treats almost everything as a file.
- FHS defines a standard directory layout followed closely by virtually every distro.
- `/etc` = config, `/var` = variable data/logs, `/home` = user files — memorize these three first.
- This predictability is what makes Linux automatable at scale.

## Further Reading

- [Filesystem Hierarchy Standard 3.0 specification](https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html)

## Related topics

- [Module 05: File System](../05-file-system/index.md) (full FHS depth)
- [Linux in Industry](linux-in-industry.md)
