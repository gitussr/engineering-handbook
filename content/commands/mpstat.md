---
title: "mpstat — Report Per-Processor CPU Statistics"
description: "CPU utilization broken down per core, revealing a single saturated core hidden inside a healthy-looking system-wide average."
relatedConcepts: ["21-performance/cpu-performance-mpstat-top"]
relatedCommands: ["top", "vmstat"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["mpstat command", "mpstat -P ALL", "per core cpu usage linux", "mpstat iowait"]
canonicalUrl: "/commands/mpstat"
---

# mpstat

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `mpstat -P ALL 2` shows per-core CPU utilization every two seconds — essential for
> spotting a single maxed-out core that a system-wide average would hide entirely.

## Purpose

`mpstat` reports CPU utilization broken down per processor core — see
[CPU Performance: mpstat, top](../docs/21-performance/cpu-performance-mpstat-top.md) for the full
concept.

## Syntax

```
mpstat [-P {CPU|ALL}] [INTERVAL] [COUNT]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `INTERVAL` | Seconds between each report | No — omitting it prints one report covering time since boot |
| `COUNT` | Number of reports before stopping | No — omitting it (with `INTERVAL` given) repeats indefinitely |

## Options

| Flag | Meaning |
|---|---|
| `-P ALL` | Show statistics for every individual core, not just the system-wide average |
| `-P CPU` | Show statistics for one specific core number |
| `-u` | Report CPU utilization (the default report type) |

## Examples

```
$ mpstat
```
Print a single system-wide average report since boot.

```
$ mpstat -P ALL 2 5
```
Print five per-core reports, two seconds apart — the standard way to check for a single saturated
core.

```
$ mpstat -P 0 2
```
Repeatedly report statistics for core 0 specifically.

## Expected Output

```
$ mpstat -P ALL 2 1
CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest   %idle
all    25.30    0.00    5.20    2.10    0.00    0.50    0.00    0.00   66.90
  0    98.50    0.00    1.00    0.00    0.00    0.50    0.00    0.00    0.00
  1     5.20    0.00    3.10    1.50    0.00    0.20    0.00    0.00   90.00
```

## Exit Status

`0` on success, non-zero on invalid option syntax.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Command not found | `sysstat` package not installed | Install via the distro's package manager (Module 14) |
| System-wide average looks fine but users report slowness | A single core may be saturated while others idle | Use `-P ALL` to check per-core, not just the `all` summary row |
| High `%iowait` misread as a CPU problem | `%iowait` means the CPU is idle waiting on I/O, not doing work | Investigate disk I/O ([`iostat`](iostat.md)), not CPU capacity |

## Security Considerations

No special considerations — `mpstat` is read-only and reports aggregate CPU statistics.

## Performance Considerations

Negligible overhead — safe to run continuously, including on an already-struggling system.

## Production Usage

`mpstat -P ALL` is the standard follow-up to a high load average or a suspected single-threaded
bottleneck, confirming per-core detail that `top`'s default view and the system-wide average both
obscure.

## Related Commands

- [`top`](top.md) — per-process view, complementary to `mpstat`'s per-core view
- [`vmstat`](vmstat.md) — system-wide memory/CPU/swap summary in one compact line

## Related Concepts

- [CPU Performance: mpstat, top](../docs/21-performance/cpu-performance-mpstat-top.md)
