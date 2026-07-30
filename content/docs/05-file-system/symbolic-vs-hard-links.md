---
title: "Symbolic vs Hard Links"
description: "Two different ways to give a file multiple names, why hard links can't cross filesystems, and the classic broken-symlink-after-move mistake."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["05-file-system/inodes-explained"]
relatedTopics: []
relatedCommands: ["ln"]
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#symlink-vs-hardlink"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/file-types-in-linux"
prevTopic: "05-file-system/inodes-explained"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["symbolic link vs hard link", "ln command", "ln -s", "broken symlink"]
canonicalUrl: "/docs/file-system/symbolic-vs-hard-links"
---

# Symbolic vs Hard Links

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** A hard link is a second name pointing at the exact same inode — indistinguishable
> from the "original," can't cross filesystems, can't link directories. A symbolic link is a
> separate file that just contains a path to another file — can cross filesystems and link
> directories, but breaks if the target moves or is deleted.

## What is it?

Both are ways to give a file more than one name/location, but they work at fundamentally
different levels, directly following from how inodes work
([Inodes Explained](inodes-explained.md)): a hard link is another directory entry pointing to
the same inode; a symbolic link is its own separate file (with its own inode) whose content is
just a path string pointing at another file.

## Why does it exist?

Sometimes you need one file accessible from two locations — a shared config, a versioned binary
with a stable "current" name, a compatibility path for a renamed tool. Two different mechanisms
exist because they make different tradeoffs: hard links are more robust (no "broken link" state
possible) but more limited (same filesystem only, files only); symbolic links are more flexible
(cross filesystems, can link directories) but can break if the target is removed or moved.

## Where is it used?

- Symbolic links: version-independent paths (`/usr/bin/python3` → `python3.11`), config file
  shortcuts, the FHS-mentioned `/bin` → `/usr/bin` symlink from the unified `/usr` layout
- Hard links: less common day to day, but used where true redundancy without extra disk space
  matters (some backup tools use hard links for space-efficient snapshots)

## How it works

> 📊 Diagram: two scenarios side by side — "Hard link": two directory entries (`app` and
> `app-backup`) both pointing directly to the same inode number, so deleting either name leaves
> the data intact under the other. "Symbolic link": one directory entry (`app-link`) pointing to
> a separate small file whose content is the text path `/path/to/app`, which breaks if `/path/to/app`
> is removed.

| | Hard link | Symbolic link |
|---|---|---|
| What it is | A second directory entry pointing to the same inode | A separate file containing a path string |
| Crosses filesystems? | No | Yes |
| Can link a directory? | No (with rare exceptions) | Yes |
| Breaks if target is deleted/moved? | No — data persists under any remaining link | Yes — becomes a "broken" or "dangling" link |
| Command | `ln target linkname` | `ln -s target linkname` |

A hard link is genuinely indistinguishable from the "original" file — there is no original,
just multiple names for the same inode, all equally valid.

## Real-world example

A deployment process symlinks `/opt/app/current` to `/opt/app/releases/v2.4.1`, so the running
application always references a stable path (`current`) regardless of which version is actually
deployed. Rolling back is just repointing the symlink to the previous release directory — instant,
with zero file copying, and exactly the kind of flexibility a hard link couldn't provide since it
can't be "repointed" the way a symlink can (a hard link IS the file, not a pointer to it).

## Commands

- [`ln`](../../commands/ln.md) — create hard and symbolic links

## Production example

```
$ ln -s /opt/app/releases/v2.4.1 /opt/app/current
$ ls -l /opt/app/current
lrwxrwxrwx 1 deploy deploy 30 Jul 25 10:00 current -> /opt/app/releases/v2.4.1
$ mv /opt/app/releases/v2.4.1 /opt/app/releases/v2.4.1-old
$ ls /opt/app/current
ls: cannot access '/opt/app/current': No such file or directory
```

The broken symlink after the move is the classic gotcha this page exists to prevent — moving or
renaming a symlink's target breaks it, since the symlink only stores a path, not a live
connection to the file itself.

## Do / Don't

| Do | Don't |
|---|---|
| Use symbolic links for cross-filesystem or directory linking | Try to hard-link a directory or cross filesystems |
| Use `ls -l` to spot symlinks (shown with `->`) before assuming a path is a real file | Assume every path in a directory listing is a regular file |
| Update or recreate a symlink immediately after moving its target | Move a symlink's target without checking what still points to it |

## Common mistakes

- Moving or deleting a symlink's target and leaving a "dangling" symlink behind, causing confusing
  "no such file" errors from something that looks like it should exist.
- Trying to hard-link across filesystems (or to a directory) and being confused by the resulting
  error — both are structural limitations of hard links, not bugs.
- Confusing a symlink for the real file it points to when checking permissions — a symlink has its
  own permissions (usually `lrwxrwxrwx`, essentially irrelevant) separate from its target's actual
  permissions, which are what really apply when the file is accessed.

## Best practices

- Prefer symbolic links for the common "stable pointer to a versioned thing" pattern (deployments,
  compatibility paths).
- After moving or renaming anything that might be a symlink target, check for and fix any now-
  broken symlinks pointing at it.
- Use `ls -l` (which shows the `->` target) to quickly identify symlinks versus regular files
  during investigation.

## Exercises

1. Create a symbolic link to a file, then move the original and observe the broken link.
2. Create a hard link to a file, delete the original name, and confirm the data still exists under
   the hard link's name.
3. Explain in one sentence why a hard link can't cross filesystems but a symbolic link can.

## Quiz

**Q: What happens to a hard link if the "original" file is deleted?**
<details><summary>Show answer</summary>
Nothing — the data persists under the hard link's name, since both names pointed equally to the
same inode. There's no real "original" to lose.
</details>

**Q: What happens to a symbolic link if its target is deleted or moved?**
<details><summary>Show answer</summary>
It breaks — becomes a "dangling" symlink that points to a path that no longer resolves to
anything.
</details>

## Interview questions

- What's the difference between a hard link and a symbolic link? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A hard link is another name for the same inode; a symbolic link is a separate file containing a
  path.
- Hard links can't cross filesystems or link directories; symbolic links can do both but break if
  the target moves.
- The "stable pointer to a versioned release" deployment pattern relies specifically on symbolic
  links' repointability.
- `ls -l` shows symlinks clearly via their `->` target notation.

## Related topics

- [Inodes Explained](inodes-explained.md)
- [Module 06: Files](../06-files/index.md)
