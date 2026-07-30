---
title: "Linux Logging Overview"
description: "Why logs exist at all, the layers that produce them, and the mental model every other topic in this module builds on."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/swap-space"]
relatedTopics: ["var-log-directory-structure"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#why-logging-matters"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "16-logs/var-log-directory-structure"
prevTopic: "15-storage/swap-space"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["linux logging explained", "log levels explained", "kernel vs application logs", "why logs matter"]
canonicalUrl: "/docs/logs/linux-logging-overview"
---

# Linux Logging Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cybersecurity

> **TL;DR:** Logs are the recorded history of what a system and its services actually did — the
> primary evidence for diagnosing a failure after the fact, since you usually can't watch an
> incident happen live. Linux logging spans at least three layers: kernel messages, systemd's
> journal, and individual application log files.

## What is it?

The recorded, timestamped history of events produced by the kernel, systemd, and individual
applications — the foundation every later topic in this module (the journal, `/var/log`, syslog,
log rotation, centralized logging, and incident response) builds directly on.

## Why does it exist?

Almost nothing important happens at a moment convenient for a human to be watching. Logs exist so
that whatever happened — a service crash, a failed login, a slow request — can be reconstructed
and understood afterward, from the recorded evidence, rather than requiring someone to have
witnessed it live.

## Where is it used?

Every production incident investigation, every security audit, every "why did this happen"
question about a system — logging is the evidence layer underneath nearly all of them.

## How it works

> 📊 Diagram: three layers producing log data simultaneously — the kernel (hardware events,
> driver messages), systemd (service lifecycle events, captured centrally in the journal,
> [Module 11](../11-services/journalctl-basics.md)), and individual applications (writing their
> own log files, commonly under `/var/log`) — all three feeding into the broader logging
> ecosystem this module covers.

| Layer | Produces | Where it typically lands |
|---|---|---|
| Kernel | Hardware, driver, and low-level system events | `dmesg`, journal |
| systemd | Service start/stop/failure events, captured centrally | The journal ([Module 11](../11-services/journalctl-basics.md)) |
| Applications | Their own request logs, error logs, audit trails | Often `/var/log/`, this module's next topic |

**Log levels/priorities**: most logging systems categorize messages by severity (commonly:
debug, info, warning, error, critical) — letting a reader filter to only what matters (e.g. errors
and above) rather than wading through routine informational noise.

## Real-world example

A service crashes overnight with no one watching. The next morning, an engineer reconstructs
exactly what happened purely from logs: the journal shows the service's exit, an application log
shows the specific error that preceded it, and a kernel message (checked via `dmesg`) reveals the
underlying cause was an out-of-memory condition — a complete picture assembled entirely after the
fact, from recorded evidence, with nobody having witnessed the crash occur.

## Commands

No command example on this page — this page is conceptual foundation. See
[/var/log Directory Structure](var-log-directory-structure.md) for the first hands-on look in
this module.

## Production example

Not applicable — see [/var/log Directory Structure](var-log-directory-structure.md) for the
first terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Treat logs as the primary evidence source for any "what happened" investigation | Assume you'll be able to observe every incident live |
| Understand which layer (kernel, systemd, application) is likely to have recorded a given event | Only check one log source and assume it has the full picture |
| Use log levels to filter to what matters first | Read every log line at every severity indiscriminately |

## Common mistakes

- Assuming a single log source has the complete picture of an incident, missing that kernel,
  systemd, and application logs each capture different, complementary information.
- Not knowing which layer is likely to have recorded a specific kind of event, wasting time
  looking in the wrong place first.
- Ignoring log levels entirely and reading everything indiscriminately instead of filtering by
  severity first.

## Best practices

- Build a mental map of which layer (kernel, systemd, application) is likely to have recorded a
  given kind of event, to know where to look first.
- Filter by log level/priority before reading raw output line by line.
- Treat logging as the default way incidents get understood after the fact, not an afterthought.

## Exercises

1. Explain in one sentence why logs matter even when nobody witnessed an incident occur.
2. Name the three layers of Linux logging introduced on this page.
3. Describe one reason a "why did this crash" investigation might need more than one log source.

## Quiz

**Q: Why do logs matter even for incidents nobody was watching happen live?**
<details><summary>Show answer</summary>
Logs are the recorded evidence that lets an incident be reconstructed and understood after the
fact, rather than requiring someone to have witnessed it as it happened.
</details>

**Q: What are the three layers of Linux logging introduced in this overview?**
<details><summary>Show answer</summary>
The kernel (hardware/driver events), systemd (service lifecycle events, centralized in the
journal), and individual applications (their own log files, often under `/var/log`).
</details>

## Interview questions

- Why is logging considered a critical part of production system design? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Logs are the recorded evidence used to reconstruct what happened after an incident.
- Linux logging spans at least three layers: kernel, systemd (journal), and application logs.
- Log levels/priorities let a reader filter to what actually matters.
- Knowing which layer likely recorded an event saves time during an investigation.

## Related topics

- [/var/log Directory Structure](var-log-directory-structure.md)
- [Module 11: journalctl Basics](../11-services/journalctl-basics.md)
