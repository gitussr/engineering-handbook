---
title: "top, htop, vmstat, iostat"
description: "Four command-line tools for real-time system observation — process-level view (top/htop) versus system-wide virtual memory and I/O statistics (vmstat/iostat)."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["20-monitoring/monitoring-fundamentals"]
relatedTopics: ["monitoring-fundamentals", "monitoring-stacks-overview"]
relatedCommands: ["top", "vmstat", "iostat"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#vmstat-vs-top"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "20-monitoring/monitoring-stacks-overview"
prevTopic: "20-monitoring/monitoring-fundamentals"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["vmstat command examples", "iostat command examples", "top vs vmstat", "linux cli monitoring tools"]
canonicalUrl: "/docs/monitoring/top-htop-vmstat-iostat"
---

# top, htop, vmstat, iostat

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `top`/`htop` show a live, per-process view. `vmstat` summarizes system-wide CPU,
> memory, and swap activity in one compact line per interval. `iostat` reports per-device disk I/O
> throughput and utilization — neither of which `top` shows in useful detail.

## What is it?

Four command-line tools for real-time observation, already introduced individually or by name
elsewhere ([Module 10](../10-processes/ps-top-htop.md) for `top`/`htop`) — this page is where
`vmstat` and `iostat` get full treatment, and where all four are placed side by side to clarify
when each one actually answers the question you have.

## Why does it exist?

`top` and `htop` answer "which process is using resources right now" — but they summarize
system-wide memory/swap and don't show per-device disk I/O in useful detail at all. `vmstat` and
`iostat` fill exactly that gap: system-wide virtual memory behavior and per-device I/O throughput,
respectively, neither of which a process-level tool is built to show.

## Where is it used?

Any time "the server feels slow" needs a first, fast diagnosis: `top`/`htop` for "what process is
the problem," `vmstat` for "is the system swapping," `iostat` for "is a specific disk saturated" —
often used together, in that rough diagnostic order.

## How it works

> 📊 Diagram: four terminal panels side by side under one heading, "answering a different
> question" — `top`/`htop`: a per-process table (which process?); `vmstat`: one summary line per
> interval showing `r`/`b` (runnable/blocked process counts), memory, swap `si`/`so`, and CPU
> columns (is the whole system healthy?); `iostat`: a per-device table showing `%util`, `await`
> (is a specific disk the bottleneck?) — each panel's distinct focus highlighted.

**`top`/`htop`** — already covered in
[ps, top, htop](../10-processes/ps-top-htop.md): a live, per-process view, sortable by CPU or
memory, with `htop`'s richer interactive UI as an alternative front end to the same underlying
data.

**`vmstat`** — one compact summary line per interval, covering the whole system at once:

```
$ vmstat 2 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 512000  45000 2500000    0    0    12    8  105  210 15  4 80  1  0
```

Key columns:

| Column | Meaning |
|---|---|
| `r` | Processes waiting to run (runnable queue) — sustained high values suggest CPU contention |
| `b` | Processes blocked, waiting on I/O |
| `si`/`so` | Swap in/out — any sustained non-zero value here means the system is actively swapping, a strong sign of memory pressure |
| `wa` | Percentage of CPU time waiting on I/O — high values point toward a disk bottleneck, not a CPU one |

**`iostat`** — per-device disk I/O throughput and utilization:

```
$ iostat -x 2
Device            r/s     w/s     rkB/s     wkB/s   await  %util
sda              12.50   45.20    512.30   3200.10    8.20  65.30
```

Key columns:

| Column | Meaning |
|---|---|
| `%util` | Percentage of time the device was busy servicing requests — approaching 100% suggests the disk itself is the bottleneck |
| `await` | Average time (ms) requests waited to be serviced — rising `await` alongside high `%util` confirms a genuine I/O bottleneck, not just high volume being handled fine |

## Real-world example

An application feels sluggish, and `top` shows no single process consuming unusual CPU — a
misleading dead end if that's the only tool checked. `vmstat` reveals a non-zero, sustained `si`/
`so` (active swapping) and a high `wa` percentage, pointing toward memory pressure causing disk
swapping rather than a CPU problem. Following up with `iostat -x` confirms `%util` near 100% on
the swap device specifically — the complete picture (memory pressure causing swap I/O saturation)
that `top` alone could never have revealed, since it doesn't expose swap activity or per-device
I/O at all.

## Syntax

```
vmstat [INTERVAL] [COUNT]
iostat [-x] [INTERVAL] [COUNT]
```

## Commands

See [`top`](../../commands/top.md) (extended with batch mode for this module),
[`vmstat`](../../commands/vmstat.md), and [`iostat`](../../commands/iostat.md) for full
references. `htop` remains covered within [Module 10's topic page](../10-processes/ps-top-htop.md)
rather than a separate canonical command page (see this module's
[intentional gaps](index.md)).

## Production example

```
$ vmstat 1 3
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 3  1  204800 128000  12000 1800000  450  620   200   150  980 1500 25 10 40 25  0
 4  2  215000 115000  11500 1795000  500  680   220   160 1010 1550 28 11 36 25  0
 3  1  210000 120000  11800 1798000  480  650   210   155  995 1520 26 10 39 25  0
```

Sustained non-zero `si`/`so` across all three sampled intervals — a clear, repeatable signal of
active swapping, not a one-off blip.

## Do / Don't

| Do | Don't |
|---|---|
| Use `vmstat`/`iostat` when `top` doesn't explain a slowdown | Assume `top` alone always tells the full story |
| Sample over multiple intervals to distinguish a trend from a blip | Judge a system's health from a single `vmstat`/`iostat` line |
| Check `wa` (I/O wait) in `vmstat` before assuming a CPU bottleneck | Conclude "CPU-bound" purely from a high user/system CPU percentage without checking `wa` |

## Common mistakes

- Only ever checking `top`, missing swap activity and disk I/O bottlenecks it doesn't surface in
  useful detail.
- Reading a single `vmstat`/`iostat` line as conclusive, rather than sampling several intervals to
  confirm a sustained pattern versus a momentary spike.
- Misreading high CPU "wa" time as low idle/high load, without recognizing `wa` specifically means
  the CPU is idle *waiting on I/O*, not doing computational work.

## Best practices

- Reach for `top`/`htop` first (process-level), then `vmstat` (system-wide memory/swap), then
  `iostat` (per-device I/O) as a fast, ordered escalation when the previous tool doesn't explain
  the symptom.
- Always sample multiple intervals (`vmstat 2 5`, not just `vmstat` once) to distinguish a real
  trend from noise.
- Watch `si`/`so` in `vmstat` as an early swap-pressure signal, and `%util`/`await` in `iostat` as
  the corresponding per-device confirmation.

## Exercises

1. Run `vmstat 2 5` on a system you have access to and identify whether `si`/`so` are non-zero.
2. Run `iostat -x 2 3` and identify the device with the highest `%util`.
3. Explain why `top` alone might miss a swap-related performance problem that `vmstat` would
   reveal immediately.

## Quiz

**Q: What do non-zero si/so values in vmstat indicate?**
<details><summary>Show answer</summary>
Active swapping — memory pages being moved to and from swap space, a strong sign of memory
pressure.
</details>

**Q: What does a high %util value in iostat suggest?**
<details><summary>Show answer</summary>
The device is busy servicing requests close to its capacity — a potential disk I/O bottleneck,
especially when paired with rising `await` times.
</details>

**Q: Why might top fail to explain a slowdown that vmstat or iostat can?**
<details><summary>Show answer</summary>
`top` is process-focused and doesn't show system-wide swap activity or per-device disk I/O
statistics in useful detail — problems rooted in swapping or disk saturation aren't visible from
`top` alone.
</details>

## Interview questions

- How would you diagnose a server that "feels slow" but shows no obvious CPU hog in top? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `top`/`htop` show per-process activity; `vmstat` summarizes system-wide memory/swap; `iostat`
  reports per-device disk I/O.
- Sustained non-zero `si`/`so` in `vmstat` signals active swapping and memory pressure.
- High `%util`/`await` in `iostat` signals a genuine per-device I/O bottleneck.
- Escalate through these tools in order (process → system memory → per-device I/O) when the
  simpler tool doesn't explain a symptom.

## Related topics

- [Monitoring Fundamentals](monitoring-fundamentals.md)
- [Monitoring Stacks Overview](monitoring-stacks-overview.md)
