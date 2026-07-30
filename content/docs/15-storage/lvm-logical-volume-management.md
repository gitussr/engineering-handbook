---
title: "LVM (Logical Volume Management)"
description: "An abstraction layer between raw partitions and filesystems that allows resizing volumes, spanning multiple disks, and taking snapshots — flexibility raw partitions don't offer."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["15-storage/mounting-and-etc-fstab"]
relatedTopics: ["raid-basics"]
relatedCommands: []
careerRelevance: ["linux-administrator", "sre", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#lvm-explained"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/raid-basics"
prevTopic: "15-storage/mounting-and-etc-fstab"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["lvm explained", "physical volume logical volume group", "lvextend resize2fs", "lvm snapshot explained"]
canonicalUrl: "/docs/storage/lvm-logical-volume-management"
---

# LVM (Logical Volume Management)

🔴 Expert · Relevant for: Linux Administrator · SRE · Platform

> **TL;DR:** LVM adds a flexible layer between raw partitions and filesystems: physical volumes
> (real disks/partitions) combine into a volume group (a pooled storage capacity), from which
> logical volumes (resizable, filesystem-holding units) are carved out — letting you grow storage
> without the rigid constraints of a raw partition.

## What is it?

A storage management layer sitting between raw partitions
([this module's earlier topics](disks-and-partitions-overview.md)) and filesystems, adding
flexibility that raw partitions alone don't offer: combining multiple disks into one pool,
resizing storage without a full backup-repartition-restore cycle, and taking point-in-time
snapshots.

## Why does it exist?

A raw partition has a fixed size at creation time, and growing it later, or spanning it across
more than one physical disk, is either impossible or requires risky, disruptive repartitioning.
LVM exists specifically to remove those constraints: storage can be pooled across multiple disks
and resized on demand, without the downtime and risk a raw-partition approach would require.

## Where is it used?

Any production environment where storage needs might grow unpredictably (a database server's
data volume, a virtual machine's root disk) and where avoiding downtime for a resize operation
matters — LVM is extremely common in these exact scenarios.

## How it works

> 📊 Diagram: two physical disks shown combined into a single volume group, from which two
> logical volumes are carved out — one already mounted at `/data`, with an arrow showing that
> volume being extended into unused volume-group capacity without unmounting or rebooting.

| Layer | What it is |
|---|---|
| Physical Volume (PV) | A raw disk or partition brought under LVM's management |
| Volume Group (VG) | A pool of storage capacity formed by combining one or more PVs |
| Logical Volume (LV) | A resizable chunk of a VG's capacity — this is what actually gets formatted and mounted |

**The key benefit — online resizing**: a logical volume can be extended into unused volume-group
capacity while still mounted and in use (`lvextend`), followed by growing the filesystem itself
to match (`resize2fs` for ext4, `xfs_growfs` for xfs) — all without unmounting or rebooting.

**Snapshots**: LVM can take a point-in-time snapshot of a logical volume, useful for a consistent
backup or a safety checkpoint before a risky change, without stopping whatever is actively using
that volume.

## Real-world example

A database server's LVM-managed data volume starts running low on space. Rather than the
disruptive process a raw partition would require (backup, repartition, restore, significant
downtime), an administrator adds a new disk as a physical volume, extends the existing volume
group to include it, extends the logical volume into that new capacity, and grows the filesystem
— all while the database continues running and serving traffic throughout.

## Commands

No new canonical command pages — `pvcreate`, `vgcreate`, `lvcreate`, `lvextend`, and the other LVM
commands are shown for orientation in the production example below, but this module scopes LVM as
a concept rather than a set of individually documented commands.

## Production example

```
$ sudo pvcreate /dev/sdc
$ sudo vgcreate data-vg /dev/sdc
$ sudo lvcreate -L 100G -n data-lv data-vg
$ sudo mkfs -t ext4 /dev/data-vg/data-lv

# Later, extending an existing logical volume:
$ sudo lvextend -L +50G /dev/data-vg/data-lv
$ sudo resize2fs /dev/data-vg/data-lv
```

The extend sequence at the bottom runs against a volume that's still mounted and in active use.

## Do / Don't

| Do | Don't |
|---|---|
| Use LVM when future growth or multi-disk pooling is a realistic need | Default to LVM for every disk with no actual flexibility requirement |
| Extend a logical volume, then grow its filesystem to match | Extend the logical volume and forget the filesystem itself still needs growing |
| Use snapshots as a safety checkpoint before a risky change | Rely on a snapshot as a substitute for a real, separate backup |

## Common mistakes

- Extending a logical volume (`lvextend`) and forgetting the filesystem itself also needs to be
  grown (`resize2fs`/`xfs_growfs`) — the extra space isn't usable until both steps are done.
- Treating an LVM snapshot as a full backup substitute — a snapshot typically lives on the same
  underlying storage and doesn't protect against a hardware failure the way an independent backup
  does.
- Adding LVM complexity to a system with no realistic future need for resizing or multi-disk
  pooling, when a plain partition would have been simpler.

## Best practices

- Use LVM specifically when flexibility (growth, multi-disk pooling, snapshotting) is a real,
  anticipated need — not as a default for every disk.
- Always follow `lvextend` with the matching filesystem-resize command.
- Treat LVM snapshots as a convenience for short-lived checkpoints, not a replacement for a real
  backup strategy.

## Exercises

1. Explain in one sentence what a physical volume, volume group, and logical volume each are.
2. Describe why growing a logical volume alone isn't enough to actually use the extra space.
3. Explain the key operational benefit LVM provides that a raw partition doesn't.

## Quiz

**Q: What are the three layers of LVM, from raw disk to mountable unit?**
<details><summary>Show answer</summary>
Physical Volume (a raw disk/partition) → Volume Group (a pool combining one or more PVs) →
Logical Volume (a resizable chunk of the VG, which actually gets formatted and mounted).
</details>

**Q: After running `lvextend` to grow a logical volume, what else must be done before the extra
space is usable?**
<details><summary>Show answer</summary>
The filesystem itself must also be grown (`resize2fs` for ext4, `xfs_growfs` for xfs) — extending
the logical volume alone doesn't extend the filesystem within it.
</details>

## Interview questions

- What problem does LVM solve that a raw partition can't? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- LVM adds a flexible layer: Physical Volumes combine into a Volume Group, from which Logical
  Volumes are carved out.
- Logical volumes can be resized online, without unmounting or rebooting.
- Growing a logical volume requires also growing its filesystem to match.
- LVM snapshots are useful checkpoints, not a substitute for a real backup.

## Related topics

- [RAID Basics](raid-basics.md)
- [Mounting and /etc/fstab](mounting-and-etc-fstab.md)
