---
title: "Reading Logs During a Production Incident"
description: "A repeatable sequence for using everything in this module under real time pressure — where to look first, how to narrow scope fast, and what to capture before it rotates away."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/centralized-logging-concepts"]
relatedTopics: ["journalctl-deep-dive"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#incident-log-workflow"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "17-cron/task-scheduling-overview"
prevTopic: "16-logs/centralized-logging-concepts"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["reading logs during incident", "production incident log workflow", "log investigation checklist", "correlate logs across services"]
canonicalUrl: "/docs/logs/reading-logs-during-a-production-incident"
---

# Reading Logs During a Production Incident

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator · Cybersecurity

> **TL;DR:** Under time pressure: narrow by time window first, then by service/unit, then by
> severity — in that order. Capture what you find before logs rotate away or the journal is lost
> to a reboot. This page is the practical synthesis of everything else in this module.

## What is it?

A repeatable investigation sequence for using this module's tools (the journal, `/var/log`,
syslog, centralized logging) effectively under real incident time pressure — not new material,
but the practiced workflow that ties everything else in this module together.

## Why does it exist?

Knowing individual tools (`journalctl` flags, where `/var/log` files live, what centralized
logging is for) doesn't automatically mean using them efficiently during an actual incident, when
time pressure and stress make it easy to search inefficiently or lose evidence. This page exists
to turn the module's individual tools into one deliberate, repeatable sequence.

## Where is it used?

Every live production incident where something has already gone wrong and the priority is
understanding what happened and restoring service — the single highest-pressure, highest-value
context all of this module's other topics were building toward.

## How it works

> 📊 Diagram: a funnel — starting broad ("something's wrong with checkout") narrowing through
> three sequential filters (time window, then service/unit, then severity), ending at a small,
> specific set of log lines that actually explain the incident — each filter stage labeled with
> the tool used (`--since`/`--until`, `-u`, `-p`, all from [Module 11](../11-services/journalctl-basics.md)
> and this module's deep-dive topic).

**The standard sequence**:

1. **Narrow by time window first** — establish roughly when the problem started (a deploy
   timestamp, an alert firing time, a user report) and scope every subsequent search to that
   window (`--since`/`--until`). Searching unscoped wastes time on irrelevant history.
2. **Narrow by service/unit next** — once you have a time window, scope to the specific
   service(s) plausibly involved (`journalctl -u`), rather than reading every unit's interleaved
   output.
3. **Narrow by severity last** — within that scoped window and service, filter to errors and
   above (`-p err`) first, expanding to lower severities only if the error-level view doesn't
   explain enough.
4. **Capture findings before they're lost** — copy relevant log excerpts somewhere durable
   immediately; a volatile journal ([this module's deep-dive topic](journalctl-deep-dive.md)) can
   lose exactly this evidence on the next reboot, and rotated log files
   ([`logrotate`](logrotate.md)) eventually compress or delete themselves.
5. **Correlate across machines if the fleet is bigger than one** — this is precisely what
   [centralized logging](centralized-logging-concepts.md) exists for; don't manually SSH into
   five machines if a central search already covers all of them.

## Real-world example

An alert fires that checkout is failing. Rather than opening every log source at once, the
on-call engineer first pins down the time window from the alert's own timestamp, then scopes to
the checkout service specifically (`journalctl -u checkout-service --since "10 minutes ago"`),
then filters to errors (`-p err`) — surfacing the exact exception within seconds rather than
scrolling through unrelated informational noise from a dozen other services. The relevant log
lines are copied into the incident channel immediately, before the service is restarted (which
would start a fresh, empty window of context).

## Commands

No new command — this page is the applied synthesis of
[`journalctl`](../../commands/journalctl.md) (already covered in Module 11 and this module's
deep-dive topic) plus everything else in this module.

## Production example

```
$ journalctl -u checkout-service --since "10 minutes ago" -p err
Jul 27 14:32:10 web-03 checkout-service[8821]: FATAL: connection to payment-gateway timed out
```

One scoped, three-filter query — time window, unit, severity — surfacing the relevant line
directly.

## Do / Don't

| Do | Don't |
|---|---|
| Narrow by time window, then service, then severity, in that order | Search everything unscoped and scroll manually looking for the problem |
| Capture relevant log excerpts immediately, before a restart or reboot | Restart the affected service before saving what its logs currently show |
| Use centralized logging to correlate across machines when the fleet is larger than one | Manually SSH into every machine individually when a central search already covers them |

## Common mistakes

- Searching logs unscoped (no time window, no service filter) during an incident, wasting
  valuable time scrolling through irrelevant history.
- Restarting a failing service before capturing its current log state, losing exactly the
  evidence needed to understand what went wrong.
- Manually checking machines one at a time when centralized logging already provides a single,
  fleet-wide search.

## Best practices

- Always narrow by time window first — it's the single highest-leverage filter, eliminating the
  most irrelevant noise fastest.
- Capture relevant log excerpts into a durable location (an incident channel, a ticket) before any
  action that could lose them (restart, reboot, log rotation).
- Default to centralized logging's cross-machine search whenever more than one machine could be
  involved, rather than manual per-machine investigation.

## Exercises

1. Practice the time-window-then-service-then-severity sequence against any log source you have
   access to.
2. Explain in one sentence why restarting a failing service before checking its logs can destroy
   evidence.
3. Describe why centralized logging changes this sequence when more than one machine is involved.

## Quiz

**Q: What's the recommended order of narrowing when investigating logs during an incident?**
<details><summary>Show answer</summary>
Time window first, then service/unit, then severity — narrowing progressively rather than
searching everything unscoped.
</details>

**Q: Why should you capture relevant log excerpts before restarting a failing service?**
<details><summary>Show answer</summary>
A restart can lose the exact log context showing what went wrong — especially if the journal is
volatile or the relevant log file is close to rotating — so evidence should be captured first.
</details>

## Interview questions

- Walk through how you'd investigate a production incident using logs, from alert to root cause. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- The standard incident log sequence: narrow by time window, then service, then severity.
- Capture relevant findings before a restart, reboot, or log rotation can destroy them.
- Use centralized logging to correlate across machines rather than manual per-machine checks.
- This page is the practical synthesis of every other topic in this module, not new material on
  its own.

## Related topics

- [journalctl Deep Dive](journalctl-deep-dive.md)
- [Module 17: Cron](../17-cron/index.md)
