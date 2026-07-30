---
title: "lsblk — List Block Devices"
description: "Show every disk and partition on the system in a tree view, with size and mount point — the read-only check to run before any partitioning operation."
relatedConcepts: ["15-storage/fdisk-parted-lsblk", "15-storage/disks-and-partitions-overview"]
relatedCommands: ["fdisk", "parted"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["lsblk examples", "lsblk -f uuid", "list disks and partitions linux", "lsblk tree view"]
canonicalUrl: "/commands/lsblk"
---

# lsblk

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `lsblk` lists every block device and partition in a tree view with size and mount
> point. `lsblk -f` adds filesystem type and UUID. Always run this before partitioning or
> formatting anything.

## Purpose

`lsblk` lists block devices and their partitions — see
[fdisk, parted, lsblk](../docs/15-storage/fdisk-parted-lsblk.md) for the full context.

## Syntax

```
lsblk [OPTIONS] [DEVICE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `DEVICE` | A specific device to limit output to | No — defaults to every block device |

## Options

| Flag | Meaning |
|---|---|
| `-f` | Include filesystem type and UUID |
| `-a` | Include empty devices, normally hidden |
| `-o COLUMNS` | Choose specific output columns |
| `-p` | Show full device paths (`/dev/sda1` instead of `sda1`) |

## Examples

```
$ lsblk
```
List all block devices and partitions in a tree view.

```
$ lsblk -f
```
Include filesystem type and UUID — needed before writing an `/etc/fstab` entry.

```
$ lsblk /dev/sdb
```
Limit output to one specific device.

## Expected Output

```
$ lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda       8:0    0   40G  0 disk
├─sda1    8:1    0    1G  0 part /boot
└─sda2    8:2    0   39G  0 part /
sdb       8:16   0   50G  0 disk
```

## Exit Status

`0` on success, non-zero if a specified device doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `not a block device` | Specified path isn't a valid block device | Verify the device name |
| Expected disk missing from output | Device not yet recognized by the kernel | Check `dmesg` for hardware detection, or that a cloud volume was actually attached |

## Security Considerations

Read-only — `lsblk` makes no changes, making it always safe to run.

## Performance Considerations

Negligible — reads already-available kernel device information.

## Production Usage

`lsblk -f` immediately before writing an `/etc/fstab` entry is the standard way to get the correct
UUID without risking a typo from copying it elsewhere.

## Related Commands

- [`fdisk`](fdisk.md) / [`parted`](parted.md) — the tools that create what `lsblk` displays

## Related Concepts

- [fdisk, parted, lsblk](../docs/15-storage/fdisk-parted-lsblk.md)
- [Disks and Partitions Overview](../docs/15-storage/disks-and-partitions-overview.md)
