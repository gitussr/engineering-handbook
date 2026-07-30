---
title: "fdisk, parted, lsblk"
description: "Viewing block devices and their partitions, and creating or modifying a partition table with either an MBR-focused or GPT-capable tool."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/disks-and-partitions-overview"]
relatedTopics: ["creating-filesystems-mkfs"]
relatedCommands: ["fdisk", "parted", "lsblk"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#fdisk-vs-parted"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/creating-filesystems-mkfs"
prevTopic: "15-storage/disks-and-partitions-overview"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["lsblk examples", "fdisk create partition", "parted gpt partition", "list block devices linux"]
canonicalUrl: "/docs/storage/fdisk-parted-lsblk"
---

# fdisk, parted, lsblk

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `lsblk` shows every block device and its partitions at a glance — always run this
> first. `fdisk` creates/modifies partitions interactively (MBR-focused, though modern versions
> support GPT). `parted` is GPT-native and also scriptable non-interactively.

## What is it?

The first hands-on tools in this module: `lsblk` for viewing block devices and partitions, and
`fdisk`/`parted` for creating or modifying a partition table — the practical, command-line
application of [Disks and Partitions Overview](disks-and-partitions-overview.md).

## Why does it exist?

Before creating or modifying any partition, you need to see the current disk layout accurately —
`lsblk` provides that view. `fdisk` and `parted` then exist to actually create, delete, or resize
partitions; both do the same fundamental job with different interaction models and historical
partition-table-format focus.

## Where is it used?

Confirming what storage is actually attached to a system and how it's currently partitioned
before making any change, and creating new partitions on a freshly attached disk (a new cloud
volume, an added physical disk) before it can be formatted.

## How it works

> 📊 Diagram: `lsblk` output shown as a tree — a disk (`sda`) with two partition children
> (`sda1`, `sda2`), each annotated with size and mount point — next to `fdisk`/`parted` shown as
> the tools that actually create the entries `lsblk` later displays.

| Tool | Role |
|---|---|
| `lsblk` | Read-only — list block devices and partitions in a tree view |
| `fdisk` | Interactive partition editor, historically MBR-focused (modern versions support GPT too) |
| `parted` | GPT-native partition editor, supports both interactive and scriptable non-interactive use |

**Always `lsblk` first**: before creating or modifying any partition, confirm exactly which device
you're about to act on — partitioning the wrong disk is a genuinely destructive, hard-to-reverse
mistake.

## Real-world example

A new cloud volume is attached to a server as `/dev/xvdf`. `lsblk` confirms it appears with no
partitions yet (an empty disk, correctly distinguished from the server's existing root disk).
`parted /dev/xvdf` is then used to create a single GPT partition spanning the whole disk, followed
by [`mkfs`](creating-filesystems-mkfs.md) (this module's next topic) to format it. Checking with
`lsblk` first is what prevented accidentally targeting the wrong device.

## Commands

- [`lsblk`](../../commands/lsblk.md) — full syntax and examples
- [`fdisk`](../../commands/fdisk.md) — full syntax and examples
- [`parted`](../../commands/parted.md) — full syntax and examples

## Production example

```
$ lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda       8:0    0   40G  0 disk
├─sda1    8:1    0    1G  0 part /boot
└─sda2    8:2    0   39G  0 part /
sdb       8:16   0   50G  0 disk

$ sudo parted /dev/sdb mklabel gpt
$ sudo parted /dev/sdb mkpart primary ext4 0% 100%
```

`sdb` shows with no partition children yet — exactly the "new, empty disk" state confirmed before
partitioning it.

## Do / Don't

| Do | Don't |
|---|---|
| Run `lsblk` before any partitioning operation to confirm the target device | Assume you know the correct device name without checking |
| Use `parted` for GPT or when scripting non-interactively | Use `fdisk` on a very large disk expecting full, seamless GPT support in every version |
| Double-check size and device before confirming a destructive change | Rush through an interactive `fdisk`/`parted` session on a production disk |

## Common mistakes

- Skipping `lsblk` and partitioning the wrong device from memory or assumption — genuinely
  destructive if it's a disk already in use.
- Assuming every `fdisk` version handles GPT identically to `parted` — behavior and feature
  completeness vary by version.
- Rushing through an interactive partitioning session on a production disk without double-checking
  each prompt.

## Best practices

- Always run `lsblk` immediately before any partitioning operation to confirm the target device.
- Use `parted` for GPT disks and for any partitioning step that needs to be scripted
  non-interactively.
- Treat any partitioning operation on a disk with existing data as high-risk — verify twice before
  confirming.

## Exercises

1. Run `lsblk` on a system you have access to and identify each disk and its partitions.
2. Explain in one sentence why `lsblk` should always be run before partitioning.
3. Describe the practical difference between `fdisk` and `parted`.

## Quiz

**Q: Why should `lsblk` always be run before creating or modifying a partition?**
<details><summary>Show answer</summary>
To confirm exactly which device you're about to act on — partitioning the wrong disk is
destructive and hard to reverse.
</details>

**Q: What's the key practical difference between `fdisk` and `parted`?**
<details><summary>Show answer</summary>
`fdisk` is historically MBR-focused and interactive (modern versions support GPT too); `parted`
is GPT-native and also supports scriptable, non-interactive use.
</details>

## Interview questions

- Why would you run `lsblk` before partitioning a disk with `fdisk` or `parted`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `lsblk` is the read-only view of block devices and partitions — always check it first.
- `fdisk` and `parted` both create/modify partitions; `parted` is GPT-native and scriptable.
- Partitioning the wrong device is a destructive, hard-to-reverse mistake — verify before acting.
- Partitioning must happen before formatting ([`mkfs`](creating-filesystems-mkfs.md), next topic).

## Related topics

- [Creating Filesystems: mkfs](creating-filesystems-mkfs.md)
- [Disks and Partitions Overview](disks-and-partitions-overview.md)
