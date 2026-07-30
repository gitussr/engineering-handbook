---
title: "Creating Filesystems: mkfs"
description: "Formatting a partition with a filesystem — the step between having a partition and being able to mount and use it."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/fdisk-parted-lsblk"]
relatedTopics: ["mounting-and-etc-fstab"]
relatedCommands: ["mkfs"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#mkfs-explained"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/mounting-and-etc-fstab"
prevTopic: "15-storage/fdisk-parted-lsblk"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["mkfs.ext4 examples", "mkfs command explained", "format a partition linux", "mkfs.xfs"]
canonicalUrl: "/docs/storage/creating-filesystems-mkfs"
---

# Creating Filesystems: mkfs

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `mkfs -t ext4 /dev/sdb1` formats a partition with an ext4 filesystem — the step
> between having a raw partition and having something mountable. `mkfs` is destructive: it erases
> whatever was on that partition before.

## What is it?

`mkfs` ("make filesystem") writes a filesystem structure onto a partition or block device,
turning a raw, unformatted partition ([this module's previous topic](fdisk-parted-lsblk.md)) into
something that can actually be mounted and store files.

## Why does it exist?

A partition on its own is just a defined region of a disk with no internal structure for storing
files — a filesystem (ext4, xfs, and the others introduced in
[Module 05](../05-file-system/filesystem-types.md)) is what actually organizes that raw space into
files, directories, and metadata. `mkfs` is the tool that writes that structure onto a partition
for the first time.

## Where is it used?

Preparing a newly created partition (a new cloud volume, an added disk) before it can be mounted
and used, and re-formatting a partition when deliberately changing its filesystem type.

## How it works

> 📊 Diagram: a raw, unformatted partition shown as empty space with no internal structure, then
> `mkfs -t ext4` shown writing filesystem metadata (superblock, inode tables — from
> [Module 05's inodes topic](../05-file-system/inodes-explained.md)) onto it, producing a
> partition that can now be mounted and hold files.

| Command | Effect |
|---|---|
| `mkfs -t ext4 /dev/sdb1` | Format a partition with ext4 |
| `mkfs -t xfs /dev/sdb1` | Format a partition with xfs |
| `mkfs.ext4 /dev/sdb1` | Equivalent shorthand form for a specific filesystem type |

`mkfs` is genuinely destructive — it erases whatever filesystem structure (and therefore data)
existed on that partition previously. This is a one-way operation without a built-in undo.

## Real-world example

An engineer means to format a newly attached data volume (`/dev/sdb1`) but mistypes the device
name as `/dev/sda1` — the server's existing root partition. Running `mkfs` against the wrong
device destroys the root filesystem's data instantly and irreversibly, exactly the kind of mistake
[the previous topic's `lsblk`-first habit](fdisk-parted-lsblk.md) exists to prevent.

## Commands

- [`mkfs`](../../commands/mkfs.md) — full syntax and examples

## Production example

```
$ lsblk
sdb       8:16   0   50G  0 disk
└─sdb1    8:17   0   50G  0 part

$ sudo mkfs -t ext4 /dev/sdb1
mke2fs 1.47.0
Creating filesystem with 13107200 4k blocks and 3276800 inodes
```

Confirming with `lsblk` that `sdb1` is the correct, still-unformatted target before running
`mkfs` — the same verify-first discipline as the previous topic.

## Do / Don't

| Do | Don't |
|---|---|
| Confirm the exact device with `lsblk` immediately before running `mkfs` | Run `mkfs` against a device name from memory without re-checking |
| Choose a filesystem type deliberately (ext4, xfs) based on the workload | Format without considering which filesystem type actually fits the use case |
| Treat `mkfs` as a point of no return | Assume `mkfs` can be safely undone if the wrong device was targeted |

## Common mistakes

- Running `mkfs` against the wrong device due to a typo or stale assumption, destroying data
  irreversibly — the exact scenario in the real-world example above.
- Not choosing a filesystem type deliberately, defaulting to whatever was used last without
  considering the actual workload's needs.
- Assuming `mkfs` can be undone — there's no built-in "un-format" once it's run.

## Best practices

- Always re-verify the exact target device (`lsblk`) immediately before running `mkfs`, even if
  you checked it minutes earlier.
- Choose ext4 or xfs deliberately based on workload characteristics
  ([Module 05's filesystem types topic](../05-file-system/filesystem-types.md)), not by default
  habit alone.
- Treat any `mkfs` command as irreversible before running it, not after.

## Exercises

1. Explain in one sentence what `mkfs` actually does to a partition.
2. Describe why `mkfs` should always be preceded by a fresh `lsblk` check.
3. Explain why `mkfs` is considered a destructive, one-way operation.

## Quiz

**Q: What does `mkfs -t ext4 /dev/sdb1` do?**
<details><summary>Show answer</summary>
Formats the partition `/dev/sdb1` with an ext4 filesystem, writing the filesystem structure
(superblock, inode tables) needed to store files — erasing any prior contents.
</details>

**Q: Can a `mkfs` operation be undone if run against the wrong device?**
<details><summary>Show answer</summary>
No — `mkfs` is a destructive, one-way operation with no built-in undo; the prior filesystem
structure and data are gone.
</details>

## Interview questions

- What does `mkfs` do, and why is it considered a high-risk command? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `mkfs` formats a partition with a filesystem, the step between a raw partition and a mountable
  one.
- `mkfs` is destructive and irreversible — it erases whatever was there before.
- Always re-verify the target device with `lsblk` immediately before running it.
- Choose the filesystem type deliberately based on the workload, not by default habit.

## Related topics

- [Mounting and /etc/fstab](mounting-and-etc-fstab.md)
- [fdisk, parted, lsblk](fdisk-parted-lsblk.md)
