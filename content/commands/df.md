---
title: "df — Report Filesystem Disk Space Usage"
description: "Show how full each mounted filesystem is — the first command to run when disk full is suspected."
relatedConcepts: ["15-storage/disk-usage-df-du"]
relatedCommands: ["du"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["df -h examples", "df command linux", "check disk space linux", "df inodes"]
canonicalUrl: "/commands/df"
---

# df

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `df -h` shows how full every mounted filesystem is, in human-readable sizes — the
> first command to run for any "disk full" investigation.

## Purpose

`df` reports disk space usage per mounted filesystem — see
[Disk Usage: df, du](../docs/15-storage/disk-usage-df-du.md) for how it complements `du`.

## Syntax

```
df [OPTIONS] [PATH]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATH` | Limit output to the filesystem containing this path | No — defaults to every mounted filesystem |

## Options

| Flag | Meaning |
|---|---|
| `-h` | Human-readable sizes (K/M/G instead of raw blocks) |
| `-i` | Show inode usage instead of block/space usage |
| `-T` | Include filesystem type in the output |

## Examples

```
$ df -h
```
Show space usage for every mounted filesystem, human-readable.

```
$ df -h /var
```
Show usage for just the filesystem containing `/var`.

```
$ df -i
```
Show inode usage — relevant when "no space left" occurs despite available disk space (all
inodes exhausted, Module 05).

## Expected Output

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        39G   37G  0.5G  98% /
/dev/sdb1        50G   1.2G   46G   3% /data
```

## Exit Status

`0` on success, non-zero if a specified path doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `No such file or directory` | Specified path doesn't exist | Verify the path |
| "No space left on device" despite `df` showing free space | Inodes exhausted, not blocks | Run `df -i` to check inode usage specifically |
| Output hard to read | Raw block counts instead of human-readable sizes | Add `-h` |

## Security Considerations

Read-only — `df` makes no changes, making it always safe to run.

## Performance Considerations

Negligible — reads already-available kernel filesystem statistics.

## Production Usage

`df -h` is the universal first command in any "disk full" investigation — quick, safe, and
immediately shows which filesystem is actually the problem before digging further with
[`du`](du.md).

## Related Commands

- [`du`](du.md) — find *what* is using the space `df` reports as consumed

## Related Concepts

- [Disk Usage: df, du](../docs/15-storage/disk-usage-df-du.md)
