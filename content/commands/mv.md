---
title: "mv — Move or Rename Files"
description: "Move files and directories, or rename them — the same command handles both."
relatedConcepts: ["06-files/creating-copying-moving-removing-files"]
relatedCommands: ["cp", "rm", "touch"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["mv command linux", "rename file linux", "move directory linux"]
canonicalUrl: "/commands/mv"
---

# mv

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `mv source dest` moves a file or directory — and renaming is just moving to a new
> name in the same directory, the same command either way.

## Purpose

`mv` moves or renames files and directories — the relocation half of
[Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md).

## Syntax

```
mv [OPTIONS] SOURCE DEST
mv [OPTIONS] SOURCE... DIRECTORY
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SOURCE` | File(s)/directory to move | Yes |
| `DEST` / `DIRECTORY` | New path, new name, or target directory | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-i` | `--interactive` | Prompt before overwriting an existing file |
| `-n` | `--no-clobber` | Never overwrite an existing destination |
| `-v` | `--verbose` | Print each move |

## Examples

```
$ mv old-name.txt new-name.txt
```
Rename a file (same directory).

```
$ mv app.tar.gz /opt/releases/
```
Move a file into a different directory.

```
$ mv -n build.log build.log
```
Move, but never overwrite an existing destination.

## Expected Output

`mv` produces no output by default; `-v` prints each move:

```
$ mv -v old.txt new.txt
'old.txt' -> 'new.txt'
```

## Exit Status

`0` on success, `1` if the move fails (permission denied, source missing).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `mv: cannot move 'x' to 'y': Permission denied` | No write permission at source or destination directory | Check permissions on both directories |
| Existing destination file silently overwritten | No `-i`/`-n` flag used | Use `-i` or `-n` when overwrite risk matters |
| `mv` across filesystems is slower than expected | `mv` across filesystems falls back to copy-then-delete internally | Expected behavior — not a bug; within one filesystem it's instant |

## Security Considerations

Same consideration as `cp` regarding permissions — a moved file typically keeps its original
permissions (unlike a fresh copy), which is usually what's intended but worth verifying when
security-sensitive files are involved.

## Performance Considerations

Within the same filesystem, `mv` is effectively instant regardless of file size (it's a metadata
change, not a data copy). Across filesystems, it's exactly as slow as a copy-then-delete, since
that's what actually happens internally.

## Production Usage

The atomic-within-a-filesystem nature of `mv` is exactly what makes the
`ln -sf`/`mv`-based "swap into place" deployment pattern safe — moving a fully-prepared new
version into its final location happens instantly, with no window where a partially-written file
is visible to other processes.

## Related Commands

- [`cp`](cp.md) — copy instead of move
- [`rm`](rm.md), [`touch`](touch.md)

## Related Concepts

- [Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md)
