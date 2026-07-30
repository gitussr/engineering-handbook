---
title: "journalctl Deep Dive"
description: "Beyond -u and -f: managing journal size, controlling whether it survives a reboot, structured JSON output, and inspecting a specific past boot."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/var-log-directory-structure"]
relatedTopics: ["syslog-and-rsyslog"]
relatedCommands: ["journalctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#journal-persistence"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "16-logs/syslog-and-rsyslog"
prevTopic: "16-logs/var-log-directory-structure"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["journalctl disk usage", "journalctl vacuum-size", "journalctl persistent storage", "journalctl list-boots"]
canonicalUrl: "/docs/logs/journalctl-deep-dive"
---

# journalctl Deep Dive

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** [Module 11](../11-services/journalctl-basics.md) covered `-u`, `-f`, `--since`, and
> `-p` — the daily-driver flags. This page covers what's left: whether the journal survives a
> reboot at all, how to keep its disk usage under control, and inspecting a specific past boot.

## What is it?

The parts of `journalctl` beyond everyday service-log reading, already covered in
[journalctl Basics](../11-services/journalctl-basics.md): journal persistence, disk usage
management, structured output, and reviewing logs from a specific previous boot.

## Why does it exist?

The daily-driver flags (`-u`, `-f`, `--since`, `-p`) answer "what did this service log?" but don't
address three separate, real operational questions: does the journal survive a reboot at all, how
much disk space is it allowed to consume, and how do you review a system that's rebooted several
times since an issue occurred? This page answers those.

## Where is it used?

Configuring a server so its journal survives a reboot (not the default on every distro),
managing journal disk usage before it becomes a capacity problem, and investigating an issue that
happened during a boot that isn't the current one.

## How it works

> 📊 Diagram: `/etc/systemd/journald.conf`'s `Storage=` directive shown switching between
> `volatile` (journal lives only in memory, lost on reboot) and `persistent` (journal written to
> disk under `/var/log/journal`, survives reboots) — alongside `journalctl --list-boots` showing
> several past boots as distinct, selectable entries.

| Task | How |
|---|---|
| Check whether the journal is persistent | Look for `/var/log/journal/` — if it doesn't exist, the journal is volatile (memory-only) |
| Make the journal persistent | Set `Storage=persistent` in `/etc/systemd/journald.conf`, then restart `systemd-journald` |
| Check journal disk usage | `journalctl --disk-usage` |
| Cap journal size | `journalctl --vacuum-size=500M` (or `--vacuum-time=2weeks`) |
| List distinct boots | `journalctl --list-boots` |
| View a specific past boot | `journalctl -b -1` (previous boot), `-b -2` (two boots ago), etc. |
| Structured, machine-readable output | `journalctl -o json` (or `-o json-pretty` for human-readable JSON) |

**Persistent vs. volatile matters more than it first appears**: on some distros, the journal
defaults to volatile (memory-only) storage — meaning a full history of what happened is lost the
moment the system reboots, which is exactly when a crash investigation most needs it.

## Real-world example

A server crashes and reboots unexpectedly. An engineer runs `journalctl -b -1` to inspect the
*previous* boot's logs (the one that ended in the crash) rather than the current, freshly-booted
session — but discovers the journal was configured as volatile, and that previous boot's history
was lost entirely at reboot. Switching to `Storage=persistent` afterward ensures the next
unexpected reboot doesn't lose the exact evidence needed to diagnose it.

## Commands

- [`journalctl`](../../commands/journalctl.md) — full syntax and examples, including this page's
  deep-dive flags

## Production example

```
$ journalctl --disk-usage
Archived and active journals take up 1.2G in the file system.

$ journalctl --vacuum-size=500M
Vacuuming done, freed 700.0M of archived journals.

$ journalctl --list-boots
-1 3a1f9c... Sat 2026-07-26 08:00:00 UTC—Sat 2026-07-26 14:22:10 UTC
 0 7b2e4d... Sat 2026-07-26 14:22:41 UTC—Mon 2026-07-27 09:10:03 UTC

$ journalctl -b -1 -p err
```

The last command reviews error-level logs specifically from the boot that ended before the most
recent one.

## Do / Don't

| Do | Don't |
|---|---|
| Confirm whether the journal is persistent before relying on it across a reboot | Assume the journal always survives a reboot by default |
| Use `--vacuum-size`/`--vacuum-time` to proactively manage journal disk usage | Let the journal grow unmanaged until it becomes a disk-full problem |
| Use `--list-boots`/`-b -N` to investigate a specific past boot | Assume only the current boot's logs are ever relevant |

## Common mistakes

- Assuming the journal is always persistent across reboots, then losing exactly the evidence
  needed after an unexpected restart — the scenario in the real-world example above.
- Never checking `--disk-usage`, letting the journal grow until it contributes to a disk-full
  issue ([Module 15](../15-storage/disk-usage-df-du.md)).
- Forgetting `-b -N` exists and assuming a past boot's logs are simply gone once the system has
  rebooted since.

## Best practices

- Explicitly set `Storage=persistent` on any server where surviving a reboot's logs matters —
  don't assume the default is correct.
- Proactively cap journal size with `--vacuum-size`/`--vacuum-time`, or configure equivalent
  limits in `journald.conf`, rather than reacting after a disk-full incident.
- Use `--list-boots` and `-b -N` as the standard way to investigate an issue that occurred before
  the current boot.

## Exercises

1. Check whether `/var/log/journal/` exists on a system you have access to, to determine whether
   its journal is persistent.
2. Run `journalctl --disk-usage` and note the current size.
3. Run `journalctl --list-boots` and identify how many distinct boots are recorded.

## Quiz

**Q: How can you tell whether a system's journal is persistent or volatile?**
<details><summary>Show answer</summary>
Check whether `/var/log/journal/` exists — if it doesn't, the journal is volatile (memory-only)
and is lost on reboot.
</details>

**Q: How would you review logs from the boot before the current one?**
<details><summary>Show answer</summary>
`journalctl -b -1` — `--list-boots` shows all recorded boots if you need to identify which offset
corresponds to a specific past boot.
</details>

## Interview questions

- Why would you explicitly configure the systemd journal as persistent, and what's the risk if
  you don't? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- The journal's persistence isn't guaranteed by default — check for `/var/log/journal/` and set
  `Storage=persistent` deliberately if reboot survival matters.
- `--disk-usage` and `--vacuum-size`/`--vacuum-time` manage journal disk consumption proactively.
- `--list-boots` and `-b -N` let you investigate logs from a specific past boot.
- This page builds directly on [Module 11's journalctl basics](../11-services/journalctl-basics.md)
  rather than repeating them.

## Related topics

- [syslog and rsyslog](syslog-and-rsyslog.md)
- [Module 11: journalctl Basics](../11-services/journalctl-basics.md)
