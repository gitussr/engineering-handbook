---
title: "CPU Performance: mpstat, top"
description: "Per-core CPU utilization with mpstat, and the run-queue/load-average signals that reveal whether CPU is a genuine bottleneck rather than just busy."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["21-performance/performance-tuning-overview"]
relatedTopics: ["performance-tuning-overview", "memory-performance-free-vmstat"]
relatedCommands: ["mpstat", "top"]
careerRelevance: ["devops", "sre", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#load-average-vs-cpu-utilization"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "21-performance/memory-performance-free-vmstat"
prevTopic: "21-performance/performance-tuning-overview"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["mpstat per core cpu", "load average vs cpu count", "cpu bound vs io bound", "mpstat command examples"]
canonicalUrl: "/docs/performance/cpu-performance-mpstat-top"
---

# CPU Performance: mpstat, top

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Backend

> **TL;DR:** `mpstat -P ALL` shows per-core CPU utilization — critical for spotting a single
> maxed-out core hidden inside a healthy-looking system-wide average. Compare load average
> against core count, not against an absolute number, to judge whether CPU is genuinely saturated.

## What is it?

Diagnosing CPU as a performance bottleneck specifically — distinguishing genuinely CPU-bound
symptoms from other resource constraints that merely look CPU-related, using `mpstat`'s per-core
breakdown alongside `top` (already covered in
[Module 10](../10-processes/ps-top-htop.md)/[Module 20](../20-monitoring/top-htop-vmstat-iostat.md)).

## Why does it exist?

A system-wide CPU average can look healthy (say, 40% utilized) while one specific core is
completely maxed out — invisible without a per-core view. `mpstat` exists specifically to reveal
that per-core detail, which `top`'s default view doesn't emphasize.

## Where is it used?

Diagnosing single-threaded application bottlenecks (where one core maxes out while others sit
idle), confirming whether load average increases are actually CPU-driven or a symptom of a
different constraint entirely (I/O wait counts toward load average too), and capacity planning
decisions about core count.

## How it works

> 📊 Diagram: a multi-core CPU shown as four boxes — three at 20% utilization, one at 100% —
> with a system-wide average calculation (35%) that looks unremarkable, contrasted against
> `mpstat -P ALL`'s per-core breakdown that reveals the single maxed-out core immediately.

**`mpstat -P ALL`** — per-core utilization breakdown:

```
$ mpstat -P ALL 2
CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest   %idle
all    25.30    0.00    5.20    2.10    0.00    0.50    0.00    0.00   66.90
  0    98.50    0.00    1.00    0.00    0.00    0.50    0.00    0.00    0.00
  1     5.20    0.00    3.10    1.50    0.00    0.20    0.00    0.00   90.00
  2     4.80    0.00    2.90    2.00    0.00    0.30    0.00    0.00   90.00
  3     3.10    0.00    2.50    5.00    0.00    0.40    0.00    0.00   89.00
```

CPU 0 is completely saturated (`%idle` at 0%) while the system-wide `all` row (35% used) looks
unremarkable — exactly the case `mpstat`'s per-core view is built to catch, and a strong signal of
a single-threaded process unable to use more than one core.

**Load average vs. CPU utilization — a critical distinction:** load average (from `uptime`, seen
on the previous page) counts processes waiting to run *or* waiting on I/O — a high load average
doesn't automatically mean CPU is the bottleneck. Always compare load average against the number
of CPU cores (`nproc`), not against an absolute threshold — a load average of 8 is fine on a
32-core machine and a serious problem on a 4-core one.

**`%iowait` in `mpstat`/`top`** — time the CPU spent idle specifically because it was waiting on
I/O, not because there was no work to do — a high `%iowait` points toward a disk bottleneck
([Disk I/O Performance](disk-io-performance-iostat-iotop.md), next-but-one page), not a genuine
CPU shortage, even though it shows up in CPU-focused tools.

## Real-world example

A single-threaded batch job runs slower than expected on a 16-core server, and the team initially
suspects the server itself is underpowered, since `top`'s system-wide summary shows only ~6% total
CPU usage — seemingly plenty of headroom. `mpstat -P ALL` reveals one core pinned at 100% while the
other 15 sit idle: the job simply can't use more than one core, and adding more cores would change
nothing. The actual fix is parallelizing the workload (or accepting its single-core ceiling), not
scaling the machine.

## Syntax

```
mpstat [-P ALL] [INTERVAL] [COUNT]
```

## Commands

See [`mpstat`](../../commands/mpstat.md) for the full reference, and
[`top`](../../commands/top.md) (Modules 10/20, already covered) for the complementary
per-process view.

## Production example

```
$ nproc
4

$ uptime
load average: 3.85, 3.20, 2.90

$ mpstat -P ALL 2 1
CPU    %usr   %sys %iowait   %idle
all    72.00   8.00    1.00   19.00
  0    95.00   4.00    0.50    0.50
  1    68.00   9.00    1.20   21.80
  2    65.00   8.50    1.30   25.20
  3    60.00   9.50    1.00   29.50
```

Load average (3.85) approaching the core count (4) alongside genuinely high per-core `%usr` —
this time a real, confirmed CPU bottleneck, not a single-core artifact.

## Do / Don't

| Do | Don't |
|---|---|
| Compare load average against `nproc`'s core count | Judge load average against a fixed absolute number regardless of core count |
| Check `mpstat -P ALL` for a single-core bottleneck hidden inside a healthy average | Trust only the system-wide CPU average |
| Recognize high `%iowait` as a disk signal, not a CPU shortage | Treat all CPU-tool output as CPU-bound by definition |

## Common mistakes

- Judging load average against an absolute number instead of the actual core count, misjudging
  genuinely fine systems as overloaded (or the reverse).
- Missing a single maxed-out core because only the system-wide average was checked, not the
  per-core breakdown.
- Treating high `%iowait` as a CPU problem, when it actually signals the CPU is idle waiting on
  disk — a disk performance investigation, not a CPU one.

## Best practices

- Always check load average relative to `nproc`'s core count, never in isolation.
- Use `mpstat -P ALL` whenever a single-threaded bottleneck is suspected, since the system-wide
  average can hide it entirely.
- Route a high `%iowait` finding toward disk I/O investigation
  ([next-but-one page](disk-io-performance-iostat-iotop.md)), not CPU tuning.

## Exercises

1. Run `nproc` and `uptime` together and judge whether the load average suggests CPU saturation
   for that specific machine.
2. Run `mpstat -P ALL 2 3` and identify whether any single core is disproportionately loaded.
3. Explain why a high `%iowait` value doesn't indicate a CPU bottleneck.

## Quiz

**Q: Why should load average be compared against core count rather than a fixed number?**
<details><summary>Show answer</summary>
The same load average value means something completely different on a 4-core machine versus a
32-core machine — it must be interpreted relative to available cores, not in isolation.
</details>

**Q: What can mpstat -P ALL reveal that top's default summary might hide?**
<details><summary>Show answer</summary>
A single core saturated at 100% while other cores sit idle — invisible in a system-wide average
that looks moderate or low.
</details>

**Q: Does a high %iowait value indicate a CPU bottleneck?**
<details><summary>Show answer</summary>
No — it means the CPU is idle specifically because it's waiting on I/O, pointing toward a disk
performance issue rather than a CPU shortage.
</details>

## Interview questions

- A server shows low average CPU usage but users report slowness — how would you investigate? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `mpstat -P ALL` reveals per-core utilization that a system-wide average can hide entirely.
- Always compare load average against `nproc`'s core count, not an absolute threshold.
- High `%iowait` signals a disk bottleneck, not a CPU one, even though it appears in CPU tools.
- A single-threaded workload can saturate one core while the system-wide average looks fine —
  only a per-core view reveals this.

## Related topics

- [Performance Tuning Overview](performance-tuning-overview.md)
- [Memory Performance: free, vmstat](memory-performance-free-vmstat.md)
