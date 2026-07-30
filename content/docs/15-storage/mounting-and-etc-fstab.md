---
title: "Mounting and /etc/fstab"
description: "Full /etc/fstab syntax — the six fields that make a mount survive a reboot, deferred from Module 05's introduction of mount and umount."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/creating-filesystems-mkfs"]
relatedTopics: ["lvm-logical-volume-management"]
relatedCommands: ["mount"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#fstab-fields"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/lvm-logical-volume-management"
prevTopic: "15-storage/creating-filesystems-mkfs"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["etc fstab syntax", "fstab fields explained", "fstab uuid", "fstab nofail option"]
canonicalUrl: "/docs/storage/mounting-and-etc-fstab"
---

# Mounting and /etc/fstab

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `/etc/fstab` has one line per filesystem, six fields each: device, mount point,
> filesystem type, options, dump flag, fsck order. Use a UUID (not a device name like `/dev/sdb1`)
> to avoid a broken boot if device naming ever shifts.

## What is it?

The full syntax of `/etc/fstab`, the file that makes a mount survive a reboot — introduced at a
concept level in [Module 05](../05-file-system/mounting-and-unmounting.md), which explicitly
deferred the full field-by-field syntax to this module.

## Why does it exist?

A manual `mount` command ([Module 05](../05-file-system/mounting-and-unmounting.md)) only lasts
until the next reboot. `/etc/fstab` exists so a system administrator can declare, once, which
filesystems should be mounted automatically and consistently every time the system boots — the
newly created filesystem from [the previous topic](creating-filesystems-mkfs.md) needs exactly
this to be usable without manual intervention after every restart.

## Where is it used?

Every production server with more than the default root filesystem: a dedicated data volume, a
swap partition (this module's later topic), or an NFS share, all rely on an `/etc/fstab` entry to
be available automatically after every boot.

## How it works

> 📊 Diagram: one `/etc/fstab` line broken into its six labeled fields (device, mount point,
> filesystem type, options, dump, fsck order), each field's purpose annotated directly beneath
> it.

```
UUID=1a2b3c4d-... /data ext4 defaults 0 2
```

| Field | Meaning |
|---|---|
| 1. Device | The filesystem to mount — a UUID (recommended) or device path (`/dev/sdb1`) |
| 2. Mount point | The directory to mount it at |
| 3. Filesystem type | `ext4`, `xfs`, etc. |
| 4. Options | Mount options, `defaults` for the common case |
| 5. Dump | Legacy backup flag, `0` almost always (unused on modern systems) |
| 6. fsck order | Filesystem check order at boot; `0` = skip, `1` = root filesystem, `2` = everything else |

**Why UUID over a device path**: device names like `/dev/sdb1` can shift if disks are added,
removed, or reordered at the hardware/hypervisor level — a UUID (viewable via `lsblk -f` or
`blkid`) is fixed to that specific filesystem regardless of which device name the kernel assigns
it on a given boot.

**`nofail` option**: without it, a missing or failing entry in `/etc/fstab` can actually prevent
the system from booting at all — `nofail` tells the boot process to continue even if that
particular filesystem can't be mounted, appropriate for non-critical mounts (though not for the
root filesystem itself).

## Real-world example

A server's data volume is added to `/etc/fstab` using its device path (`/dev/sdb1`) rather than
its UUID. After an unrelated hardware change, the kernel assigns that same physical volume a
different device name on the next boot — the `/etc/fstab` entry now points at the wrong device
(or nothing), and depending on other options, the system either mounts the wrong volume at that
mount point or fails to boot cleanly. Using the volume's UUID instead would have made the entry
immune to the device name change entirely.

## Commands

- [`mount`](../../commands/mount.md) — already covered in Module 05; `mount -a` reads and applies
  every `/etc/fstab` entry without rebooting, the standard way to test a new entry safely.

## Production example

```
$ lsblk -f
NAME   FSTYPE UUID                                 MOUNTPOINT
sdb1   ext4   1a2b3c4d-5e6f-7890-abcd-ef1234567890

$ echo "UUID=1a2b3c4d-5e6f-7890-abcd-ef1234567890 /data ext4 defaults,nofail 0 2" | sudo tee -a /etc/fstab

$ sudo mount -a
```

`mount -a` applies the new entry immediately, letting you confirm it works correctly before ever
rebooting the server.

## Do / Don't

| Do | Don't |
|---|---|
| Use a UUID (from `lsblk -f`/`blkid`), not a device path, in `/etc/fstab` | Reference `/dev/sdb1` directly, risking a shifted device name later |
| Test a new entry with `mount -a` before rebooting | Add an entry and only find out it's broken on the next reboot |
| Use `nofail` for non-critical mounts | Let a single non-critical filesystem failure block the entire boot |

## Common mistakes

- Using a device path instead of a UUID, breaking silently (or loudly, at boot) if device naming
  ever shifts — the exact scenario in the real-world example above.
- Not testing a new `/etc/fstab` entry with `mount -a` before rebooting, discovering a syntax
  error only when the system fails to boot cleanly.
- Omitting `nofail` on a non-critical mount, letting one failing non-essential filesystem block
  the entire boot process unnecessarily.

## Best practices

- Always use `UUID=` in `/etc/fstab` entries, retrieved with `lsblk -f` or `blkid`.
- Test every new entry with `mount -a` before rebooting, to catch a syntax or device error safely.
- Add `nofail` to non-critical mounts so a single failing filesystem doesn't prevent the whole
  system from booting.

## Exercises

1. Find a filesystem's UUID with `lsblk -f` on a system you have access to.
2. Write an `/etc/fstab` line for a hypothetical data volume, using UUID and the `nofail` option.
3. Explain in one sentence why `mount -a` is the safe way to test a new `/etc/fstab` entry.

## Quiz

**Q: Why is using a UUID preferred over a device path like `/dev/sdb1` in `/etc/fstab`?**
<details><summary>Show answer</summary>
Device paths can shift if disks are added, removed, or reordered — a UUID stays fixed to that
specific filesystem regardless of which device name the kernel assigns it on a given boot.
</details>

**Q: What does the `nofail` mount option do, and when should you use it?**
<details><summary>Show answer</summary>
It allows the boot process to continue even if that specific filesystem fails to mount — use it
for non-critical mounts so one failure doesn't block the entire system from booting.
</details>

## Interview questions

- Walk through the six fields of an `/etc/fstab` entry. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/etc/fstab` has six fields per line: device, mount point, filesystem type, options, dump flag,
  fsck order.
- Use a UUID, not a device path, to avoid breakage if device naming shifts.
- `mount -a` applies new `/etc/fstab` entries immediately, letting you test before rebooting.
- `nofail` prevents a single non-critical mount failure from blocking the entire boot.

## Related topics

- [LVM (Logical Volume Management)](lvm-logical-volume-management.md)
- [Module 05: Mounting and Unmounting](../05-file-system/mounting-and-unmounting.md)
