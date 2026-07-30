---
title: "find — Search for Files by Criteria"
description: "Search the live filesystem by name, type, size, or modification time, and optionally act on the results."
relatedConcepts: ["06-files/finding-files"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["find command linux", "find by name", "find by size", "find -exec"]
canonicalUrl: "/commands/find"
---

# find

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `find PATH -name pattern` is the basic form. Add `-type`, `-size`, or `-mtime` to
> filter by criteria, and `-exec` to run a command on every match — a powerful combination for
> targeted cleanup and investigation.

## Purpose

`find` searches the live filesystem starting from a given path, matching files and directories
against flexible criteria — name, type, size, modification time, permissions — the command behind
[Finding Files](../docs/06-files/finding-files.md).

## Syntax

```
find PATH [EXPRESSION]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATH` | Directory to start searching from | Yes |
| `EXPRESSION` | Criteria and actions (see Options) | No (defaults to listing everything) |

## Options

| Flag | Meaning |
|---|---|
| `-name PATTERN` | Match by filename (case-sensitive; `-iname` for case-insensitive) |
| `-type f\|d\|l` | Match by type: file, directory, symlink |
| `-size +N\|-N` | Larger than / smaller than N (e.g. `+100M`) |
| `-mtime +N\|-N` | Modified more than / less than N days ago |
| `-exec CMD {} \;` | Run CMD on each match |
| `-maxdepth N` | Limit how many directory levels deep to search |

## Examples

```
$ find /var/log -name "*.log"
```
Find files matching a name pattern.

```
$ find / -type f -size +500M
```
Find files over 500MB anywhere on the system.

```
$ find /tmp -mtime +7 -exec rm {} \;
```
Find files older than 7 days in `/tmp` and delete each one — a common cleanup pattern.

## Expected Output

```
$ find /var/log -name "*.log"
/var/log/nginx/access.log
/var/log/nginx/error.log
```

## Exit Status

`0` on success (even with zero matches), non-zero if a path is invalid or unreadable.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `find: 'path': No such file or directory` | The starting path doesn't exist | Verify the path first |
| `find: 'path': Permission denied` (partial results) | No read access to some subdirectories | Run with appropriate privileges, or ignore (find continues past inaccessible dirs) |
| `-exec` seems to run once per file instead of batched | Default `-exec ... \;` behavior — one invocation per match | Use `-exec CMD {} +` to batch matches into fewer invocations |

## Security Considerations

`-exec ... -delete` or `-exec rm {} \;` run carelessly (especially as root, especially from `/`)
can be as dangerous as a mistyped `rm -rf` — always run the `find` command without `-exec` first
to review matches before adding a destructive action.

## Performance Considerations

Searching from `/` across an entire filesystem is meaningfully slower than scoping to a known
subdirectory — use `-maxdepth` or a narrower starting path when possible.

## Production Usage

`find /tmp -mtime +7 -exec rm {} \;`-style cleanup jobs are a common cron-scheduled pattern
(full cron syntax in Module 17) for keeping temporary/scratch directories from growing
unbounded.

## Related Commands

None documented yet — `locate`, `which`, `whereis` are covered in
[Finding Files](../docs/06-files/finding-files.md) but don't have separate canonical pages yet.

## Related Concepts

- [Finding Files](../docs/06-files/finding-files.md)
