---
title: "cp — Copy Files and Directories"
description: "Copy files and directories, including recursive copies and preserving permissions/timestamps."
relatedConcepts: ["06-files/creating-copying-moving-removing-files"]
relatedCommands: ["mv", "rm", "touch"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["cp command linux", "copy directory recursively linux", "cp preserve permissions"]
canonicalUrl: "/commands/cp"
---

# cp

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `cp source dest` copies a file. Add `-r` to copy a directory recursively. Add `-p` to
> preserve permissions and timestamps instead of getting fresh ones from the copy operation.

## Purpose

`cp` copies files or directories from a source path to a destination — the duplication half of
[Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md).

## Syntax

```
cp [OPTIONS] SOURCE DEST
cp [OPTIONS] SOURCE... DIRECTORY
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SOURCE` | File(s)/directory to copy | Yes |
| `DEST` / `DIRECTORY` | Destination path or directory | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-r`, `-R` | `--recursive` | Copy directories recursively |
| `-p` | `--preserve` | Preserve permissions, ownership, and timestamps |
| `-i` | `--interactive` | Prompt before overwriting an existing file |
| `-v` | `--verbose` | Print each file as it's copied |
| `-u` | `--update` | Only copy if source is newer than destination |

## Examples

```
$ cp config.yaml config.yaml.bak
```
Copy a single file.

```
$ cp -r project/ project-backup/
```
Copy a directory and everything inside it.

```
$ cp -p important.conf important.conf.bak
```
Copy while preserving the original's permissions and timestamps.

## Expected Output

`cp` produces no output by default; `-v` prints each file copied:

```
$ cp -v config.yaml config.yaml.bak
'config.yaml' -> 'config.yaml.bak'
```

## Exit Status

`0` on success, `1` if any copy fails (source missing, permission denied, etc.).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `cp: omitting directory 'dir'` | Tried to copy a directory without `-r` | Add `-r` |
| `cp: cannot create regular file 'dest': Permission denied` | No write permission at the destination | Check destination directory permissions |
| Copy silently overwrote an existing file | No `-i` flag used | Use `-i` when overwriting risk matters |

## Security Considerations

Copies inherit fresh permissions/ownership by default (not the source's) unless `-p` is used —
relevant when copying a sensitive file, since the copy's permissions might unintentionally be
more permissive than the original.

## Performance Considerations

Copying large directories (`-r`) can be slow and double disk usage temporarily; for very large
data migrations, purpose-built tools (`rsync`, covered in Module 13) are more efficient and
resumable.

## Production Usage

`cp -p` before editing a config file in place is a fast, low-ceremony backup habit — cheap
insurance before a risky manual edit, even when proper version control also exists.

## Related Commands

- [`mv`](mv.md) — move instead of copy
- [`rm`](rm.md), [`touch`](touch.md)

## Related Concepts

- [Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md)
