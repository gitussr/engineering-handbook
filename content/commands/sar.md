---
title: "sar — Report Historical System Activity"
description: "Query continuously logged historical performance data across CPU, memory, disk, and network — the only tool in this documentation that can look at the past, not just the present."
relatedConcepts: ["21-performance/network-performance-iperf-sar"]
relatedCommands: ["mpstat", "vmstat", "iostat"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["sar command examples", "sar historical cpu usage", "sysstat sar setup", "sar time range"]
canonicalUrl: "/commands/sar"
---

# sar

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `sar -u -s 09:00:00 -e 10:00:00` reports CPU usage for a specific past time window —
> but only if `sysstat`'s continuous data collection was already enabled before that window. Enable
> it proactively; it can't be turned on retroactively for a period that's already passed.

## Purpose

`sar` reports historical system performance data collected continuously by the `sysstat`
package — see
[Network Performance: iperf, sar](../docs/21-performance/network-performance-iperf-sar.md) for the
full concept.

## Syntax

```
sar [-u|-r|-d|-n DEV] [-s TIME] [-e TIME] [INTERVAL] [COUNT]
```

## Arguments

None — entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `-u` | CPU utilization report |
| `-r` | Memory utilization report |
| `-d` | Disk activity report (device names may show as numeric identifiers unless `-p` is also given) |
| `-n DEV` | Network device statistics |
| `-s TIME` | Start time for the report (e.g. `09:00:00`) |
| `-e TIME` | End time for the report |
| `-f FILE` | Read from a specific historical data file instead of today's default |

## Examples

```
$ sar -u
```
Show today's CPU utilization report so far, at the default logging interval.

```
$ sar -u -s 09:00:00 -e 10:00:00
```
Show CPU utilization specifically between 9:00 and 10:00 AM today.

```
$ sar -r 1 5
```
Show five live memory utilization samples, one second apart (like `vmstat`, but from `sar`'s
reporting format).

```
$ sar -n DEV -f /var/log/sysstat/sa15
```
Show network device statistics from a specific archived data file (the 15th of the month, in
`sysstat`'s default naming).

## Expected Output

```
$ sar -u -s 14:00:00 -e 14:30:00
14:00:01        CPU     %user     %system   %iowait     %idle
14:05:01        all     72.30       8.20       2.10      17.40
14:10:01        all     68.50       7.90       1.80      21.80
```

## Exit Status

`0` on success, non-zero if no data exists for the requested range or the syntax is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Cannot find the specified data file` | `sysstat`'s data collection wasn't enabled, or the requested date's file has aged out | Enable `sysstat` collection proactively going forward; historical gaps can't be filled retroactively |
| No data for a requested time range | The collection service wasn't running during that specific window | Confirm the `sysstat` collection job ([Module 17](../docs/17-cron/index.md) cron/timer) was active throughout the desired range |
| Command not found | `sysstat` package not installed | Install via the distro's package manager (Module 14) |

## Security Considerations

Historical performance data can reveal usage patterns (e.g. exactly when backups or batch jobs
run) — treat archived `sar` data with the same care as other operational logs when working in
security-sensitive contexts.

## Performance Considerations

`sysstat`'s background collection has minimal ongoing overhead; querying historical data with
`sar` itself is a fast, read-only operation regardless of how much history has accumulated.

## Production Usage

`sar` is most valuable when its continuous collection has been enabled from day one on every
production system — the standard practice is enabling `sysstat`'s data collection as part of
initial provisioning, alongside monitoring agent setup ([Module 20](../docs/20-monitoring/setting-up-a-basic-monitoring-agent.md)),
so historical data is always available if an investigation later needs it.

## Related Commands

- [`mpstat`](mpstat.md) — live per-core CPU view, complementary to `sar`'s historical CPU report
- [`vmstat`](vmstat.md) — live memory/swap view, complementary to `sar`'s historical memory report
- [`iostat`](iostat.md) — live per-device I/O view, complementary to `sar`'s historical disk report

## Related Concepts

- [Network Performance: iperf, sar](../docs/21-performance/network-performance-iperf-sar.md)
