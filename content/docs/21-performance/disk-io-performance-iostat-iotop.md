---
title: "Disk I/O Performance: iostat, iotop"
description: "Confirming a disk bottleneck with iostat's %util and await, then finding exactly which process is responsible with iotop's per-process I/O view."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["21-performance/memory-performance-free-vmstat"]
relatedTopics: ["memory-performance-free-vmstat", "network-performance-iperf-sar"]
relatedCommands: ["iostat", "iotop"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#iotop-vs-iostat"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "21-performance/network-performance-iperf-sar"
prevTopic: "21-performance/memory-performance-free-vmstat"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["iotop command examples", "iostat await util explained", "find process using disk io", "disk bottleneck diagnosis linux"]
canonicalUrl: "/docs/performance/disk-io-performance-iostat-iotop"
---

# Disk I/O Performance: iostat, iotop

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `iostat -x` (Module 20) confirms *whether* a device is saturated; `iotop` then shows
> exactly *which process* is generating that I/O — the same escalation pattern as `mpstat` →
> per-process CPU tools, applied to disk.

## What is it?

The two-step disk I/O diagnosis: confirming device-level saturation with
[`iostat`](../20-monitoring/top-htop-vmstat-iostat.md) (already covered in Module 20), then
identifying the responsible process with `iotop`, new to this page.

## Why does it exist?

`iostat` confirms a device is a bottleneck but doesn't say which process is causing it —
essential context missing when several processes on a busy system could plausibly be responsible.
`iotop` closes that gap, giving a per-process I/O view the same way `top` gives a per-process CPU
view.

## Where is it used?

Any time `iostat` has already confirmed high `%util`/`await` on a device and the next question is
"which process is actually doing this" — a shared database server, a host running several
containers, or any multi-tenant system where the disk-heavy process isn't obvious.

## How it works

> 📊 Diagram: a two-step funnel — step one, `iostat -x` confirming device-level saturation
> (`%util` near 100%, high `await`); step two, `iotop` sorted by I/O, revealing a specific PID
> responsible for the bulk of that device's read/write activity — mirroring the
> `mpstat`-then-`top` pattern from CPU diagnosis.

**Step 1 — confirm the device is actually saturated** (recap from
[Module 20](../20-monitoring/top-htop-vmstat-iostat.md)):

```
$ iostat -x 2
Device            r/s     w/s     rkB/s     wkB/s   await  %util
sda              12.50  245.20    512.30  18200.10   85.20  98.30
```

`%util` near 100% and high `await` confirm the device itself is the bottleneck — the necessary
first step before looking for a specific culprit process.

**Step 2 — `iotop` to find the responsible process:**

```
$ sudo iotop -o
  TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN     IO>    COMMAND
  4521 be/4  postgres    0.00 B/s   18.20 M/s  0.00 %  95.50 % postgres: writer
```

`-o` shows only processes actually doing I/O right now, cutting through noise from idle processes.
The `postgres` writer process here accounts for nearly all the write activity `iostat` confirmed
was saturating the device.

## Real-world example

`iostat -x` shows a database server's disk consistently at 95%+ utilization with high `await`, but
several services run on the same host — the database, a log shipper, and a backup job — any of
which could plausibly be responsible. `iotop -o` immediately reveals the backup job is
responsible for nearly all current write activity, running at an unexpected time due to a
misconfigured schedule — a finding `iostat` alone could confirm existed, but never identify by
itself.

## Syntax

```
iotop [-o] [-a]
```

## Commands

See [`iostat`](../../commands/iostat.md) (Module 20, already covered) and
[`iotop`](../../commands/iotop.md) for the full references.

## Production example

```
$ iostat -x 2 1
Device            r/s     w/s     await  %util
sda              8.20   180.50    72.30  94.10

$ sudo iotop -o -b -n 1
Total DISK READ:         0.00 B/s | Total DISK WRITE:        14.80 M/s
  PID  USER     DISK READ  DISK WRITE  COMMAND
 8821  backup      0.00 B/s   14.60 M/s  tar czf /backup/data.tar.gz /data
```

Confirming saturation, then identifying the exact command (`tar` running a backup) responsible —
a complete, two-tool diagnosis.

## Do / Don't

| Do | Don't |
|---|---|
| Confirm device saturation with `iostat` before hunting for a process | Jump straight to `iotop` without confirming the device is actually the bottleneck |
| Use `iotop -o` to filter to only active I/O processes | Scroll through every process's idle I/O looking for the culprit manually |
| Treat a high-I/O process finding as a starting point for investigation, not automatically a problem | Assume the top I/O process in `iotop` is automatically misbehaving |

## Common mistakes

- Running `iotop` without first confirming via `iostat` that the device is genuinely saturated,
  potentially chasing a process that isn't actually causing a real problem.
- Not using `iotop -o`, making it harder to spot the actually active process among many idle ones.
- Assuming the top process in `iotop` is automatically at fault, without considering whether its
  I/O activity is expected and legitimate (a scheduled backup, for instance) versus genuinely
  unexpected.

## Best practices

- Always confirm with `iostat` first — `iotop` answers "which process," not "is there actually a
  problem."
- Use `iotop -o` as the default, filtering out the noise of idle processes.
- Cross-reference a surprising `iotop` finding against [Module 17's](../17-cron/index.md)
  scheduled jobs or [Module 20's](../20-monitoring/index.md) monitoring history — an unexpected
  process at an unexpected time is often a scheduling misconfiguration, not a code problem.

## Exercises

1. Run `iostat -x 2 3` and note whether any device shows high `%util` and `await`.
2. If a device looks saturated, run `sudo iotop -o` and identify the responsible process.
3. Explain why confirming saturation with `iostat` should always precede running `iotop`.

## Quiz

**Q: What does iostat confirm that iotop doesn't, and vice versa?**
<details><summary>Show answer</summary>
`iostat` confirms whether a device is genuinely saturated (utilization and wait time); `iotop`
identifies which specific process is responsible for that I/O — they answer different, sequential
questions.
</details>

**Q: What does the -o flag do in iotop?**
<details><summary>Show answer</summary>
Shows only processes actually performing I/O right now, filtering out idle processes that would
otherwise clutter the view.
</details>

**Q: Should the top process shown in iotop always be treated as a problem?**
<details><summary>Show answer</summary>
No — its I/O activity might be entirely expected and legitimate (a scheduled backup, for example);
`iotop` identifies the responsible process, but whether that's actually a problem requires further
context.
</details>

## Interview questions

- Walk through how you'd find which process is causing high disk I/O on a shared server. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `iostat` confirms device-level saturation; `iotop` identifies the specific responsible process —
  a two-step escalation, not interchangeable tools.
- `iotop -o` filters to only actively I/O-active processes, cutting through idle-process noise.
- A high-I/O process isn't automatically a problem — cross-reference against expected scheduled
  activity before concluding it's misbehaving.
- This mirrors the `mpstat`-then-`top` escalation pattern from CPU diagnosis, applied to disk I/O.

## Related topics

- [Memory Performance: free, vmstat](memory-performance-free-vmstat.md)
- [Network Performance: iperf, sar](network-performance-iperf-sar.md)
