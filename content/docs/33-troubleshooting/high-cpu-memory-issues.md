---
title: "High CPU / Memory Issues"
description: "How to diagnose whether a server's slowness is actually a CPU or memory problem, find the specific process responsible, and tell a genuine leak from normal steady-state usage."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["21-performance/cpu-performance-mpstat-top", "21-performance/memory-performance-free-vmstat"]
relatedTopics: ["33-troubleshooting/disk-full-issues", "33-troubleshooting/service-wont-start"]
relatedCommands: ["top", "vmstat", "ps"]
careerRelevance: ["sre", "devops", "platform"]
relatedLabs: ["advanced/debug-a-memory-leak-with-top-vmstat-journalctl"]
relatedInterviewQuestions: ["scenario-based#high-load-average-low-cpu-utilization", "scenario-based#oom-killer-terminated-wrong-process"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "33-troubleshooting/service-wont-start"
prevTopic: "33-troubleshooting/disk-full-issues"
estimatedReadingTime: 8
updatedAt: "2026-07-30"
keywords: ["high cpu usage linux troubleshooting", "high memory usage linux", "diagnose memory leak linux", "load average vs cpu utilization"]
canonicalUrl: "/docs/troubleshooting/high-cpu-memory-issues"
---

# High CPU / Memory Issues

🟡 Good to Know · Relevant for: SRE · DevOps · Platform

> **TL;DR:** "Slow" isn't a diagnosis — confirm whether it's actually CPU-bound, memory-bound, or
> I/O-bound first, then find the specific process. A genuine leak shows a trend over time; a
> single snapshot can't tell you that.

## What is it?

A diagnostic approach for "the server is slow" or "a service keeps getting killed" that starts by
identifying *which* resource is actually the bottleneck — CPU, memory, or something else entirely
masquerading as one of them — before looking at any specific process.

## Why does it exist?

"High load" and "high CPU usage" are often assumed to be the same thing, and they aren't — a
server can show a high load average while CPU sits mostly idle, because load average counts
processes waiting on *any* resource, including disk I/O. Diagnosing CPU tuning on what's actually
a disk I/O problem wastes time and misses the real cause. Memory has its own version of this trap:
"free" memory looking low is often just disk caching working as designed, not a shortage.

## Where is it used?

Any "the server is slow" report, a service being repeatedly OOM-killed, or a monitoring alert on
CPU/memory thresholds that needs a specific process identified before it can be fixed.

## How it works

> 📊 Diagram: a decision tree — "System feels slow" at the top, branching to "Check load average
> vs CPU utilization (`top`, `vmstat`)" → if load is high but CPU is idle, branch to "check I/O
> wait instead"; if CPU genuinely busy, branch to "find the process (`ps aux --sort=-%cpu`)"; a
> parallel branch for memory — "check `free`'s available column, not just 'free'" → "watch RSS of
> the suspect process over time to confirm a trend, not a snapshot."

1. **Confirm which resource is actually the bottleneck.** `vmstat 2 5` shows CPU, memory, and I/O
   wait side by side — a high `wa` (I/O wait) column with modest CPU usage means the bottleneck is
   disk, not CPU, no matter how "busy" the system looks.
2. **For CPU: find the specific process** with `ps aux --sort=-%cpu` or `top` sorted by CPU — a
   system-wide "high CPU" report almost always traces back to one or two processes, not a general
   load.
3. **For memory: check `available`, not `free`.** Linux uses spare RAM for disk caching by design;
   the `available` column in `free -h` is the number that reflects genuinely reclaimable memory.
4. **Distinguish a leak from normal usage by watching a trend, not a snapshot** — sample the same
   process's RSS every few seconds over a couple of minutes. Usage that plateaus is normal; usage
   that climbs without ever leveling off is a leak.
5. **Check kernel logs for past OOM kills** (`journalctl -k | grep -i oom`) if a process has been
   dying unexpectedly — the kernel logs exactly which process it killed and why.

## Real-world example

A worker process gets OOM-killed roughly once a day. A single `top` snapshot right after restart
shows nothing unusual — memory looks fine. Watching the same process's RSS every few seconds over
several minutes reveals it climbing steadily with no plateau, confirming a genuine leak rather
than a one-time spike, which a single snapshot could never have shown.

## Commands

See [`top`](../../commands/top.md), [`vmstat`](../../commands/vmstat.md), and
[`ps`](../../commands/ps.md) for full flag references.

## Production example

```
$ vmstat 2 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  4      0  102340  22040 512300    0    0   820   140  210  480  8  4 10 78  0

$ free -h
              total        used        free      shared  buff/cache   available
Mem:           7.8G        1.2G        200M        50M        6.4G        6.1G
```

The `wa` (I/O wait) column at 78% with only 8% actual CPU usage tells the real story: this system
isn't CPU-constrained at all, it's waiting on disk. And despite "free" showing only 200M, the
`available` column (6.1G) confirms there's no genuine memory shortage — most of that memory is
reclaimable disk cache.

## Do / Don't

| Do | Don't |
|---|---|
| Check `vmstat`'s `wa` column before assuming a CPU problem | Tune CPU/kernel settings for what's actually a disk I/O bottleneck |
| Read `free`'s `available` column, not `free` | Panic over a low "free" number that's just disk cache |
| Watch a process's memory over time to confirm a leak | Diagnose a leak from a single snapshot |
| Check `journalctl -k` for OOM history | Assume a process crash was random without checking kernel logs |

## Common mistakes

- Treating a high load average as automatically a CPU problem, when it's often disk or network
  I/O wait instead.
- Reading `free`'s "free" column as the memory-shortage indicator instead of "available."
- Declaring a "memory leak" from one `top` snapshot instead of a sustained upward trend.
- Not checking `journalctl -k` when a process has been mysteriously dying — the OOM killer logs
  exactly what it killed and why.

## Best practices

- Always check `vmstat`'s CPU/wait breakdown before assuming which resource is the bottleneck.
- Sample a suspect process's memory over several minutes before calling something a leak.
- Set `oom_score_adj` deliberately for processes that must survive memory pressure, rather than
  discovering the kernel's default priority the hard way.
- Alert on the `available` memory metric, not raw "free," to avoid false alarms from normal
  caching behavior.

## Exercises

1. Run `vmstat 2 5` on any system and identify which column would tell you if I/O, not CPU, is the
   bottleneck.
2. Explain in one sentence why `free`'s "free" column can look alarmingly low on a perfectly
   healthy system.
3. Write the one-line `ps` command that lists the top 5 processes by memory usage.

## Quiz

**Q: A server shows a high load average but `top` shows CPU mostly idle. What should you check next?**
<details><summary>Show answer</summary>
Disk I/O wait (`vmstat`'s `wa` column) — load average counts processes waiting on any resource,
not just CPU, so a high load with idle CPU usually points to I/O-bound processes instead.
</details>

**Q: Why can't a single `top` snapshot confirm a memory leak?**
<details><summary>Show answer</summary>
Because it shows a moment in time, not a trend — a genuine leak is defined by memory usage that
climbs without ever plateauing, which requires watching the same process over multiple samples.
</details>

## Interview questions

- `uptime` shows a load average of 40 on an 8-core box, but `top` shows CPU sitting mostly idle.
  What's going on? → [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#high-load-average-low-cpu-utilization)
- The OOM killer terminated your database process instead of a memory-hogging batch job — why? →
  [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#oom-killer-terminated-wrong-process)

## Key Takeaways

- Confirm which resource is actually the bottleneck (`vmstat`) before assuming CPU or memory.
- Read `free`'s "available" column, not "free" — most of "used" memory is often reclaimable cache.
- A genuine leak is a trend over time, not a single high reading.
- `journalctl -k` shows past OOM kills and exactly which process was terminated.

## Related topics

- [Disk Full Issues](disk-full-issues.md)
- [Service Won't Start](service-wont-start.md)
- [Performance](../21-performance/index.md)
