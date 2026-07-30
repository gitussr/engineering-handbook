---
title: "mkfs — Create a Filesystem on a Partition"
description: "Format a partition or block device with a filesystem (ext4, xfs, etc.) — a destructive, one-way operation."
relatedConcepts: ["15-storage/creating-filesystems-mkfs"]
relatedCommands: ["lsblk", "fdisk"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["mkfs.ext4 examples", "mkfs -t explained", "format partition linux", "mkfs.xfs command"]
canonicalUrl: "/commands/mkfs"
---

# mkfs

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `mkfs -t ext4 /dev/sdb1` formats a partition with ext4. Destructive and irreversible
> — always re-verify the target device with `lsblk` immediately before running it.

## Purpose

`mkfs` formats a partition or block device with a filesystem — see
[Creating Filesystems: mkfs](../docs/15-storage/creating-filesystems-mkfs.md) for the full
concept.

## Syntax

```
mkfs -t TYPE DEVICE
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `DEVICE` | The partition or block device to format (e.g. `/dev/sdb1`) | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-t TYPE` | Filesystem type (`ext4`, `xfs`, etc.) |
| `-L LABEL` | Set a volume label |

Type-specific shorthand forms also exist: `mkfs.ext4 DEVICE` and `mkfs.xfs DEVICE` are equivalent
to `mkfs -t ext4 DEVICE` and `mkfs -t xfs DEVICE` respectively.

## Examples

```
$ sudo mkfs -t ext4 /dev/sdb1
```
Format a partition with ext4.

```
$ sudo mkfs.xfs /dev/sdb1
```
Format a partition with xfs, using the shorthand form.

```
$ sudo mkfs -t ext4 -L data-volume /dev/sdb1
```
Format with a specific volume label, useful for identifying the filesystem later.

## Expected Output

```
$ sudo mkfs -t ext4 /dev/sdb1
mke2fs 1.47.0
Creating filesystem with 13107200 4k blocks and 3276800 inodes
Filesystem UUID: 1a2b3c4d-5e6f-7890-abcd-ef1234567890
Writing inode tables: done
Writing superblocks and filesystem accounting information: done
```

The output includes the new filesystem's UUID — useful for the `/etc/fstab` entry
([Module 15's mounting topic](../docs/15-storage/mounting-and-etc-fstab.md)) immediately after.

## Exit Status

`0` on success, non-zero if the device doesn't exist, is mounted, or the operation fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `mke2fs: /dev/sdX is mounted` | Attempting to format a currently mounted filesystem | Unmount it first |
| Wrong device formatted | Typo or stale assumption about device naming | Always re-verify with `lsblk` immediately before running `mkfs` — no undo once run |
| `mkfs.TYPE: command not found` | Filesystem-specific tools (e.g. xfs utilities) not installed | Install the relevant package (`xfsprogs` for xfs, etc.) |

## Security Considerations

`mkfs` destroys the existing filesystem structure (and data) on the target device immediately and
irreversibly — treat the target device argument with the same caution as any other destructive,
unrecoverable command.

## Performance Considerations

Formatting a very large device can take noticeably longer, particularly for filesystem types that
pre-allocate more metadata structures up front.

## Production Usage

Immediately re-confirming the target device with `lsblk` right before running `mkfs` — not
relying on a check done minutes earlier — is standard practice precisely because this command has
no undo.

## Related Commands

- [`lsblk`](lsblk.md) — verify the target device before and after formatting
- [`fdisk`](fdisk.md) / [`parted`](parted.md) — create the partition `mkfs` formats

## Related Concepts

- [Creating Filesystems: mkfs](../docs/15-storage/creating-filesystems-mkfs.md)
