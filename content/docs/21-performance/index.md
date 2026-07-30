---
title: "Performance"
description: "Module 21 of the Linux roadmap — measure-first tuning methodology, CPU/memory/disk/network performance diagnosis, kernel tuning with sysctl, and validating changes with benchmarks."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
type: "module-index"
nextTopic: "21-performance/performance-tuning-overview"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/performance"
---

# Performance

Module 21 of 34 · Stage: Professional · Previous: [20 Monitoring](../20-monitoring/index.md)

Module 20 built the habit of continuous observation; this module builds the deeper, deliberate
investigation that begins once monitoring has already flagged a problem. Each resource dimension —
CPU, memory, disk I/O, network — gets its own diagnostic escalation, kernel tuning closes the loop
with `sysctl`, and benchmarking validates that a change actually helped rather than just assuming
it did.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Performance Tuning Overview](performance-tuning-overview.md) | 🟡 Good to Know |
| [CPU Performance: mpstat, top](cpu-performance-mpstat-top.md) | 🟡 Good to Know |
| [Memory Performance: free, vmstat](memory-performance-free-vmstat.md) | 🟡 Good to Know |
| [Disk I/O Performance: iostat, iotop](disk-io-performance-iostat-iotop.md) | 🔴 Expert |
| [Network Performance: iperf, sar](network-performance-iperf-sar.md) | 🔴 Expert |
| [Kernel Tuning: sysctl](kernel-tuning-sysctl.md) | 🔴 Expert |
| [Benchmarking Tools](benchmarking-tools.md) | 🔴 Expert |

## What you should be able to do after this module

- Apply a measure-first methodology, confirming a bottleneck before making any change.
- Diagnose CPU bottlenecks with `mpstat`'s per-core view and correctly interpret load average
  relative to core count.
- Read `free`'s `available` column correctly and distinguish healthy cache usage from genuine
  memory pressure.
- Escalate from `iostat`'s device-level confirmation to `iotop`'s per-process identification.
- Measure real network throughput with `iperf3` and review historical trends with `sar`.
- Tune kernel parameters with `sysctl`, understanding the runtime-vs-persistent distinction.
- Validate a tuning change with a reproducible before/after benchmark rather than assuming it
  helped.

## Known, intentional gaps in this module

- Canonical command pages exist for [`mpstat`](../../commands/mpstat.md),
  [`free`](../../commands/free.md), [`iotop`](../../commands/iotop.md),
  [`iperf`](../../commands/iperf.md), [`sar`](../../commands/sar.md), and
  [`sysctl`](../../commands/sysctl.md) — every command explicitly named in this module's roadmap
  bullets. `free` was previously deferred from Module 15's swap topic; this is its owning module,
  since this is the first roadmap bullet to name it explicitly in backticks.
- `top` (Modules 10/20) and `vmstat`/`iostat` (Module 20) are reused without a new page, since
  this module applies them in a deeper diagnostic context rather than introducing new flags.
- `fio` and `sysbench` (Benchmarking Tools) are specialized, situational tools shown
  illustratively without canonical command pages — this roadmap bullet names a concept
  ("Benchmarking tools"), not specific commands in backticks, consistent with the scoping rule
  established in Module 15.

**Previous module:** [20 Monitoring](../20-monitoring/index.md)
**Next module:** [22 Containers →](../22-containers/index.md)
