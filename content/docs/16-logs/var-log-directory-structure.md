---
title: "/var/log Directory Structure"
description: "Where traditional, file-based application and system logs actually live, and how to recognize what a given log file is for."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/linux-logging-overview"]
relatedTopics: ["journalctl-deep-dive"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#var-log-layout"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "16-logs/journalctl-deep-dive"
prevTopic: "16-logs/linux-logging-overview"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["var log directory explained", "var log auth.log", "var log syslog", "var log dpkg log"]
canonicalUrl: "/docs/logs/var-log-directory-structure"
---

# /var/log Directory Structure

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cybersecurity

> **TL;DR:** `/var/log` is where traditional, file-based logs live — one recognizable file or
> subdirectory per service or subsystem (`auth.log`, `syslog`/`messages`, `nginx/`, `dpkg.log`).
> Confirmed as part of the FHS in [Module 05](../05-file-system/core-directories-explained.md);
> this topic is about what's actually inside it.

## What is it?

The conventional location for file-based system and application logs — the concrete filesystem
counterpart to the journal ([Module 11](../11-services/journalctl-basics.md)), for services and
tools that still log to plain files rather than (or in addition to) the systemd journal.

## Why does it exist?

Before systemd's centralized journal, and still today for many applications and tools, logging to
a plain, appendable file under a well-known, conventional location was (and remains) the standard
approach — `/var/log` is that convention, letting anyone administering a Linux system know where
to look regardless of which specific application produced the log.

## Where is it used?

Investigating authentication attempts, package manager history, web server access/error logs, and
any application that logs to a file rather than exclusively through the journal.

## How it works

> 📊 Diagram: `/var/log` shown as a directory tree with several recognizable entries — `auth.log`
> (or `secure`), `syslog` (or `messages`), a `nginx/` subdirectory with `access.log`/`error.log`,
> and `dpkg.log` — each annotated with what actually gets logged there.

| Path | Typically contains |
|---|---|
| `/var/log/auth.log` (Debian/Ubuntu) or `/var/log/secure` (RHEL family) | Authentication attempts, `sudo` usage |
| `/var/log/syslog` (Debian/Ubuntu) or `/var/log/messages` (RHEL family) | General system messages (this module's next-but-one topic explains the mechanism) |
| `/var/log/nginx/access.log`, `/var/log/nginx/error.log` | Web server request and error logs |
| `/var/log/dpkg.log` / `/var/log/yum.log` | Package manager install/update history ([Module 14](../14-package-managers/index.md)) |

**Naming differs by distro family**: the same category of information (authentication events,
general system messages) commonly lands in a differently-named file on Debian/Ubuntu versus
RHEL-family distros — worth confirming on an unfamiliar system rather than assuming a name.

## Real-world example

A security review needs to check for repeated failed SSH login attempts on a fleet of mixed
Debian and RHEL-family servers. The investigation checks `/var/log/auth.log` on the Debian/Ubuntu
hosts and `/var/log/secure` on the RHEL-family hosts — the same category of information, in a
differently-named file depending on distro family, exactly the kind of detail this topic exists
to make explicit rather than assumed.

## Commands

No new command — files under `/var/log` are read with `cat`/`less`/`tail`, already covered in
[Module 06](../06-files/viewing-files.md); what's new here is knowing what to look for and where.

## Production example

```
$ ls /var/log
auth.log  syslog  nginx/  dpkg.log  ...

$ tail -20 /var/log/auth.log
Jul 27 09:12:03 web-01 sshd[4521]: Failed password for invalid user admin from 203.0.113.9 port 51422 ssh2
```

## Do / Don't

| Do | Don't |
|---|---|
| Confirm the actual file/path names on a given distro before assuming | Assume `/var/log/auth.log` exists identically on every distro family |
| Check `/var/log` for file-based logs in addition to the journal | Rely exclusively on the journal and forget applications may log to plain files too |
| Recognize a log file's purpose from its name/location before reading it | Grep blindly through every file in `/var/log` without narrowing down first |

## Common mistakes

- Assuming a specific log file name (`auth.log`) is universal across all distros, missing that
  RHEL-family systems use a different name (`secure`) for the same category of information.
- Checking only the journal and forgetting that many applications still log to plain files under
  `/var/log` independently.
- Searching through every file in `/var/log` indiscriminately instead of narrowing to the file
  most likely to contain the relevant information first.

## Best practices

- Learn the distro-specific naming for common logs (`auth.log`/`secure`, `syslog`/`messages`)
  rather than assuming one convention universally.
- Check both `/var/log` and the journal when investigating an issue — different tools and
  applications may log to either or both.
- Narrow to the most relevant file first based on what's being investigated, rather than
  searching everything.

## Exercises

1. List the contents of `/var/log` on a system you have access to and identify at least three
   recognizable log files or subdirectories.
2. Check whether authentication logs are named `auth.log` or `secure` on that system.
3. Explain in one sentence why log file naming can differ between distro families.

## Quiz

**Q: Where do authentication attempts typically get logged on Debian/Ubuntu versus a RHEL-family
distro?**
<details><summary>Show answer</summary>
`/var/log/auth.log` on Debian/Ubuntu; `/var/log/secure` on RHEL/CentOS/Rocky and similar.
</details>

**Q: Why might an investigation need to check both `/var/log` and the systemd journal?**
<details><summary>Show answer</summary>
Some applications and subsystems log to plain files under `/var/log`, while systemd-managed
services are centrally captured in the journal — either or both may hold relevant information
depending on what's being investigated.
</details>

## Interview questions

- Where would you look for failed SSH login attempts on a Debian system versus a RHEL-family
  system? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/var/log` is the conventional home for file-based system and application logs.
- Common log names differ by distro family (`auth.log`/`secure`, `syslog`/`messages`).
- Applications that log to plain files complement, rather than replace, the systemd journal.
- Knowing what a log file is for before reading it saves time during an investigation.

## Related topics

- [journalctl Deep Dive](journalctl-deep-dive.md)
- [Module 05: Core Directories Explained](../05-file-system/core-directories-explained.md)
