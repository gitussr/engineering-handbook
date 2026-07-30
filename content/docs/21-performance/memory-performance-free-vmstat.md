---
title: "Memory Performance: free, vmstat"
description: "Reading free's available column correctly (not free), and using vmstat's si/so swap columns to confirm genuine memory pressure versus healthy cache usage."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["21-performance/cpu-performance-mpstat-top"]
relatedTopics: ["cpu-performance-mpstat-top", "disk-io-performance-iostat-iotop"]
relatedCommands: ["free", "vmstat"]
careerRelevance: ["devops", "sre", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#free-vs-available-memory"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "21-performance/disk-io-performance-iostat-iotop"
prevTopic: "21-performance/cpu-performance-mpstat-top"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["free command available vs free column", "linux memory usage explained", "buff cache vs used memory", "vmstat swap pressure"]
canonicalUrl: "/docs/performance/memory-performance-free-vmstat"
---

# Memory Performance: free, vmstat

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Backend

> **TL;DR:** `free -h`'s `available` column — not `free` — is the number that actually matters;
> Linux deliberately uses "free" RAM for disk cache, which isn't a problem and doesn't mean memory
> is scarce. `vmstat`'s `si`/`so` columns (already introduced in Module 20) confirm genuine
> memory pressure.

## What is it?

Diagnosing memory as a performance bottleneck specifically, centered on `free` (new to this
documentation) and `vmstat` (already covered in
[Module 20](../20-monitoring/top-htop-vmstat-iostat.md)) — and correcting the single most common
misreading of Linux memory output.

## Why does it exist?

Linux's memory management deliberately uses "unused" RAM for disk caching, making a system look
like it's nearly out of memory when it's actually operating exactly as designed. Understanding
`free`'s columns correctly is the difference between a false alarm and catching a genuine memory
problem.

## Where is it used?

Any time a system appears to have very little "free" memory and someone wonders if that's a
problem — almost always it isn't, and this page's job is knowing how to tell the difference from
output that genuinely does indicate memory pressure.

## How it works

> 📊 Diagram: a single bar representing total RAM, divided into segments — "used" (actively held
> by processes), "buff/cache" (disk cache, reclaimable on demand), and "available" (what's
> actually free for new processes, including the reclaimable cache portion) — visually showing why
> `available`, not `free`, is the number that matters.

**`free -h` — reading the columns correctly:**

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           15Gi        4.2Gi       512Mi        45Mi        10Gi        11Gi
Swap:         2.0Gi          0B       2.0Gi
```

| Column | Meaning |
|---|---|
| `used` | Memory actively held by running processes |
| `free` | Memory not being used for anything at all — deliberately kept low by design, since Linux uses spare RAM for disk cache rather than leaving it idle |
| `buff/cache` | Memory used for disk caching — reclaimed automatically and instantly if a process needs it |
| `available` | The number that actually answers "how much memory can a new process get" — `free` plus the reclaimable portion of `buff/cache` |

A low `free` value with a high `available` value (as in the example above) is completely normal
and healthy — the system is using spare RAM efficiently for caching, not running low on memory.

**Genuine memory pressure — what to check instead:**

- `available` itself trending low over time (not `free`).
- Non-zero, *sustained* `si`/`so` in `vmstat` (Module 20) — actual swap activity, the clearest
  sign of real memory pressure.
- `Swap: used` climbing in `free`'s second row.

## Real-world example

A new engineer sees `free`'s `free` column showing only 512MB out of 15GB total RAM and raises an
urgent alarm about a memory shortage — but the `available` column shows 11GB genuinely available,
and `vmstat`'s `si`/`so` are both zero, confirming no actual memory pressure exists. The system is
simply using otherwise-idle RAM for disk cache, exactly as Linux is designed to do. Understanding
this distinction prevents a false-alarm incident and the wasted effort of "fixing" a system that
was never actually broken.

## Syntax

```
free [-h] [-s SECONDS]
vmstat [INTERVAL] [COUNT]
```

## Commands

See [`free`](../../commands/free.md) for the full reference, and
[`vmstat`](../../commands/vmstat.md) (Module 20, already covered) for the swap-activity
confirmation.

## Production example

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           15Gi         14Gi       200Mi        50Mi       800Mi       450Mi

$ vmstat 2 3
procs -----------memory---------- ---swap--
 r  b   swpd   free   buff  cache   si   so
 3  2  512000 200000  80000 800000  850  920
 5  3  580000 190000  78000 795000  900  980
 4  3  610000 185000  77000 790000  880  950
```

This time `available` is also genuinely low (450Mi), and `vmstat` shows sustained, substantial
`si`/`so` — a real, confirmed memory-pressure situation, not a caching false alarm.

## Do / Don't

| Do | Don't |
|---|---|
| Read `available`, not `free`, as the meaningful memory number | Alarm on a low `free` value alone |
| Confirm memory pressure with sustained `si`/`so` in `vmstat` | Conclude memory pressure from `free`'s output alone without checking swap activity |
| Expect `buff/cache` to be reclaimed automatically when needed | Treat cache usage as memory unavailable to applications |

## Common mistakes

- Alarming over a low `free` column value without checking `available`, the number that actually
  matters.
- Not cross-checking with `vmstat`'s `si`/`so` before concluding genuine memory pressure exists.
- Assuming memory used for `buff/cache` is unavailable to applications, when it's automatically
  reclaimed the instant a process needs it.

## Best practices

- Always read `available`, not `free`, when assessing memory headroom.
- Confirm suspected memory pressure with `vmstat`'s sustained `si`/`so` activity, not `free`'s
  output in isolation.
- Explain the `free`-vs-`available` distinction proactively to teammates new to Linux memory
  output — it's one of the most common sources of unnecessary alarm.

## Exercises

1. Run `free -h` on a system and identify whether `available` or `free` gives a healthier picture.
2. Explain in your own words why Linux deliberately keeps the `free` column low by design.
3. Describe what combination of `free` and `vmstat` output would confirm genuine memory pressure.

## Quiz

**Q: Why is a low value in free's "free" column often not a problem?**
<details><summary>Show answer</summary>
Linux deliberately uses otherwise-idle RAM for disk caching (shown in `buff/cache`), which is
instantly reclaimable — the `available` column, not `free`, reflects what's actually usable by new
processes.
</details>

**Q: What vmstat columns confirm genuine memory pressure?**
<details><summary>Show answer</summary>
`si` and `so` (swap in/out) — sustained non-zero values here indicate the system is actively
swapping, a clear, real sign of memory pressure.
</details>

**Q: What happens to buff/cache memory when a process needs more RAM?**
<details><summary>Show answer</summary>
It's reclaimed automatically and immediately — cache memory isn't unavailable to applications, it's
just being used productively until something else needs it.
</details>

## Interview questions

- A server shows very little "free" memory — is that necessarily a problem? Explain why or why
  not. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Read `free`'s `available` column, not `free`, to assess real memory headroom.
- A low `free` value with high `buff/cache` and `available` is normal, healthy behavior, not a
  problem.
- Confirm genuine memory pressure with sustained `si`/`so` swap activity in `vmstat`.
- `buff/cache` memory is reclaimed instantly on demand — it isn't unavailable to applications.

## Related topics

- [CPU Performance: mpstat, top](cpu-performance-mpstat-top.md)
- [Disk I/O Performance: iostat, iotop](disk-io-performance-iostat-iotop.md)
