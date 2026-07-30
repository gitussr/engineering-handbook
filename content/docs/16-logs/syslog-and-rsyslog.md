---
title: "syslog and rsyslog"
description: "The older, protocol-based logging standard many /var/log files are still populated by — facility, severity, and how a message gets routed to a specific file."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/journalctl-deep-dive"]
relatedTopics: ["var-log-directory-structure"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#syslog-facility-severity"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "16-logs/logrotate"
prevTopic: "16-logs/journalctl-deep-dive"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["syslog explained", "rsyslog conf explained", "syslog facility severity", "rsyslog vs journald"]
canonicalUrl: "/docs/logs/syslog-and-rsyslog"
---

# syslog and rsyslog

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** syslog is an older logging protocol/standard that categorizes messages by facility
> (what subsystem) and severity (how serious). `rsyslog` is the most common implementation still
> running on many distros, routing syslog messages into the specific files seen in
> [`/var/log`](var-log-directory-structure.md).

## What is it?

syslog is a long-standing logging standard defining how a message is categorized (by facility and
severity) and transmitted; `rsyslog` is the most widely used daemon implementing it — the
mechanism actually responsible for many of the files this module's earlier topic found under
`/var/log`.

## Why does it exist?

Long before systemd's journal existed, Linux and Unix systems needed a standard way for any
program to emit a log message with enough metadata (which subsystem, how severe) that a central
daemon could route it to the right destination — syslog is that standard, and it predates and
still coexists with the journal on most current systems.

## Where is it used?

Any system where `rsyslog` (or a similar syslog daemon) is still running alongside systemd's
journal — commonly to maintain the traditional `/var/log` file layout many tools, scripts, and
habits still expect, and to forward logs to a remote log server (this module's later centralized
logging topic).

## How it works

> 📊 Diagram: a program emitting a syslog message tagged with a facility (e.g. `auth`) and a
> severity (e.g. `error`), received by `rsyslog`, which consults its configured rules
> (`/etc/rsyslog.conf`) to route that specific facility/severity combination into the correct file
> under `/var/log`.

| Concept | Meaning |
|---|---|
| Facility | Which subsystem generated the message (`auth`, `kern`, `mail`, `cron`, etc.) |
| Severity | How serious the message is (`emerg`, `alert`, `crit`, `err`, `warning`, `notice`, `info`, `debug`) |
| `rsyslog` | The daemon receiving syslog messages and routing them per configured rules |
| `/etc/rsyslog.conf` | Defines the routing rules — which facility/severity combination goes to which file |

**Relationship with the journal**: on most current systemd-based distros, `rsyslog` doesn't
receive messages directly from applications anymore — instead, it commonly reads from the
systemd journal and forwards/formats entries into traditional `/var/log` files, bridging the two
systems rather than competing with the journal as a separate, independent source.

## Real-world example

A team relies on a legacy script that greps `/var/log/auth.log` for failed login attempts on a
systemd-based server. The script continues working correctly because `rsyslog`, even though the
journal is the actual originating source of those events, still reads from the journal and writes
matching entries into `/var/log/auth.log` as configured — a live example of syslog/rsyslog
bridging older tooling expectations onto a modern, journal-based system underneath.

## Commands

No new command — `rsyslog` is a service managed with
[`systemctl`](../../commands/systemctl.md), already covered in
[Module 11](../11-services/systemctl-start-stop-enable-disable-status.md); its routing rules are
configuration, not a command to run directly.

## Production example

```
$ cat /etc/rsyslog.conf
auth,authpriv.*                /var/log/auth.log
*.*;auth,authpriv.none         -/var/log/syslog

$ sudo systemctl status rsyslog
```

The first rule routes anything tagged with the `auth`/`authpriv` facility to `auth.log`; the
second routes everything else (excluding those same facilities) to `syslog`.

## Do / Don't

| Do | Don't |
|---|---|
| Check `/etc/rsyslog.conf` when a log entry appears in an unexpected file | Assume every log file's routing is hardcoded and unconfigurable |
| Understand `rsyslog` commonly bridges the journal to traditional log files on modern systems | Assume `rsyslog` and the journal are always two entirely separate, competing log sources |
| Check whether `rsyslog` is running with `systemctl status` before assuming a file should be populated | Assume `/var/log/syslog`/`auth.log` are always actively updated regardless of service status |

## Common mistakes

- Assuming `/var/log` files are populated by some fixed, unconfigurable mechanism, rather than
  checking `/etc/rsyslog.conf`'s actual routing rules when something appears in an unexpected
  place.
- Treating `rsyslog` and the systemd journal as entirely separate, competing systems, missing that
  `rsyslog` commonly reads from and formats the journal's data on modern systems.
- Not checking whether the `rsyslog` service is actually running when an expected log file isn't
  being updated.

## Best practices

- Check `/etc/rsyslog.conf`'s routing rules directly when a log entry's destination seems
  surprising.
- Understand that on a modern systemd-based system, `rsyslog` is often a bridge from the journal
  to traditional files, not an independent original source.
- Verify `rsyslog`'s service status (`systemctl status rsyslog`) as part of troubleshooting a
  missing or stale traditional log file.

## Exercises

1. Check whether `rsyslog` is installed and running on a system you have access to.
2. Read `/etc/rsyslog.conf` (if present) and identify at least one facility/severity routing rule.
3. Explain in one sentence how `rsyslog` relates to the systemd journal on a modern system.

## Quiz

**Q: What do "facility" and "severity" mean in the context of syslog?**
<details><summary>Show answer</summary>
Facility identifies which subsystem generated a message (e.g. `auth`, `kern`, `cron`); severity
indicates how serious it is (e.g. `err`, `warning`, `info`).
</details>

**Q: On a modern systemd-based system, where does `rsyslog` typically get its messages from?**
<details><summary>Show answer</summary>
Commonly from the systemd journal itself, which it reads and formats into traditional `/var/log`
files — bridging the two systems rather than acting as a fully independent source.
</details>

## Interview questions

- What's the relationship between `rsyslog` and the systemd journal on a modern Linux system? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- syslog categorizes messages by facility (subsystem) and severity; `rsyslog` is the common
  implementing daemon.
- `/etc/rsyslog.conf` defines the routing rules that determine which file a message lands in.
- On modern systemd-based systems, `rsyslog` commonly bridges the journal to traditional
  `/var/log` files rather than being an independent original source.
- Checking `rsyslog`'s service status is a normal troubleshooting step for a stale log file.

## Related topics

- [/var/log Directory Structure](var-log-directory-structure.md)
- [logrotate](logrotate.md)
