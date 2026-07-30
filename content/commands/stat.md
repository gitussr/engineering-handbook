---
title: "stat — Display File Status and Inode Metadata"
description: "Show full metadata for a file, including inode number, size, permissions, and timestamps, beyond what ls -l shows."
relatedConcepts: ["05-file-system/inodes-explained"]
relatedCommands: ["ls"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "good-to-know"
updatedAt: "2026-07-25"
keywords: ["stat command linux", "inode number command", "file metadata linux"]
canonicalUrl: "/commands/stat"
---

# stat

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `stat file` shows a file's full metadata — inode number, exact size, permissions,
> owner, and precise access/modify/change timestamps — more detail than `ls -l` provides.

## Purpose

`stat` displays detailed status information about a file, pulled directly from its inode — the
tool for inspecting the metadata covered conceptually in
[Inodes Explained](../docs/05-file-system/inodes-explained.md).

## Syntax

```
stat [OPTIONS] FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | One or more files to inspect | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-f` | `--file-system` | Show filesystem status instead of file status |
| `-c FORMAT` | `--format=FORMAT` | Output specific fields only, using a format string |

## Examples

```
$ stat app.log
```
Full metadata for a single file.

```
$ stat -c "%i %n" app.log
```
Print only the inode number and filename.

## Expected Output

```
$ stat app.log
  File: app.log
  Size: 1820            Blocks: 8          IO Block: 4096   regular file
Device: 802h/2050d       Inode: 4821        Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/deploy)   Gid: ( 1000/deploy)
Access: 2026-07-25 09:12:03
Modify: 2026-07-25 09:12:01
Change: 2026-07-25 09:12:01
```

## Exit Status

`0` on success, `1` if the file doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `stat: cannot stat 'file': No such file or directory` | Typo or wrong path | Verify with `ls` or tab completion |

## Security Considerations

`stat` output reveals ownership and precise timestamps — useful for legitimate forensic/audit
work (e.g. determining exactly when a file was last modified during an incident investigation).

## Performance Considerations

Negligible — reading inode metadata is fast regardless of file size.

## Production Usage

`stat`'s three timestamps (Access, Modify, Change) are frequently confused — "Change" refers to
metadata changes (permissions, ownership), not content changes, which is "Modify." Getting this
distinction right matters during incident timelines where "when was this file's permission
changed" and "when was its content last changed" are different, both useful, questions.

## Related Commands

- [`ls`](ls.md) — `ls -l` shows a lighter summary of some of the same information

## Related Concepts

- [Inodes Explained](../docs/05-file-system/inodes-explained.md)
