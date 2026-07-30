---
title: "umount — Detach a Filesystem"
description: "Detach a mounted filesystem, and what to do when it fails with 'device is busy'."
relatedConcepts: ["05-file-system/mounting-and-unmounting"]
relatedCommands: ["mount"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["umount command linux", "device is busy unmount", "umount force"]
canonicalUrl: "/commands/umount"
---

# umount

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `umount mountpoint` detaches a filesystem. "Device is busy" means something still
> has an open file or working directory there — find and release it before forcing anything.

## Purpose

`umount` detaches a previously mounted filesystem from its mount point — the counterpart to
[`mount`](mount.md), covered together in
[Mounting and Unmounting](../docs/05-file-system/mounting-and-unmounting.md).

## Syntax

```
umount [OPTIONS] MOUNTPOINT
umount [OPTIONS] DEVICE
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `MOUNTPOINT` or `DEVICE` | The mounted directory, or the device, to unmount | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-f` | `--force` | Force unmount (use with caution — can cause data loss for network filesystems) |
| `-l` | `--lazy` | Detach immediately, cleaning up references once they're no longer in use |

## Examples

```
$ umount /mnt/data
```
Standard unmount.

```
$ umount -l /mnt/data
```
Lazy unmount — detaches from the namespace immediately, actual cleanup happens once nothing is
using it anymore.

## Expected Output

Success produces no output. Failure:

```
$ umount /mnt/data
umount: /mnt/data: target is busy.
```

## Exit Status

`0` on success, non-zero (commonly `32`) if the target is busy or doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `target is busy` | A process has an open file or working directory on the mount | Find the process (`lsof`/`fuser`, covered later) and close it, or use `-l` for a lazy unmount |
| `not mounted` | The target isn't currently mounted at all | Verify with `mount` or `df -h` first |

## Security Considerations

`-f` (force) can cause data loss or corruption, especially for network filesystems with pending
writes — never the default choice; understand why something is busy before forcing.

## Performance Considerations

Not applicable — `umount` itself has negligible cost; the concern is correctness (data integrity)
around forcing an unmount, not performance.

## Production Usage

A busy mount point during a planned maintenance window is a common, recurring situation —
experienced engineers check what's holding it open (a lingering SSH session, a running process)
before reaching for `-f`, since force-unmounting a filesystem with unflushed writes can lose data.

## Related Commands

- [`mount`](mount.md) — attach a filesystem

## Related Concepts

- [Mounting and Unmounting](../docs/05-file-system/mounting-and-unmounting.md)
