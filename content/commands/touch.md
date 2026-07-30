---
title: "touch — Create a File or Update Its Timestamp"
description: "Create an empty file, or update an existing file's access and modification timestamps without changing its content."
relatedConcepts: ["06-files/creating-copying-moving-removing-files"]
relatedCommands: ["cp", "mv", "rm"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["touch command linux", "create empty file linux", "update file timestamp"]
canonicalUrl: "/commands/touch"
---

# touch

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `touch file` creates an empty file if it doesn't exist, or updates its timestamp to
> now if it does — content is never modified.

## Purpose

`touch` creates an empty file, or updates an existing file's access/modification timestamps to
the current time without touching its content — one of the four core operations in
[Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md).

## Syntax

```
touch [OPTIONS] FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | One or more files to create or update | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-c` | `--no-create` | Don't create the file if it doesn't already exist |
| `-t STAMP` | | Set a specific timestamp instead of the current time |
| `-a` | | Update only the access time |
| `-m` | | Update only the modification time |

## Examples

```
$ touch newfile.txt
```
Create an empty file.

```
$ touch existingfile.txt
```
Update its timestamp to now, content unchanged.

```
$ touch -c maybe-exists.txt
```
Update the timestamp only if the file already exists — don't create it.

## Expected Output

`touch` produces no output on success.

## Exit Status

`0` on success, non-zero if the path is invalid or permissions don't allow it.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `touch: cannot touch 'file': Permission denied` | No write permission on the containing directory | Check directory permissions (Module 07) |
| `touch: cannot touch 'file': No such file or directory` | The containing directory doesn't exist | Create the parent directory first |

## Security Considerations

None specific — `touch` doesn't alter file content or permissions.

## Performance Considerations

Negligible.

## Production Usage

`touch` is commonly used in scripts to create marker/lock files (e.g. `touch
/tmp/deploy.lock`) that signal a process is running, checked and removed by the script itself —
a lightweight coordination mechanism before reaching for something more robust.

## Related Commands

- [`cp`](cp.md), [`mv`](mv.md), [`rm`](rm.md) — the other core file operations

## Related Concepts

- [Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md)
