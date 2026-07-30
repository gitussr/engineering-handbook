---
title: "parted — GPT-Native Partition Editor"
description: "Create or modify a partition table, interactively or scriptably — GPT-native, unlike fdisk's historical MBR focus."
relatedConcepts: ["15-storage/fdisk-parted-lsblk"]
relatedCommands: ["fdisk", "lsblk"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["parted examples", "parted mklabel gpt", "parted mkpart", "parted scriptable"]
canonicalUrl: "/commands/parted"
---

# parted

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `parted /dev/sdb mklabel gpt` creates a GPT partition table; `parted /dev/sdb mkpart
> primary ext4 0% 100%` creates a partition spanning the whole disk. Can run interactively or as
> a single scriptable command line.

## Purpose

`parted` creates and modifies partition tables, GPT-native and scriptable — see
[fdisk, parted, lsblk](../docs/15-storage/fdisk-parted-lsblk.md) for how it compares to `fdisk`.

## Syntax

```
parted [OPTIONS] DEVICE [COMMAND]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `DEVICE` | The block device to operate on (e.g. `/dev/sdb`) | Yes |
| `COMMAND` | A single command to run non-interactively | No — omitting it opens an interactive session |

## Options

| Flag | Meaning |
|---|---|
| `mklabel TYPE` | Create a new partition table (`gpt` or `msdos` for MBR) |
| `mkpart TYPE FS START END` | Create a new partition |
| `print` | Show the current partition table |
| `rm NUMBER` | Remove a partition by number |
| `-s` | Script mode — never prompt for confirmation (for automation) |

## Examples

```
$ sudo parted /dev/sdb print
```
Show the current partition table.

```
$ sudo parted /dev/sdb mklabel gpt
```
Create a new GPT partition table (destroys any existing table).

```
$ sudo parted /dev/sdb mkpart primary ext4 0% 100%
```
Create a single partition spanning the entire disk.

```
$ sudo parted -s /dev/sdb mklabel gpt mkpart primary ext4 0% 100%
```
Do both steps in one non-interactive, scriptable command.

## Expected Output

```
$ sudo parted /dev/sdb print
Model: Virtio Block Device
Disk /dev/sdb: 53.7GB
Partition Table: gpt
Number  Start   End     Size    File system  Name     Flags
 1      1049kB  53.7GB  53.7GB               primary
```

## Exit Status

`0` on success, non-zero on an invalid device or command.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Error: Partition(s) ... are in use` | Target partition is currently mounted | Unmount first |
| Unexpected partition table wiped | `mklabel` run on a disk with existing, needed data | Always `print` first to confirm current state before `mklabel` |
| Alignment warning | Partition boundaries not aligned to the underlying disk's optimal block size | Use percentage-based start/end (as shown in examples) rather than exact byte offsets, letting `parted` align automatically |

## Security Considerations

`mklabel` destroys the existing partition table (and effectively all data) on the target device
immediately, with no confirmation prompt in script mode (`-s`) — double-check the device name
before running scripted partitioning commands.

## Performance Considerations

Negligible — a low-frequency administrative operation.

## Compatibility Notes

`parted` handles both GPT and MBR (`msdos`), but is specifically the more common choice for
GPT and for scripted, non-interactive workflows compared to [`fdisk`](fdisk.md).

## Production Usage

`parted -s` (script mode) is the standard way to include partitioning steps in automated
provisioning scripts, since it never pauses for interactive confirmation.

## Related Commands

- [`fdisk`](fdisk.md) — interactive, historically MBR-focused alternative
- [`lsblk`](lsblk.md) — read-only check to run before and after any partitioning change

## Related Concepts

- [fdisk, parted, lsblk](../docs/15-storage/fdisk-parted-lsblk.md)
