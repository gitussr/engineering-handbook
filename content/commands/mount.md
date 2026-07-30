---
title: "mount — Attach a Filesystem"
description: "Attach a device's filesystem to a directory in the filesystem tree, or list currently mounted filesystems."
relatedConcepts: ["05-file-system/mounting-and-unmounting"]
relatedCommands: ["umount"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["mount command linux", "mount device to directory", "mount -t"]
canonicalUrl: "/commands/mount"
---

# mount

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `mount device mountpoint` attaches a filesystem. Run `mount` alone to list everything
> currently mounted. Manual mounts don't survive a reboot — see `/etc/fstab` for that.

## Purpose

`mount` attaches a filesystem (from a disk, partition, network share, or virtual device) to a
directory, making its contents accessible there — the command behind
[Mounting and Unmounting](../docs/05-file-system/mounting-and-unmounting.md).

## Syntax

```
mount [OPTIONS] DEVICE MOUNTPOINT
mount
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `DEVICE` | The device or partition to mount (e.g. `/dev/xvdb1`) | Yes (unless just listing mounts) |
| `MOUNTPOINT` | An existing directory to attach the filesystem to | Yes (unless just listing mounts) |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-t TYPE` | `--types` | Specify the filesystem type explicitly (usually auto-detected) |
| `-o OPTIONS` | `--options` | Mount options, comma-separated (e.g. `ro` for read-only) |
| `-a` | `--all` | Mount everything listed in `/etc/fstab` |
| `-r` | `--read-only` | Mount as read-only |

## Examples

```
$ mount /dev/xvdb1 /mnt/data
```
Mount a device at a directory.

```
$ mount -o ro /dev/sr0 /mnt/cdrom
```
Mount read-only.

```
$ mount
```
List all currently mounted filesystems.

```
$ mount -a
```
Mount everything listed in `/etc/fstab` (useful after editing it, to test without rebooting).

## Expected Output

```
$ mount | grep xvdb1
/dev/xvdb1 on /mnt/data type ext4 (rw,relatime)
```

## Exit Status

`0` on success, non-zero if the device doesn't exist, the mount point doesn't exist, or the
filesystem type can't be determined/isn't supported.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `mount: special device DEVICE does not exist` | Wrong device path | Verify with `lsblk` (covered in Module 15) |
| `mount: mount point MOUNTPOINT does not exist` | The target directory hasn't been created | Create it first with `mkdir` |
| `mount: only root can do that` | Mounting typically requires root privileges | Use `sudo` |

## Security Considerations

Mounting untrusted removable media without care (e.g. an unknown USB drive) can expose a system
to malicious content; mount options like `noexec` and `nosuid` restrict what a mounted filesystem
can do, relevant for hardened environments.

## Performance Considerations

Mount options can affect performance (e.g. `noatime` avoids the overhead of updating file access
times) — a common tuning choice on high-I/O servers, covered further in Module 21.

## Production Usage

`mount -a` after editing `/etc/fstab` is the standard way to test that new fstab entries are
valid without needing to reboot the server to find out.

## Related Commands

- [`umount`](umount.md) — detach a mounted filesystem

## Related Concepts

- [Mounting and Unmounting](../docs/05-file-system/mounting-and-unmounting.md)
