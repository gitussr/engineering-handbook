---
title: "du — Estimate File and Directory Space Usage"
description: "Show how much space a specific file or directory tree actually uses — the tool for finding what's consuming the space df reported as full."
relatedConcepts: ["15-storage/disk-usage-df-du"]
relatedCommands: ["df"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["du -sh examples", "du max-depth", "find large directories linux", "du command linux"]
canonicalUrl: "/commands/du"
---

# du

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `du -sh PATH` shows the total size of a directory tree, summarized. `du -h
> --max-depth=1 PATH` breaks usage down one directory level at a time — the standard way to find
> what's consuming space.

## Purpose

`du` reports how much disk space a specific file or directory tree consumes — see
[Disk Usage: df, du](../docs/15-storage/disk-usage-df-du.md) for how it complements `df`.

## Syntax

```
du [OPTIONS] [PATH]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATH` | The file or directory to measure | No — defaults to the current directory |

## Options

| Flag | Meaning |
|---|---|
| `-s` | Summarize — total only, not every subdirectory individually |
| `-h` | Human-readable sizes |
| `--max-depth=N` | Show totals only down to N directory levels deep |

## Examples

```
$ du -sh /var/log
```
Show the total size of `/var/log`, summarized.

```
$ du -h --max-depth=1 /var/log
```
Break down `/var/log`'s usage one subdirectory level at a time.

```
$ du -sh /var/log/* | sort -rh | head
```
List the largest items directly under `/var/log`, sorted biggest first (combining
[Module 06's](../docs/06-files/cut-sort-uniq-wc.md) `sort`).

## Expected Output

```
$ du -h --max-depth=1 /var/log
2.1G    /var/log/myapp
120M    /var/log/nginx
2.3G    /var/log
```

## Exit Status

`0` on success, non-zero if the path doesn't exist. Permission-denied subdirectories produce
warnings on stderr but don't necessarily stop the overall command.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Command runs for a very long time | Unscoped `du` across a huge directory tree | Use `--max-depth` to narrow scope instead of scanning everything at once |
| `Permission denied` warnings mixed into output | No read access to some subdirectories | Expected for non-root users on some system paths; re-run with `sudo` if you need those included |
| Total doesn't match `df`'s reported used space | An open-but-deleted file handle `du` can't see | Cross-reference with `lsof` for a process still holding a deleted file open |

## Security Considerations

Read-only — `du` makes no changes, making it always safe to run, though it can be slow on very
large trees.

## Performance Considerations

An unscoped `du -h` across a very large filesystem can take a long time; `--max-depth=1`, applied
repeatedly at deeper levels as needed, is significantly faster than scanning everything at once.

## Production Usage

`du -h --max-depth=1`, run repeatedly one level deeper each time, is the standard technique for
narrowing down exactly which directory is responsible for unexpectedly high disk usage.

## Related Commands

- [`df`](df.md) — confirm and quantify the overall problem before locating the cause with `du`

## Related Concepts

- [Disk Usage: df, du](../docs/15-storage/disk-usage-df-du.md)
