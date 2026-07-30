---
title: "fdisk — Create or Modify a Partition Table"
description: "Interactive partition editor, historically MBR-focused, with modern versions supporting GPT too."
relatedConcepts: ["15-storage/fdisk-parted-lsblk"]
relatedCommands: ["parted", "lsblk"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
compatibility: [{"context": "Modern util-linux fdisk", "note": "Supports GPT in addition to its historical MBR focus, but parted remains the more common choice for GPT-heavy or scripted workflows."}]
updatedAt: "2026-07-27"
keywords: ["fdisk examples", "fdisk create partition", "fdisk -l list partitions", "fdisk delete partition"]
canonicalUrl: "/commands/fdisk"
---

# fdisk

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `fdisk /dev/sdb` opens an interactive session to create, delete, or modify
> partitions on that device. `fdisk -l` lists partition tables without entering interactive mode.

## Purpose

`fdisk` creates and modifies partition tables interactively — see
[fdisk, parted, lsblk](../docs/15-storage/fdisk-parted-lsblk.md) for how it compares to `parted`.

## Syntax

```
fdisk [OPTIONS] DEVICE
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `DEVICE` | The block device to operate on (e.g. `/dev/sdb`) | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-l` | List partition tables for all (or a specified) device, non-interactively |

Within the interactive session, single-letter commands control the session: `n` (new partition),
`d` (delete partition), `p` (print the current partition table), `w` (write changes and exit),
`q` (quit without saving).

## Examples

```
$ sudo fdisk -l
```
List partition tables for every device, without entering interactive mode.

```
$ sudo fdisk /dev/sdb
```
Open an interactive session for `/dev/sdb`.

Inside the session:

```
Command (m for help): n
Command (m for help): p
Command (m for help): w
```
Create a new partition (`n`), print the table to review (`p`), then write the changes (`w`).

## Expected Output

```
$ sudo fdisk -l /dev/sdb
Disk /dev/sdb: 50 GiB, 53687091200 bytes, 104857600 sectors
Device     Boot Start       End   Sectors Size Id Type
```

## Exit Status

`0` on success, non-zero on an invalid device or a failed write.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Device or resource busy` on write | The device (or a partition on it) is currently mounted or in use | Unmount first, or reboot if the kernel hasn't re-read the table |
| Changes don't appear to take effect | Kernel hasn't re-read the partition table | Run `partprobe`, or reboot |
| Accidentally deleted the wrong partition | Confirmed a destructive change without reviewing (`p`) first | Quit without writing (`q`) if changes haven't been saved yet — but no undo once written |

## Security Considerations

Changes are only committed on `w` (write) — reviewing with `p` before writing is the safety check
that prevents committing an unintended change; once written, there is no undo.

## Performance Considerations

Negligible — a low-frequency administrative operation, not something run repeatedly.

## Compatibility Notes

Modern `fdisk` supports GPT as well as its historically MBR-focused design; [`parted`](parted.md)
remains the more common choice specifically for GPT-heavy or scripted, non-interactive workflows.

## Production Usage

`fdisk -l` (non-interactive) is commonly used purely for inspection/auditing; the interactive
session is reserved for actual partition changes, always reviewed with `p` before committing with
`w`.

## Related Commands

- [`parted`](parted.md) — GPT-native, scriptable alternative
- [`lsblk`](lsblk.md) — read-only check to run before and after any partitioning change

## Related Concepts

- [fdisk, parted, lsblk](../docs/15-storage/fdisk-parted-lsblk.md)
