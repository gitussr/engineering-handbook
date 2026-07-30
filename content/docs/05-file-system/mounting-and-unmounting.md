---
title: "Mounting and Unmounting"
description: "How a storage device becomes part of the filesystem tree — mount, umount, and a first look at /etc/fstab."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["05-file-system/filesystem-types"]
relatedTopics: ["proc-and-sys-virtual-filesystems"]
relatedCommands: ["mount", "umount"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#mounting"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "05-file-system/proc-and-sys-virtual-filesystems"
prevTopic: "05-file-system/filesystem-types"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["mount command linux", "umount command", "etc fstab", "device busy unmount"]
canonicalUrl: "/docs/file-system/mounting-and-unmounting"
---

# Mounting and Unmounting

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** Mounting attaches a storage device's filesystem to a specific point in the directory
> tree, making it accessible as if it were just another folder. `/etc/fstab` defines what mounts
> automatically at boot. "Device is busy" on unmount means something still has an open file on it.

## What is it?

Mounting is the process of attaching a filesystem (from a disk, partition, network share, or
virtual device) to a specific directory (the **mount point**), making its contents accessible
through that directory. Unmounting detaches it.

## Why does it exist?

Unlike Windows, which assigns separate drive letters (`C:`, `D:`), Linux presents everything as
one unified directory tree starting at `/`. Mounting is the mechanism that makes a second disk, a
USB drive, or a network share appear as a normal directory within that single tree instead of a
separate, disconnected namespace.

## Where is it used?

- Attaching additional storage volumes to a server (a separate data disk mounted at `/data`)
- Automatically mounting filesystems at boot via `/etc/fstab`
- Mounting removable media (USB drives) and network shares (NFS, CIFS)
- Understanding container storage — Docker volumes are, under the hood, a mount

## How it works

> 📊 Diagram: a before/after tree diagram — before mounting, `/mnt/data` is just an empty
> directory and the second disk's filesystem is invisible; after `mount /dev/xvdb1 /mnt/data`,
> the disk's contents appear directly under `/mnt/data` as if they'd always been there.

- **Mount point**: an existing, normally empty directory that a filesystem gets attached to.
- **`mount device mountpoint`**: attaches a device's filesystem at that directory, immediately
  and only until reboot (unless also configured in `/etc/fstab`).
- **`/etc/fstab`**: a config file listing filesystems that should be mounted automatically at
  boot — device, mount point, filesystem type, and options, one line per filesystem. Full syntax
  detail is in [Module 15: Storage](../15-storage/index.md); this page only flags that it exists
  and what problem it solves (not needing to manually `mount` after every reboot).
- **`umount mountpoint`**: detaches a mounted filesystem. Fails with "device is busy" if any
  process still has an open file or working directory on that filesystem.

## Real-world example

A company attaches a new, larger data volume to a database server. An engineer mounts it
temporarily at `/mnt/newdata` to verify it's correct and empty, then adds an entry to
`/etc/fstab` so it mounts automatically at `/data` on every future boot — without that `/etc/fstab`
entry, the volume would need to be manually remounted after every server restart, a real
operational risk if forgotten.

## Commands

- [`mount`](../../commands/mount.md) — attach a filesystem
- [`umount`](../../commands/umount.md) — detach a filesystem

## Production example

```
$ mount /dev/xvdb1 /mnt/data
$ df -h /mnt/data
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvdb1       50G   1.2G   46G   3% /mnt/data
$ umount /mnt/data
umount: /mnt/data: target is busy.
```

The failed `umount` above is a common, real scenario — something (a shell session `cd`'d into
`/mnt/data`, or a process with an open file there) is still using the mount point.

## Do / Don't

| Do | Don't |
|---|---|
| Add a new persistent mount to `/etc/fstab`, not just a manual `mount` | Rely on a manual `mount` alone and lose it on the next reboot |
| Check what's using a mount point before force-unmounting | Force-unmount a busy filesystem without knowing why it's busy |
| Verify a new volume before writing `/etc/fstab` (a bad entry can prevent boot) | Edit `/etc/fstab` carelessly on a production system |

## Common mistakes

- Mounting manually and forgetting to add an `/etc/fstab` entry, losing the mount on the next
  reboot — a common cause of "why is my data missing" after a routine restart.
- Trying to unmount a filesystem that's still in use (a shell session sitting inside it, an open
  file) and not understanding the "device is busy" error.
- Writing a bad `/etc/fstab` entry, which on some systems can prevent the machine from booting
  cleanly — a real reason to be careful and test changes before rebooting a production server.

## Best practices

- Always add a persistent mount to `/etc/fstab` once you've verified it's correct — don't leave
  it as a manual, reboot-losable mount.
- Before force-unmounting, use process-inspection tools (`lsof`, covered later) to find what's
  actually holding the mount point busy, rather than guessing.
- Test new `/etc/fstab` entries carefully — a mistake there can affect the next boot.

## Exercises

1. Identify the mount points on your own system with `df -h` or `mount` with no arguments.
2. Explain in one sentence why Linux doesn't use drive letters the way Windows does.
3. Explain what "device is busy" means when unmounting fails.

## Quiz

**Q: What does "device is busy" mean when trying to `umount` a filesystem?**
<details><summary>Show answer</summary>
Something — an open file, or a shell session with its current directory inside the mount point —
is still actively using that filesystem, and it must be released before unmounting will succeed.
</details>

**Q: What happens to a manual `mount` if you don't add it to `/etc/fstab`?**
<details><summary>Show answer</summary>
It's lost on the next reboot — `/etc/fstab` is what makes a mount persistent across restarts.
</details>

## Interview questions

- Why would you add a mount to `/etc/fstab` instead of just running `mount` manually? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Mounting attaches a filesystem to a directory (the mount point) within Linux's single unified
  tree — no drive letters.
- A manual `mount` doesn't survive a reboot; `/etc/fstab` is what makes a mount persistent.
- "Device is busy" on unmount means something still has an open file or working directory there.
- Full `/etc/fstab` syntax and disk management live in Module 15: Storage.

## Related topics

- [Filesystem Types](filesystem-types.md)
- [Module 15: Storage](../15-storage/index.md)
