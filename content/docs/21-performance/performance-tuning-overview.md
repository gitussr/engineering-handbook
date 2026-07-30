---
title: "Performance Tuning Overview"
description: "The measure-first discipline that separates real performance tuning from guesswork, and the four resource dimensions this module examines one at a time."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["20-monitoring/log-based-monitoring"]
relatedTopics: ["cpu-performance-mpstat-top", "benchmarking-tools"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#performance-tuning-methodology"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "21-performance/cpu-performance-mpstat-top"
prevTopic: "20-monitoring/log-based-monitoring"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["linux performance tuning methodology", "measure before tuning", "use method linux", "performance bottleneck identification"]
canonicalUrl: "/docs/performance/performance-tuning-overview"
---

# Performance Tuning Overview

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Backend

> **TL;DR:** Measure before you tune — guessing which resource is the bottleneck wastes effort and
> can make things worse. This module examines CPU, memory, disk I/O, and network one at a time,
> each with the tool that actually measures it.

## What is it?

The discipline of identifying an actual, measured bottleneck before changing anything — and the
map of which resource dimension (CPU, memory, disk, network) each of this module's tools measures.

## Why does it exist?

Performance problems have specific, measurable causes, but the intuitive guess ("it's probably the
CPU") is wrong often enough that acting on it wastes time and can introduce new problems without
fixing the original one. [Monitoring](../20-monitoring/index.md) taught continuous observation;
this module teaches the deeper, targeted investigation that begins once monitoring has already
flagged that something is wrong.

## Where is it used?

Any performance investigation — "this endpoint is slow," "this batch job takes longer than it
used to," "this server feels sluggish" — where the first real step is determining which resource
is actually constrained, not applying a familiar fix on assumption.

## How it works

> 📊 Diagram: a decision flow starting at "something is slow" branching into four resource
> investigation paths — CPU ([mpstat/top](cpu-performance-mpstat-top.md)), memory
> ([free/vmstat](memory-performance-free-vmstat.md)), disk I/O
> ([iostat/iotop](disk-io-performance-iostat-iotop.md)), network
> ([iperf/sar](network-performance-iperf-sar.md)) — each path requiring its own measurement before
> any tuning action is taken, converging at "confirmed bottleneck" only after data, not assumption.

**The measure-first principle:** never change a system configuration to "fix" a performance
problem before confirming, with actual tool output, which resource is genuinely constrained. A
common industry framing (Brendan Gregg's USE Method) checks each resource for **U**tilization,
**S**aturation, and **E**rrors — a repeatable checklist rather than intuition.

**This module's structure — one resource dimension at a time:**

| Dimension | Tools | What "saturated" looks like |
|---|---|---|
| CPU | [`mpstat`, `top`](cpu-performance-mpstat-top.md) | Sustained high utilization, growing run queue |
| Memory | [`free`, `vmstat`](memory-performance-free-vmstat.md) | Low available memory, active swapping |
| Disk I/O | [`iostat`, `iotop`](disk-io-performance-iostat-iotop.md) | High `%util`, rising `await` |
| Network | [`iperf`, `sar`](network-performance-iperf-sar.md) | Throughput near link capacity, rising latency |

Each of these overlaps with [Module 20's](../20-monitoring/top-htop-vmstat-iostat.md) tools —
performance tuning is the deeper, deliberate application of the same measurement tools monitoring
already introduced, now used to confirm a specific hypothesis rather than watch continuously.

## Real-world example

A team notices requests are slow and immediately adds more CPU cores to the server, assuming
compute is the bottleneck — but the real cause was disk I/O saturation from an inefficient
database query, and the extra CPU cores changed nothing. Confirming the bottleneck with `iostat`
first (high `%util`, rising `await` on the database's disk) before taking any action would have
directed the fix toward the actual query optimization needed, avoiding the wasted cost and time of
scaling the wrong resource.

## Syntax

No single syntax — this page is conceptual; each following page introduces its dimension's actual
tools.

## Commands

No command example on this page — see
[CPU Performance: mpstat, top](cpu-performance-mpstat-top.md) for the first ones.

## Production example

```
$ uptime
 14:32:10 up 45 days,  3:12,  2 users,  load average: 8.42, 7.95, 6.10
```

A load average well above the number of available CPU cores is a signal to investigate further —
but on its own doesn't yet confirm *which* resource is saturated, motivating the dimension-by-dimension
approach this module takes.

## Do / Don't

| Do | Don't |
|---|---|
| Measure and confirm a bottleneck before making a change | Act on an intuitive guess about which resource is the problem |
| Check utilization, saturation, and errors for each candidate resource | Stop investigating the moment one resource looks slightly elevated |
| Treat performance tuning as data-driven troubleshooting | Treat it as applying familiar fixes regardless of the actual cause |

## Common mistakes

- Acting on an assumption about the bottleneck (usually "it's the CPU") without measuring first,
  wasting effort on a change that doesn't address the real cause.
- Stopping investigation at the first resource that looks even slightly elevated, without
  confirming it's actually the dominant constraint.
- Conflating monitoring (continuous observation, Module 20) with performance tuning (targeted,
  deliberate investigation once a problem is already suspected) as the same activity.

## Best practices

- Always measure before changing anything — a five-minute check with the right tool is cheaper
  than an ineffective, potentially disruptive change.
- Use a repeatable framework (utilization, saturation, errors) across each resource dimension
  rather than an ad hoc, intuition-driven search.
- Treat this module's tools as targeted investigation, building on but distinct from Module 20's
  continuous monitoring.

## Exercises

1. Explain in your own words why acting on an assumption about a bottleneck can be worse than
   doing nothing.
2. Describe the utilization/saturation/errors framing for any one resource dimension.
3. Explain how this module's tools relate to, but differ in purpose from, Module 20's monitoring
   tools.

## Quiz

**Q: What is the measure-first principle in performance tuning?**
<details><summary>Show answer</summary>
Confirming which resource is actually constrained, using real tool output, before making any
configuration change — rather than acting on an intuitive guess.
</details>

**Q: What three things does the USE Method check for each resource?**
<details><summary>Show answer</summary>
Utilization, Saturation, and Errors — a repeatable checklist applied to each candidate resource
(CPU, memory, disk, network) rather than relying on intuition.
</details>

**Q: How does performance tuning differ from the monitoring covered in Module 20?**
<details><summary>Show answer</summary>
Monitoring is continuous, ongoing observation; performance tuning is the deeper, deliberate
investigation applied once a specific problem is already suspected, using many of the same
underlying tools.
</details>

## Interview questions

- Walk through your methodology for diagnosing a "slow server" report. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Measure before tuning — never act on an assumption about which resource is the bottleneck.
- The USE Method (Utilization, Saturation, Errors) gives a repeatable framework for checking each
  resource.
- This module examines CPU, memory, disk I/O, and network one at a time, each with its own tools.
- Performance tuning is the targeted, deliberate counterpart to Module 20's continuous monitoring.

## Related topics

- [CPU Performance: mpstat, top](cpu-performance-mpstat-top.md)
- [Benchmarking Tools](benchmarking-tools.md)
