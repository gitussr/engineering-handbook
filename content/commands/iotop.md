---
title: "iotop — Display Per-Process Disk I/O Usage"
description: "A live, top-like view sorted by disk I/O, identifying exactly which process is responsible for I/O activity iostat has already confirmed is saturating a device."
relatedConcepts: ["21-performance/disk-io-performance-iostat-iotop"]
relatedCommands: ["iostat", "top"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["iotop command", "iotop -o only active", "find process causing disk io", "iotop batch mode"]
canonicalUrl: "/commands/iotop"
---

# iotop

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `sudo iotop -o` shows only processes currently doing disk I/O, sorted by activity —
> the per-process complement to [`iostat`](iostat.md)'s per-device confirmation that a bottleneck
> exists.

## Purpose

`iotop` displays a live, per-process view of disk I/O usage — see
[Disk I/O Performance: iostat, iotop](../docs/21-performance/disk-io-performance-iostat-iotop.md)
for the full concept and the two-step diagnosis pattern it's part of.

## Syntax

```
iotop [OPTIONS]
```

## Arguments

None.

## Options

| Flag | Meaning |
|---|---|
| `-o` | Only show processes actually performing I/O right now, hiding idle ones |
| `-a` | Show accumulated I/O since `iotop` started, instead of per-interval rate |
| `-b` | Batch (non-interactive) mode, suitable for scripting or a fixed number of iterations |
| `-n COUNT` | Number of iterations before exiting (used with `-b`) |
| `-p PID` | Monitor a specific process only |

## Examples

```
$ sudo iotop -o
```
Show only currently I/O-active processes, live — the standard default for real investigation.

```
$ sudo iotop -o -b -n 1
```
A single non-interactive snapshot, useful inside a script or for a quick one-off check.

```
$ sudo iotop -p 8821
```
Monitor a specific process's I/O activity only.

## Expected Output

```
$ sudo iotop -o -b -n 1
Total DISK READ:         0.00 B/s | Total DISK WRITE:        14.80 M/s
  PID  USER     DISK READ  DISK WRITE  COMMAND
 8821  backup      0.00 B/s   14.60 M/s  tar czf /backup/data.tar.gz /data
```

## Exit Status

`0` on normal quit, non-zero on invalid options or insufficient privilege.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `iotop: Permission denied` | Requires elevated privilege to read per-process I/O accounting | Run with `sudo` |
| Empty output | No process is currently performing I/O, or `-o` is filtering out idle ones (expected) | Confirm with [`iostat`](iostat.md) that a device is actually active before expecting `iotop` output |
| Command not found | Not installed by default on all distros | Install via the distro's package manager (Module 14) |

## Security Considerations

Requires root/sudo to access per-process I/O accounting — treat access to `iotop` with the same
care as other privileged monitoring tools, since it reveals what data other users' processes are
reading/writing.

## Performance Considerations

`iotop` itself has minor overhead from continuously polling I/O accounting data — safe for
diagnostic use but not intended to run continuously as a background monitor (that's the role of a
[monitoring stack](../docs/20-monitoring/monitoring-stacks-overview.md) instead).

## Production Usage

`sudo iotop -o` is the standard second step the moment [`iostat`](iostat.md) confirms a device is
saturated — identifying the specific responsible process rather than just knowing the device is
busy.

## Related Commands

- [`iostat`](iostat.md) — confirms device-level saturation; run first, before `iotop`
- [`top`](top.md) — the CPU/memory equivalent per-process view

## Related Concepts

- [Disk I/O Performance: iostat, iotop](../docs/21-performance/disk-io-performance-iostat-iotop.md)
