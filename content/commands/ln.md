---
title: "ln — Create Links Between Files"
description: "Create hard links and symbolic links, including the classic mistakes with relative symlink targets."
relatedConcepts: ["05-file-system/symbolic-vs-hard-links"]
relatedCommands: ["ls"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["ln command linux", "ln -s symlink", "create hard link linux"]
canonicalUrl: "/commands/ln"
---

# ln

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `ln target linkname` creates a hard link. `ln -s target linkname` creates a symbolic
> link — the one you'll use far more often. Get target and linkname order right: target first,
> new link name second.

## Purpose

`ln` creates links between files — either hard links (another name for the same inode) or
symbolic links (a separate file pointing at a path) — the command behind
[Symbolic vs Hard Links](../docs/05-file-system/symbolic-vs-hard-links.md).

## Syntax

```
ln [OPTIONS] TARGET LINKNAME
ln -s [OPTIONS] TARGET LINKNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `TARGET` | The existing file (or path, for symlinks) being linked to | Yes |
| `LINKNAME` | The new link's name/path | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-s` | `--symbolic` | Create a symbolic link instead of a hard link |
| `-f` | `--force` | Overwrite an existing file at `LINKNAME` |
| `-v` | `--verbose` | Print each link created |

## Examples

```
$ ln -s /opt/app/releases/v2.4.1 /opt/app/current
```
Create a symbolic link — the far more common use case.

```
$ ln app.log app.log.hardlink
```
Create a hard link (no `-s`) — same filesystem only.

```
$ ln -sf /opt/app/releases/v2.4.2 /opt/app/current
```
Force-repoint an existing symlink to a new target — the standard deployment "switch to new
release" pattern.

## Expected Output

```
$ ls -l /opt/app/current
lrwxrwxrwx 1 deploy deploy 30 Jul 25 10:00 current -> /opt/app/releases/v2.4.1
```

The `l` at the start of the permission string and the `->` notation both indicate a symbolic
link, visible directly in `ls -l` output.

## Exit Status

`0` on success, non-zero if the target doesn't exist (for hard links), the link name already
exists without `-f`, or a hard link is attempted across filesystems or on a directory.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `ln: failed to create hard link: Invalid cross-device link` | Hard links can't cross filesystems | Use a symbolic link (`-s`) instead |
| `ln: LINKNAME: File exists` | The link name already exists | Use `-f` to overwrite, or choose a different name |
| Symlink shows as broken after a move | The target was moved/renamed after the symlink was created | Recreate the symlink pointing at the new location |

## Security Considerations

A symlink pointing into a location an attacker controls can be used in symlink attacks
(tricking a privileged process into writing through the symlink to an unintended target) —
relevant when creating symlinks in shared/world-writable directories.

## Performance Considerations

Negligible for either link type — both are lightweight filesystem operations.

## Production Usage

The `ln -sf target current` pattern (force-repointing a symlink) is the standard mechanism behind
many deployment tools' "release switch" step — instant, atomic-enough for practical purposes, and
trivially reversible by repointing again.

## Related Commands

- [`ls`](ls.md) — `ls -l` reveals symlinks via their `->` notation

## Related Concepts

- [Symbolic vs Hard Links](../docs/05-file-system/symbolic-vs-hard-links.md)
- [Inodes Explained](../docs/05-file-system/inodes-explained.md)
