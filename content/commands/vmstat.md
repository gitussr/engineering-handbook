---
title: "vmstat — Report Virtual Memory, CPU, and Swap Statistics"
description: "One compact summary line per interval covering the whole system's process queue, memory, swap activity, I/O, and CPU — the fastest way to check for memory pressure."
relatedConcepts: ["20-monitoring/top-htop-vmstat-iostat", "20-monitoring/monitoring-fundamentals"]
relatedCommands: ["top", "iostat"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["vmstat command", "vmstat si so swap", "vmstat interval count", "vmstat columns explained"]
canonicalUrl: "/commands/vmstat"
---

# vmstat

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `vmstat 2 5` prints five summary lines, two seconds apart, covering the whole
> system's runnable/blocked processes, memory, swap activity, and CPU — sustained non-zero
> `si`/`so` means the system is actively swapping.

## Purpose

`vmstat` reports system-wide virtual memory, CPU, and swap statistics — see
[top, htop, vmstat, iostat](../docs/20-monitoring/top-htop-vmstat-iostat.md) for the full concept
and how it complements process-level tools.

## Syntax

```
vmstat [OPTIONS] [INTERVAL] [COUNT]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `INTERVAL` | Seconds between each report line | No — omitting it prints one report and exits |
| `COUNT` | Number of report lines to print before stopping | No — omitting it (with `INTERVAL` given) repeats indefinitely |

## Options

| Flag | Meaning |
|---|---|
| `-a` | Show active/inactive memory breakdown instead of the default buffer/cache columns |
| `-s` | Show a table of memory-related event counters instead of the periodic summary format |
| `-d` | Show per-disk statistics (overlaps with `iostat`'s more detailed view) |
| `-w` | Wide output — improves readability of column alignment |

## Examples

```
$ vmstat
```
Print a single summary line covering activity since boot.

```
$ vmstat 2 5
```
Print five summary lines, two seconds apart — the standard way to observe a live trend rather than
one static point.

```
$ vmstat -a 2 5
```
Same repeated sampling, showing active/inactive memory breakdown instead of buffer/cache.

```
$ vmstat -s
```
Show cumulative memory event counters (total page faults, etc.) rather than the periodic summary.

## Expected Output

```
$ vmstat 2 3
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 2048000  85000 3200000    0    0    15    10  120  250 12  3 84  1  0
 2  0      0 2035000  85200 3201000    0    0    18    12  118  245 14  4 81  1  0
 1  0      0 2040000  85100 3200500    0    0    16    11  115  240 13  3 83  1  0
```

## Exit Status

`0` on success, non-zero on invalid option syntax.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| First line looks like an outlier compared to the rest | The first line is a summary since boot, not a live sample | Ignore the first line when sampling repeatedly; treat lines 2+ as the live trend |
| Confusing `si`/`so` with `bi`/`bo` | Similar-looking column names | `si`/`so` are swap in/out; `bi`/`bo` are block device in/out (disk I/O), a different thing entirely |
| Command not found | `sysstat` (or equivalent) package not installed | Install via the distro's package manager (Module 14) |

## Security Considerations

No special considerations — `vmstat` is read-only and reports aggregate system statistics rather
than per-user or per-process sensitive detail.

## Performance Considerations

`vmstat` itself has negligible overhead, making it safe to run continuously even on a
already-struggling system — one of the reasons it's a standard first diagnostic step.

## Production Usage

`vmstat 2 5` (or similar) is a standard first command when a system "feels slow" and `top` doesn't
show an obvious CPU-hogging process — quickly confirming or ruling out memory pressure/swapping as
the cause before investigating further.

## Related Commands

- [`top`](top.md) — per-process view, complementary to `vmstat`'s system-wide summary
- [`iostat`](iostat.md) — per-device I/O detail beyond `vmstat`'s basic `bi`/`bo` columns
- `free` — a simpler, memory-only snapshot without `vmstat`'s process-queue and CPU columns
  (covered within [Module 15's swap topic](../docs/15-storage/swap-space.md); no separate
  canonical page — see that module's intentional gaps)

## Related Concepts

- [top, htop, vmstat, iostat](../docs/20-monitoring/top-htop-vmstat-iostat.md)
- [Monitoring Fundamentals](../docs/20-monitoring/monitoring-fundamentals.md)
