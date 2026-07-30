---
title: "File System"
description: "Module 05 of the Linux roadmap — the full Filesystem Hierarchy Standard, mounting, filesystem types, inodes, and links. Closes the FHS forward references opened in Modules 01 and 03."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
type: "module-index"
nextTopic: "05-file-system/fhs-deep-dive"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/file-system"
---

# File System

Module 05 of 34 · Stage: Beginner · Previous: [04 Bash](../04-bash/index.md)

Modules 01 and 03 both previewed the Filesystem Hierarchy Standard and pointed here for full
depth. This module delivers that depth — every standard directory's actual purpose, how mounting
works, what a filesystem type even is, and the inode/link mechanics underneath everyday commands
like `cp` and `ln`.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [FHS Deep Dive](fhs-deep-dive.md) | 🟢 Must Know |
| [Core Directories Explained](core-directories-explained.md) | 🟢 Must Know |
| [Filesystem Types: ext4, xfs, btrfs, ntfs](filesystem-types.md) | 🟡 Good to Know |
| [Mounting and Unmounting](mounting-and-unmounting.md) | 🟢 Must Know |
| [/proc and /sys Virtual Filesystems](proc-and-sys-virtual-filesystems.md) | 🔴 Expert |
| [Inodes Explained](inodes-explained.md) | 🟡 Good to Know |
| [Symbolic vs Hard Links](symbolic-vs-hard-links.md) | 🟢 Must Know |

## What you should be able to do after this module

- Explain what the FHS is and why it's versioned and standardized, not just convention.
- Know what belongs in `/etc`, `/var`, `/home`, `/usr`, `/bin`, `/opt`, `/tmp` without looking it up.
- Compare ext4, xfs, and btrfs and explain when each is the right choice.
- Mount and unmount a filesystem, and explain what `/etc/fstab` is for.
- Explain what `/proc` and `/sys` are and pull real information out of them.
- Explain what an inode is and why "no space left on device" can happen with free disk space.
- Correctly choose between a symbolic link and a hard link for a given situation.

## Known, intentional gaps in this module

- Canonical command pages exist for `mount`, `umount`, `ln`, and `stat` — the four commands this
  module teaches in the most depth. `df` and `du` are referenced but deliberately **not** given
  full pages here — they belong to [Module 15: Storage](../15-storage/index.md), which owns disk
  usage reporting in depth.
- `mkfs` and `/etc/fstab` syntax are previewed but not taught in full here — full coverage is in
  [Module 15: Storage](../15-storage/index.md), which owns disk/partition management.
- Kernel parameter tuning via `/proc`/`sysctl` is previewed in
  [/proc and /sys Virtual Filesystems](proc-and-sys-virtual-filesystems.md) but owned in full by
  [Module 21: Performance](../21-performance/index.md).

**Previous module:** [04 Bash](../04-bash/index.md)
**Next module:** [06 Files →](../06-files/index.md)
