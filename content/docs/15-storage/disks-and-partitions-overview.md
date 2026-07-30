---
title: "Disks and Partitions Overview"
description: "A physical or virtual disk is divided into partitions before it can hold a filesystem — what a partition actually is and why disks aren't used raw."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["14-package-managers/package-signing-and-verification"]
relatedTopics: ["fdisk-parted-lsblk"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#partition-vs-filesystem"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/fdisk-parted-lsblk"
prevTopic: "14-package-managers/package-signing-and-verification"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["what is a disk partition", "partition table explained", "gpt vs mbr", "block device explained"]
canonicalUrl: "/docs/storage/disks-and-partitions-overview"
---

# Disks and Partitions Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** A disk is divided into one or more partitions before it can be formatted and mounted.
> A partition table (GPT or the older MBR) records where each partition starts and ends on the
> disk. Each partition then gets its own filesystem, independent of the others.

## What is it?

A partition is a defined region of a physical or virtual disk, recorded in a partition table at
the start of the disk — the structural layer that exists between "a raw disk" and "a mountable
filesystem" ([Module 05](../05-file-system/mounting-and-unmounting.md)).

## Why does it exist?

Using an entire disk as one undivided space works, but partitioning exists to allow deliberate
separation: a single disk can hold a boot partition, a swap partition, and one or more data
partitions, each managed and mounted independently — useful for organization, for isolating one
area from filling up and affecting another, and because some things (like swap) need their own
dedicated, specially-formatted partition entirely.

## Where is it used?

Every disk on every Linux system has at least one partition (even a "whole disk" setup typically
has one partition spanning it), and any deliberate disk layout — separating `/`, `/home`, swap,
or a dedicated data volume — happens at the partition level before a filesystem is ever created.

## How it works

> 📊 Diagram: a single physical disk shown divided into three partitions — a small boot
> partition, a swap partition, and a large data partition — each with its own entry in the disk's
> partition table (start/end location, type), each eventually getting its own independent
> filesystem.

| Concept | What it is |
|---|---|
| Disk (block device) | The raw physical or virtual storage device (e.g. `/dev/sda`, `/dev/xvdb`) |
| Partition table | A small data structure at the start of the disk recording each partition's location and type |
| Partition | A defined region of the disk (e.g. `/dev/sda1`), later formatted with a filesystem |

**GPT vs. MBR**: GPT (GUID Partition Table) is the modern standard, supporting more partitions
and much larger disks; MBR (Master Boot Record) is the older, legacy standard with real
limitations (a maximum of 4 primary partitions, a 2TB disk size ceiling). New systems should
default to GPT unless there's a specific legacy compatibility requirement.

**Naming convention**: a partition is named after its parent disk plus a number —
`/dev/sda` (the disk) → `/dev/sda1`, `/dev/sda2` (its partitions).

## Real-world example

An engineer provisions a new server with a single large disk and, rather than using it as one
undivided partition, creates separate partitions for the root filesystem and a dedicated data
volume. Months later, a runaway log file fills up disk space rapidly — because logs live on the
same partition as the data volume in this layout, the root filesystem (and the system's ability
to function normally) is unaffected, illustrating exactly why separating partitions by purpose
is a common, deliberate production practice.

## Commands

No command example on this page — this page is conceptual foundation. See
[fdisk, parted, lsblk](fdisk-parted-lsblk.md) for the first hands-on commands in this module.

## Production example

Not applicable — see [fdisk, parted, lsblk](fdisk-parted-lsblk.md) for the first terminal session
in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Default to GPT for new disk layouts unless legacy compatibility is specifically required | Use MBR by default on a new system with no legacy constraint |
| Separate partitions by purpose (root, data, swap) when isolation matters | Use one undivided partition for everything on a server where isolation would help |
| Understand a partition must exist before a filesystem can be created on it | Confuse "partitioning" with "formatting" — they're two separate steps |

## Common mistakes

- Confusing partitioning (defining a region of the disk) with formatting (creating a filesystem
  within that region) — they're sequential, separate operations.
- Using MBR on a new system without a specific legacy reason, missing out on GPT's larger disk
  and partition count support.
- Using a single undivided partition where separating concerns (like isolating logs from the root
  filesystem) would have contained an incident instead of letting it affect everything.

## Best practices

- Default to GPT for any new disk layout.
- Separate partitions by purpose when isolation genuinely matters for the workload (a database
  server's data volume, a dedicated swap partition).
- Understand the partition table itself before making changes — a mistake there can affect an
  entire disk's data.

## Exercises

1. Explain in one sentence the difference between partitioning and formatting.
2. Explain why GPT is generally preferred over MBR on new systems.
3. Describe one practical reason to separate a data volume onto its own partition, rather than
   using one partition for everything.

## Quiz

**Q: What's the difference between partitioning a disk and formatting it?**
<details><summary>Show answer</summary>
Partitioning defines a region of the disk (recorded in a partition table); formatting creates a
filesystem within that region afterward — two separate, sequential steps.
</details>

**Q: What's one practical limitation of the older MBR partitioning scheme compared to GPT?**
<details><summary>Show answer</summary>
MBR supports a maximum of 4 primary partitions and a 2TB disk size ceiling; GPT supports far more
partitions and much larger disks.
</details>

## Interview questions

- What's the difference between a disk, a partition, and a filesystem? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A partition is a defined region of a disk, recorded in a partition table (GPT or MBR).
- Partitioning and formatting are separate, sequential steps.
- GPT is the modern standard; MBR is legacy with real size and count limitations.
- Separating partitions by purpose isolates one area's problems (like a full disk) from others.

## Related topics

- [fdisk, parted, lsblk](fdisk-parted-lsblk.md)
- [Module 05: Mounting and Unmounting](../05-file-system/mounting-and-unmounting.md)
