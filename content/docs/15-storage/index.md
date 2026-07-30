---
title: "Storage"
description: "Module 15 of the Linux roadmap — disks and partitions, viewing and creating them, filesystem creation, persistent mounting via /etc/fstab, LVM, RAID, disk usage, and swap space."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
type: "module-index"
nextTopic: "15-storage/disks-and-partitions-overview"
updatedAt: "2026-07-27"
canonicalUrl: "/docs/storage"
---

# Storage

Module 15 of 34 · Stage: Intermediate · Previous: [14 Package Managers](../14-package-managers/index.md)

[Module 05](../05-file-system/mounting-and-unmounting.md) introduced mounting at a concept level
and explicitly deferred full `/etc/fstab` syntax to this module. This module delivers that, plus
everything upstream of it: partitioning a raw disk, creating a filesystem on it, and the two
advanced storage layers (LVM, RAID) production environments commonly build on top of a plain
partition.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Disks and Partitions Overview](disks-and-partitions-overview.md) | 🟢 Must Know |
| [fdisk, parted, lsblk](fdisk-parted-lsblk.md) | 🟢 Must Know |
| [Creating Filesystems: mkfs](creating-filesystems-mkfs.md) | 🟢 Must Know |
| [Mounting and /etc/fstab](mounting-and-etc-fstab.md) | 🟢 Must Know |
| [LVM (Logical Volume Management)](lvm-logical-volume-management.md) | 🔴 Expert |
| [RAID Basics](raid-basics.md) | 🔴 Expert |
| [Disk Usage: df, du](disk-usage-df-du.md) | 🟢 Must Know |
| [Swap Space](swap-space.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain what a partition is and the difference between GPT and MBR.
- View block devices and partitions with `lsblk`, and create/modify partitions with `fdisk` or
  `parted`.
- Format a partition with a filesystem using `mkfs`.
- Write a correct `/etc/fstab` entry using a UUID, and test it safely with `mount -a`.
- Explain what LVM adds over a raw partition, and walk through extending a logical volume.
- Explain the tradeoffs between common RAID levels, and why RAID is not a substitute for backups.
- Diagnose a "disk full" issue using `df` and `du` together.
- Explain what swap space protects against, and why heavy sustained swap usage is a performance
  warning sign, not a capacity solution.

## Known, intentional gaps in this module

- Canonical command pages exist for `fdisk`, `parted`, `lsblk`, `mkfs`, `df`, and `du` — every
  command explicitly named in this module's roadmap entry.
- LVM commands (`pvcreate`, `vgcreate`, `lvcreate`, `lvextend`, `resize2fs`, and others), RAID
  tooling (`mdadm`), and the remaining swap commands (`mkswap`, `swapon`, `swapoff`) are covered
  in full within their respective topic pages but don't get separate canonical command pages — the
  roadmap scopes LVM, RAID, and swap as concepts, not as named-command bullets the way `fdisk`,
  `parted`, `lsblk`, `mkfs`, `df`, and `du` explicitly were. `free`, also introduced on this
  module's [Swap Space](swap-space.md) page, later got its own canonical
  [command page](../../commands/free.md) in Module 21: Performance, once a roadmap bullet there
  finally named it explicitly in backticks.
- `xfs_growfs`, `blkid`, and `lsof` are referenced ahead of their own full coverage.

**Previous module:** [14 Package Managers](../14-package-managers/index.md)
**Next module:** [16 Logs →](../16-logs/index.md)
