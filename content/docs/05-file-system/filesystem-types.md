---
title: "Filesystem Types: ext4, xfs, btrfs, ntfs"
description: "What a filesystem type actually controls, and how to choose between ext4, xfs, and btrfs for a new Linux volume."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["05-file-system/core-directories-explained"]
relatedTopics: ["mounting-and-unmounting", "inodes-explained"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ext4-vs-xfs"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "05-file-system/mounting-and-unmounting"
prevTopic: "05-file-system/core-directories-explained"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["ext4 vs xfs", "btrfs vs ext4", "ntfs on linux", "filesystem type comparison"]
canonicalUrl: "/docs/file-system/filesystem-types"
---

# Filesystem Types: ext4, xfs, btrfs, ntfs

🟡 Good to Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** A filesystem type is the format data is actually written to disk in. ext4 is the
> safe general-purpose default, xfs excels with very large files and is RHEL's default, btrfs
> adds snapshots and built-in volume management at some cost to maturity/stability track record.
> NTFS is Windows' filesystem, relevant on Linux mainly for reading external/dual-boot drives.

## What is it?

A filesystem type defines how data, metadata, and directory structure are actually organized and
written on a storage device. It's a different layer from the FHS (which defines *where* things
go); the filesystem type defines *how* the underlying bytes are structured on disk.

## Why does it exist?

Different filesystem types make different tradeoffs — performance characteristics, maximum file
size, journaling/crash-recovery behavior, and advanced features like snapshots or built-in
compression. No single filesystem type is best for every workload, which is why Linux supports
choosing one at creation time (previewed here, full `mkfs` syntax in
[Module 15: Storage](../15-storage/index.md)).

## Where is it used?

- **ext4** — the long-standing general-purpose default across most distros; mature, reliable,
  well-understood failure modes.
- **xfs** — RHEL/CentOS/Rocky's default; excels with large files and high-throughput workloads
  (common in database and media storage use cases).
- **btrfs** — a newer filesystem with built-in snapshots, checksums, and volume management
  (overlapping with what LVM provides separately for other filesystems); openSUSE's default,
  gaining adoption elsewhere.
- **NTFS** — Windows' native filesystem; relevant on Linux mainly for reading/writing external
  drives or dual-boot Windows partitions, not chosen for a native Linux install.

## How it works

> 📊 Diagram: a simple comparison table rendered visually — four columns (ext4, xfs, btrfs, ntfs)
> with rows for "Typical use," "Snapshots built in," "Maturity," "Common on" — to make tradeoffs
> scannable.

| Filesystem | Typical use | Snapshots built in | Common on |
|---|---|---|---|
| ext4 | General-purpose default | No (needs LVM) | Ubuntu, Debian (often default) |
| xfs | Large files, high throughput | No (needs LVM) | RHEL, CentOS, Rocky (default) |
| btrfs | Snapshots, built-in volume management | Yes | openSUSE (default), growing elsewhere |
| ntfs | Windows-native | No | Dual-boot/external drives read from Linux |

A filesystem type is chosen when a partition/volume is formatted (`mkfs.ext4`, `mkfs.xfs`, etc.
— full syntax in Module 15) and generally can't be changed without reformatting, which is why the
choice matters upfront rather than being easily revisited later.

## Real-world example

A company running a database server chooses xfs specifically for its strong performance with
large files and high write throughput, matching their database's I/O pattern — while their
general-purpose application servers stay on ext4, the distro default, since there's no specific
reason to deviate. The filesystem choice was made deliberately per workload, not uniformly.

## Commands

No new canonical command page on this page — `mkfs` (filesystem creation) is previewed only;
full syntax lives in [Module 15: Storage](../15-storage/index.md).

## Production example

```
$ df -T /
Filesystem     Type  1K-blocks    Used Available Use% Mounted on
/dev/xvda1     ext4   20511312 8234156  11212345  43% /
```

The `-T` flag on `df` (full coverage in Module 15) reveals which filesystem type a mounted volume
is actually using.

## Do / Don't

| Do | Don't |
|---|---|
| Choose xfs for large-file, high-throughput workloads (databases, media) | Default to a filesystem type without considering the actual workload |
| Stick with ext4 as a safe general-purpose default | Choose btrfs purely for its features without weighing its shorter production track record |

## Common mistakes

- Assuming filesystem type is a "set once, never think about again" decision without matching it
  to the actual workload — xfs and ext4 genuinely perform differently under different I/O
  patterns.
- Not realizing changing filesystem type generally requires a reformat (and data migration), not
  an in-place conversion.
- Treating NTFS as a viable choice for a native Linux system install instead of what it actually
  is — a compatibility option for reading Windows-formatted drives.

## Best practices

- Default to ext4 unless a specific workload characteristic (large files, need for snapshots)
  points to xfs or btrfs instead.
- Match filesystem type to workload deliberately for anything performance-sensitive, rather than
  accepting a blanket default everywhere.
- Research a filesystem's maturity and your team's operational familiarity with it before
  adopting something newer (btrfs) for production-critical data.

## Exercises

1. Run `df -T` on a Linux system and identify which filesystem type each mounted volume uses.
2. Explain in one sentence why a database server might prefer xfs over ext4.
3. Explain why NTFS wouldn't be chosen for a native Linux system install.

## Quiz

**Q: What does a filesystem type actually control?**
<details><summary>Show answer</summary>
How data, metadata, and directory structure are physically organized and written on the storage
device — a different concern from FHS, which controls where things logically go.
</details>

**Q: Which filesystem type is RHEL/CentOS/Rocky's default?**
<details><summary>Show answer</summary>
xfs.
</details>

## Interview questions

- When would you choose xfs over ext4? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Filesystem type controls how data is physically structured on disk — a different layer from FHS.
- ext4 is the safe general-purpose default; xfs suits large-file/high-throughput workloads; btrfs
  adds built-in snapshots at the cost of a shorter production track record.
- NTFS is relevant on Linux only for reading Windows-formatted drives, not for a native install.
- Changing filesystem type generally requires a reformat, so the choice matters upfront.

## Related topics

- [Mounting and Unmounting](mounting-and-unmounting.md)
- [Module 15: Storage](../15-storage/index.md)
