---
title: "free — Display Memory Usage"
description: "A snapshot of total, used, free, cached, and available memory — reading the available column correctly is the single most common Linux memory-usage misunderstanding."
relatedConcepts: ["21-performance/memory-performance-free-vmstat", "15-storage/swap-space"]
relatedCommands: ["vmstat", "top"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["free command linux", "free -h", "available vs free memory", "free command swap"]
canonicalUrl: "/commands/free"
---

# free

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `free -h` shows memory usage in human-readable units. Read the `available` column,
> not `free` — Linux deliberately uses spare RAM for disk cache, making `free` alone look
> misleadingly low.

## Purpose

`free` displays a snapshot of system memory and swap usage — see
[Memory Performance: free, vmstat](../docs/21-performance/memory-performance-free-vmstat.md) for
the full concept and how to read its columns correctly.

## Syntax

```
free [OPTIONS]
```

## Arguments

None — entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `-h` | Human-readable units (GiB/MiB instead of raw KB) |
| `-s SECONDS` | Repeat the report every SECONDS, like a simple continuous monitor |
| `-t` | Include a total line combining memory and swap |
| `-w` | Wide output, separating buffers and cache into distinct columns |

## Examples

```
$ free -h
```
Show current memory and swap usage in human-readable units.

```
$ free -s 2
```
Repeat the report every 2 seconds, useful for watching memory usage change live.

```
$ free -h -t
```
Include a combined memory+swap total line.

## Expected Output

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           15Gi        4.2Gi       512Mi        45Mi        10Gi        11Gi
Swap:         2.0Gi          0B       2.0Gi
```

## Exit Status

`0` on success, non-zero on invalid option syntax.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| "Free" memory looks alarmingly low | Normal — Linux uses spare RAM for disk cache (`buff/cache`), reclaimable instantly | Read the `available` column instead, which already accounts for reclaimable cache |
| Swap shows non-zero `used` | Some swap activity has occurred | Not automatically a problem on its own — confirm with [`vmstat`](vmstat.md)'s `si`/`so` whether it's ongoing/active or historical |

## Security Considerations

No special considerations — `free` reports aggregate system memory statistics only.

## Performance Considerations

Negligible overhead — a simple, near-instantaneous read of kernel memory statistics.

## Production Usage

`free -h` is typically the very first command run when investigating a suspected memory issue,
immediately followed by cross-checking `available` (not `free`) and, if pressure is suspected,
[`vmstat`](vmstat.md)'s swap activity columns for confirmation.

## Related Commands

- [`vmstat`](vmstat.md) — confirms whether low `available` memory is accompanied by actual swap
  activity
- [`top`](top.md) — per-process memory usage, complementary to `free`'s system-wide view

## Related Concepts

- [Memory Performance: free, vmstat](../docs/21-performance/memory-performance-free-vmstat.md)
- [Swap Space](../docs/15-storage/swap-space.md)
