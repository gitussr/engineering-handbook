---
title: "RAID Basics"
description: "Combining multiple disks for redundancy, performance, or both — the tradeoffs between the common RAID levels and what RAID does and doesn't protect against."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["15-storage/lvm-logical-volume-management"]
relatedTopics: ["lvm-logical-volume-management"]
relatedCommands: []
careerRelevance: ["linux-administrator", "sre", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#raid-levels"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/disk-usage-df-du"
prevTopic: "15-storage/lvm-logical-volume-management"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["raid 0 vs raid 1 vs raid 5", "raid explained", "raid is not a backup", "mdadm raid linux"]
canonicalUrl: "/docs/storage/raid-basics"
---

# RAID Basics

🔴 Expert · Relevant for: Linux Administrator · SRE · Platform

> **TL;DR:** RAID combines multiple physical disks into one logical unit for redundancy,
> performance, or both — RAID 0 stripes for speed with zero redundancy, RAID 1 mirrors for full
> redundancy, RAID 5/6 balance both with parity. RAID protects against a disk failure — it is not
> a backup, and does not protect against deletion, corruption, or a site-level disaster.

## What is it?

RAID (Redundant Array of Independent Disks) combines multiple physical disks into one logical
storage unit, using one of several standard "levels," each making a different tradeoff between
redundancy (surviving a disk failure), performance, and usable capacity.

## Why does it exist?

A single disk is a single point of failure — if it fails, whatever it stored is gone. RAID exists
to let a group of disks tolerate the failure of one (or, depending on level, more) without losing
data or availability, and in some configurations, to improve read/write performance by spreading
work across multiple disks simultaneously.

## Where is it used?

Production servers where disk failure must not mean data loss or downtime — database servers,
file servers, and any storage layer where the underlying disks are expected to eventually fail
(all disks do, eventually) and the system needs to keep functioning when one does.

## How it works

> 📊 Diagram: four small panels, one per RAID level — RAID 0 (data striped evenly across two
> disks, no redundancy, one disk failure loses everything), RAID 1 (data fully mirrored across
> two disks, either can fail with zero data loss), RAID 5 (data plus distributed parity across
> three or more disks, tolerates one disk failure), RAID 6 (like RAID 5 but tolerates two
> simultaneous disk failures).

| Level | Redundancy | Usable capacity | Tradeoff |
|---|---|---|---|
| RAID 0 | None | 100% of total disks | Fastest, but any single disk failure loses everything |
| RAID 1 | Full mirror | 50% (of a 2-disk set) | Simple, safe, but "wastes" half the raw capacity |
| RAID 5 | Tolerates 1 disk failure | N-1 disks' worth | Balances redundancy and capacity via distributed parity |
| RAID 6 | Tolerates 2 disk failures | N-2 disks' worth | More fault-tolerant than RAID 5, at more capacity cost |

**The critical point often misunderstood**: RAID protects against a physical disk failure. It
does **not** protect against accidental deletion, filesystem corruption, ransomware, or a
site-level disaster — a mistakenly deleted file is mirrored/parity-protected just as faithfully
as any other data, and gone from every disk in the array just as completely. RAID is not a
substitute for a real, separate backup.

## Real-world example

A team relies on RAID 1 as their only data protection and treats it as "the backup." An engineer
accidentally deletes a critical directory — RAID 1 faithfully mirrors that deletion to the second
disk instantly, and the data is gone from the array entirely, with no backup to recover from. RAID
did exactly what it was designed to do (protect against a disk failure); it was never designed to
protect against this kind of mistake, which is precisely why "RAID is not a backup" is one of the
most repeated warnings in storage administration.

## Commands

No new canonical command pages — RAID arrays are commonly managed with `mdadm` (Linux software
RAID) or configured at the hardware controller level; this module scopes RAID as a concept rather
than a set of individually documented commands.

## Production example

```
$ cat /proc/mdstat
Personalities : [raid1]
md0 : active raid1 sdb1[1] sda1[0]
      52428672 blocks super 1.2 [2/2] [UU]
```

`[2/2] [UU]` indicates both disks in this RAID 1 array are present and healthy — `[U_]` would
indicate one disk has failed or is missing.

## Do / Don't

| Do | Don't |
|---|---|
| Maintain a real, separate backup in addition to RAID | Treat RAID as a substitute for backups |
| Choose a RAID level based on the actual redundancy/performance/capacity tradeoff needed | Default to RAID 0 for production data without weighing its zero-redundancy tradeoff |
| Monitor array health (`/proc/mdstat` or equivalent) proactively | Assume a RAID array is healthy without ever checking |

## Common mistakes

- Treating RAID as a backup, learning the difference only after an accidental deletion or
  corruption event destroys data across every disk in the array simultaneously.
- Choosing RAID 0 for data that actually needs redundancy, drawn in by its performance and full
  capacity, then losing everything to a single disk failure.
- Never monitoring array health, only discovering a already-failed disk in a RAID 5/6 array when a
  second failure causes real data loss.

## Best practices

- Always maintain backups independent of RAID — RAID and backup solve different problems.
- Match the RAID level to the actual requirement: RAID 1 for simple redundancy, RAID 5/6 when
  balancing redundancy against usable capacity across more disks, RAID 0 only for genuinely
  disposable, performance-only data.
- Monitor array health proactively so a single disk failure gets addressed before a second one
  turns into real data loss (especially critical for RAID 5, which only tolerates one failure).

## Exercises

1. Explain in one sentence why RAID is not a substitute for a backup.
2. Compare RAID 1 and RAID 5 in terms of redundancy and usable capacity.
3. Explain what `[2/2] [UU]` in `/proc/mdstat` output indicates about array health.

## Quiz

**Q: Why doesn't RAID protect against an accidentally deleted file?**
<details><summary>Show answer</summary>
RAID protects against a physical disk failure — an accidental deletion is faithfully replicated
(mirrored or parity-updated) across the array just like any other write, so the file is gone from
every disk, not just one.
</details>

**Q: What's the key tradeoff of RAID 0 compared to RAID 1?**
<details><summary>Show answer</summary>
RAID 0 offers full capacity and the best performance but zero redundancy — any single disk
failure loses all data. RAID 1 mirrors data fully, tolerating a disk failure at the cost of half
the usable capacity.
</details>

## Interview questions

- Why is "RAID is not a backup" one of the most important things to understand about RAID? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- RAID combines disks for redundancy, performance, or both, depending on the level chosen.
- RAID 0 has no redundancy; RAID 1 fully mirrors; RAID 5/6 balance redundancy and capacity via
  parity.
- RAID protects against a physical disk failure — it does not protect against deletion,
  corruption, or disaster, and is not a substitute for backups.
- Array health should be actively monitored, especially for levels that only tolerate one disk
  failure.

## Related topics

- [LVM (Logical Volume Management)](lvm-logical-volume-management.md)
- [Disk Usage: df, du](disk-usage-df-du.md)
