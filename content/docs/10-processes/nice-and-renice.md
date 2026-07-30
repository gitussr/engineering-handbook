---
title: "nice and renice"
description: "Controlling how much CPU priority a process gets relative to others — a lower niceness means higher priority, which trips up everyone at first."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["10-processes/foreground-vs-background-processes"]
relatedTopics: ["zombie-and-orphan-processes"]
relatedCommands: ["nice", "renice"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#nice-value-range"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "10-processes/zombie-and-orphan-processes"
prevTopic: "10-processes/foreground-vs-background-processes"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["nice command linux", "renice command", "nice value range", "process priority linux"]
canonicalUrl: "/docs/processes/nice-and-renice"
---

# nice and renice

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** Niceness ranges from -20 (highest priority) to 19 (lowest) — counterintuitively, a
> *lower* number means *higher* priority ("less nice to other processes" = takes more CPU).
> `nice` sets priority when starting a process; `renice` changes it for one already running.

## What is it?

Niceness is a value controlling how much CPU priority a process gets relative to others on the
same system, on a scale from -20 (highest priority) to 19 (lowest priority), defaulting to 0.

## Why does it exist?

Not every process is equally urgent — a backup job running in the background shouldn't compete
equally with a user-facing web server for CPU time. Niceness lets you express that relative
priority directly, influencing the kernel scheduler's decisions without needing to fully pause or
stop the lower-priority work.

## Where is it used?

- Running batch jobs, backups, or bulk processing at low priority so they don't compete with
  latency-sensitive services
- Temporarily deprioritizing a resource-heavy but non-urgent process discovered via `top`
  (Module 10's earlier topic)

## How it works

> 📊 Diagram: a horizontal scale from -20 to 19, labeled "Higher Priority" on the -20 end and
> "Lower Priority" on the 19 end, with 0 marked as default — the inverse relationship (lower
> number = higher priority) is the single detail worth calling out visually, since it's
> consistently counterintuitive.

- **`nice`**: sets a starting niceness when launching a new process (`nice -n 10 command`).
- **`renice`**: changes the niceness of an already-running process (`renice -n 10 -p PID`).
- Only root can *decrease* a process's niceness (increase its priority) below 0, or below another
  user's process — a regular user can only make their own processes *less* favorably prioritized,
  not more, which prevents users from freely starving each other's processes of CPU.

## Real-world example

A nightly backup script is launched with `nice -n 15 backup.sh` specifically so it runs whenever
spare CPU is available, without competing meaningfully against the production web server also
running on the same box during the (hopefully rare) moments they overlap — the backup finishes
somewhat slower, but the web server's responsiveness is protected.

## Commands

- [`nice`](../../commands/nice.md) — full syntax and examples
- [`renice`](../../commands/renice.md) — full syntax and examples

## Production example

```
$ nice -n 15 ./backup.sh &
$ renice -n 10 -p 24831
```

## Do / Don't

| Do | Don't |
|---|---|
| Remember lower niceness number = higher priority | Assume a "higher" nice value means more priority |
| Use `nice` for genuinely non-urgent background/batch work | Deprioritize something latency-sensitive by mistake |
| Use `renice` to adjust an already-running process's priority | Kill and restart a process just to change its priority |

## Common mistakes

- Getting the direction backwards — assuming a higher nice value means higher priority, when it's
  the opposite.
- Trying to *increase* a process's priority (decrease niceness below 0) as a regular user and
  being confused by the permission error — this requires root.
- Killing and restarting a process just to adjust its priority, when `renice` does it directly
  without any interruption.

## Best practices

- Use `nice -n [positive value]` for batch/background work that shouldn't compete with
  latency-sensitive services.
- Use `renice` to adjust an already-running process rather than restarting it.
- Remember the direction: lower number, higher priority — say it out loud if it helps; this trips
  up almost everyone at first.

## Exercises

1. Start a test process with `nice -n 15` and confirm its niceness with `ps -o pid,ni,comm`.
2. Use `renice` to change that process's niceness while it's still running.
3. Explain in one sentence why a regular user can't decrease a process's niceness below 0.

## Quiz

**Q: Does a niceness of -10 or 10 give a process higher CPU priority?**
<details><summary>Show answer</summary>
-10 — lower niceness values mean higher priority, the reverse of what the numbers might suggest
at first glance.
</details>

**Q: What's the difference between `nice` and `renice`?**
<details><summary>Show answer</summary>
`nice` sets a starting niceness when launching a new process; `renice` changes the niceness of a
process that's already running.
</details>

## Interview questions

- Why might you run a backup job with a nice value of 15 or higher? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Niceness ranges -20 (highest priority) to 19 (lowest); the relationship is inverted from what
  intuition suggests.
- `nice` sets priority at launch; `renice` changes it for a running process.
- Only root can increase priority (decrease niceness below 0 or below another user's process).
- Use lower priority for non-urgent batch work to protect latency-sensitive services.

## Related topics

- [Zombie and Orphan Processes](zombie-and-orphan-processes.md)
