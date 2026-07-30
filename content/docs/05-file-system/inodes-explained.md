---
title: "Inodes Explained"
description: "What an inode actually stores, why a filename isn't part of it, and how a disk can run out of inodes with free space still available."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["05-file-system/proc-and-sys-virtual-filesystems"]
relatedTopics: ["symbolic-vs-hard-links"]
relatedCommands: ["stat"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#inodes"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "05-file-system/symbolic-vs-hard-links"
prevTopic: "05-file-system/proc-and-sys-virtual-filesystems"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["inode explained", "no space left on device inodes full", "inode vs filename"]
canonicalUrl: "/docs/file-system/inodes-explained"
---

# Inodes Explained

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** An inode stores a file's metadata (permissions, owner, size, timestamps, and pointers
> to its actual data blocks) — but not its filename. A directory is just a list mapping names to
> inode numbers. A filesystem can run out of inodes (and refuse to create new files) even with
> free disk space, if it has millions of tiny files.

## What is it?

An inode ("index node") is the data structure that stores everything about a file except its
name: permissions, owner, group, size, timestamps, and pointers to where its actual data lives on
disk. Every file on a filesystem has exactly one inode.

## Why does it exist?

Separating a file's metadata (the inode) from its name (stored in the containing directory) is
what makes hard links possible — the same underlying file (inode) can have multiple names
(directory entries) pointing to it, covered fully in
[Symbolic vs Hard Links](symbolic-vs-hard-links.md). It also explains a real, if surprising,
production failure mode: running out of inodes independently of running out of disk space.

## Where is it used? / What problem does it solve?

Every file, on every filesystem, has an inode — this isn't an advanced feature you opt into, it's
the fundamental structure ext4/xfs/btrfs are built on. Understanding it explains behavior that
otherwise looks like a bug: "no space left on device" with `df` showing plenty of free space.

## How it works

> 📊 Diagram: a directory shown as a simple table mapping filenames to inode numbers (`app.log` →
> `inode 4821`, `config.txt` → `inode 4822`), with each inode number pointing to a separate box
> containing that file's actual metadata and data block pointers — making clear the filename lives
> in the directory, not the inode.

- A **directory** is really just a list of (filename → inode number) pairs.
- An **inode** stores: permissions, owner/group, size, timestamps, and pointers to the file's
  actual data blocks on disk — everything except the name.
- A filesystem has a **fixed number of inodes**, decided when it's formatted — this is why a
  filesystem with an enormous number of very small files can exhaust its inode count while still
  having plenty of free disk space in bytes.
- `df -i` shows inode usage separately from `df -h`'s byte usage — two different resources that
  can each run out independently.

## Real-world example

A server hosting a caching layer that creates millions of tiny temporary files hits "no space
left on device" errors — but `df -h` shows 40% free disk space. The actual problem, revealed by
`df -i`, is inode exhaustion: the filesystem physically cannot track any more files, regardless
of how many free bytes remain. This is a real, recurring production incident pattern, not a rare
edge case.

## Commands

- [`stat`](../../commands/stat.md) — inspect a file's full inode metadata

## Production example

```
$ df -i /
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
/dev/xvda1     1310720 1310720      0  100% /
$ df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G   8.2G   11G  43% /
```

100% inode usage with only 43% disk space used — exactly the confusing-looking failure this page
exists to explain.

## Do / Don't

| Do | Don't |
|---|---|
| Check `df -i` alongside `df -h` when diagnosing "no space" errors | Assume "no space left on device" always means out of disk bytes |
| Use `stat` to inspect a file's full inode metadata | Assume a file's "size" on disk tells you everything `stat` would |
| Consider inode count when a workload creates huge numbers of small files | Format a filesystem without considering expected file count for that use case |

## Common mistakes

- Diagnosing "no space left on device" purely from `df -h` and missing that it's actually inode
  exhaustion, visible only via `df -i`.
- Assuming a filename is stored inside the inode — it's stored in the containing directory's own
  listing, not the inode itself.
- Not considering expected file count (not just total size) when provisioning a filesystem for a
  workload that creates many small files.

## Best practices

- Always check `df -i` as a standard part of diagnosing "no space" errors, not just `df -h`.
- For workloads expected to create huge numbers of small files, consider inode count during
  filesystem planning, not just total capacity in bytes.
- Use `stat` when you need full metadata detail beyond what `ls -l` shows.

## Exercises

1. Run `df -i` and `df -h` on the same filesystem and compare inode usage to byte usage.
2. Run `stat` on any file and identify its inode number.
3. Explain in one sentence why a filesystem with millions of tiny files could run out of space
   before running out of bytes.

## Quiz

**Q: Is a file's name stored in its inode?**
<details><summary>Show answer</summary>
No — the name is stored in the containing directory's listing, which maps names to inode numbers.
The inode itself stores everything else (permissions, size, timestamps, data pointers).
</details>

**Q: Can a filesystem run out of inodes while still having free disk space?**
<details><summary>Show answer</summary>
Yes — inode count is a separate, fixed resource from disk space in bytes. A filesystem with an
extremely large number of small files can exhaust inodes long before it exhausts bytes.
</details>

## Interview questions

- Why might "no space left on device" occur even when `df -h` shows free space? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- An inode stores a file's metadata and data pointers — everything except its name.
- A directory maps filenames to inode numbers; that mapping is where the name actually lives.
- Inode count is fixed at filesystem creation and can be exhausted independently of disk space.
- `df -i` checks inode usage; `df -h` checks byte usage — check both when diagnosing "no space."

## Related topics

- [Symbolic vs Hard Links](symbolic-vs-hard-links.md)
