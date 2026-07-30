---
title: "iostat — Report Per-Device Disk I/O Statistics"
description: "Per-device throughput, wait time, and utilization — the tool that reveals a disk I/O bottleneck that top and vmstat alone can't show in useful detail."
relatedConcepts: ["20-monitoring/top-htop-vmstat-iostat", "20-monitoring/monitoring-fundamentals"]
relatedCommands: ["vmstat", "top", "df"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["iostat command", "iostat -x extended", "iostat util await", "iostat interval count"]
canonicalUrl: "/commands/iostat"
---

# iostat

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `iostat -x 2` prints extended per-device I/O stats every two seconds. `%util` near
> 100% alongside rising `await` confirms a genuine disk I/O bottleneck on that specific device.

## Purpose

`iostat` reports per-device disk I/O throughput and utilization statistics — see
[top, htop, vmstat, iostat](../docs/20-monitoring/top-htop-vmstat-iostat.md) for the full concept
and how it complements `vmstat`'s system-wide view.

## Syntax

```
iostat [OPTIONS] [INTERVAL] [COUNT]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `INTERVAL` | Seconds between each report | No — omitting it prints one report since boot and exits |
| `COUNT` | Number of reports to print before stopping | No — omitting it (with `INTERVAL` given) repeats indefinitely |

## Options

| Flag | Meaning |
|---|---|
| `-x` | Extended statistics — includes `await` and `%util`, essential for real diagnosis |
| `-d` | Show only device statistics, omitting the CPU summary |
| `-c` | Show only the CPU summary, omitting device statistics |
| `-p DEVICE` | Show statistics for a specific device (or partition) only |
| `-h` | Human-readable units |

## Examples

```
$ iostat
```
Print a single basic report (CPU summary plus per-device stats since boot).

```
$ iostat -x 2 5
```
Print five extended reports, two seconds apart — the standard way to observe a live I/O trend
rather than a since-boot average.

```
$ iostat -xd 2
```
Extended, device-only statistics (no CPU summary), repeating every two seconds indefinitely.

```
$ iostat -p sda 2
```
Focus on a single device (`sda`) specifically, repeated every two seconds.

## Expected Output

```
$ iostat -x 2 2
Device            r/s     w/s     rkB/s     wkB/s   await  %util
sda              12.50   45.20    512.30   3200.10    8.20  65.30
sda              15.10   52.30    600.10   3450.00   12.50  78.90
```

Rising `await` alongside rising `%util` across the two samples — a device trending toward
saturation, not just a momentary blip.

## Exit Status

`0` on success, non-zero on invalid option syntax or if the underlying statistics can't be read.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Command not found | `sysstat` package not installed | Install via the distro's package manager (Module 14) |
| First report shows unusually high values | The first report without an interval covers the average since boot, not current activity | Always specify an `INTERVAL`/`COUNT` and treat the first line as a baseline, not a live reading |
| High `%util` but low `await` | The device is busy but requests aren't queuing/waiting long — not necessarily a real bottleneck | Confirm with `await` specifically before concluding saturation, not `%util` alone |

## Security Considerations

No special considerations — `iostat` is read-only and reports aggregate device statistics rather
than the content of any file being read or written.

## Performance Considerations

`iostat` itself has negligible overhead. `-x` (extended stats) is the flag that matters most in
practice — the basic report without it omits `await`, the column that actually distinguishes a
genuine bottleneck from a device simply handling high but healthy throughput.

## Production Usage

`iostat -x 2 5` is the standard follow-up the moment `vmstat` shows high `wa` (I/O wait) time,
pinpointing exactly which device is responsible rather than just confirming that I/O in general is
the bottleneck.

## Related Commands

- [`vmstat`](vmstat.md) — system-wide summary that often prompts reaching for `iostat`'s
  per-device detail
- [`top`](top.md) — per-process view; doesn't show per-device I/O in useful detail at all
- [`df`](df.md) — disk space usage, a distinct question from `iostat`'s I/O throughput/utilization

## Related Concepts

- [top, htop, vmstat, iostat](../docs/20-monitoring/top-htop-vmstat-iostat.md)
- [Monitoring Fundamentals](../docs/20-monitoring/monitoring-fundamentals.md)
