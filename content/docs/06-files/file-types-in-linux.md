---
title: "File Types in Linux"
description: "Regular files, directories, symlinks, and device files — how to tell them apart and why ls -l's first character matters."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["05-file-system/symbolic-vs-hard-links"]
relatedTopics: ["creating-copying-moving-removing-files"]
relatedCommands: ["ls"]
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#file-types"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/creating-copying-moving-removing-files"
prevTopic: "05-file-system/symbolic-vs-hard-links"
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["linux file types", "ls -l first character", "device file linux", "regular file vs directory"]
canonicalUrl: "/docs/files/file-types-in-linux"
---

# File Types in Linux

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** Under "everything is a file," Linux has several distinct file types — regular files,
> directories, symlinks, and device files among them — all visible at a glance from the first
> character of `ls -l` output.

## What is it?

Linux categorizes every filesystem entry into a type: regular file, directory, symbolic link
(covered in [Module 05](../05-file-system/symbolic-vs-hard-links.md)), and special device files
(character and block devices, primarily under `/dev`), among a few rarer types (named pipes,
sockets).

## Why does it exist?

"Everything is a file" ([Module 01](../01-linux-basics/linux-philosophy-and-fhs.md)) only works
as a practical principle because the type of each file tells programs how to interact with it
correctly — a directory is traversed differently than a regular file is read, and a device file
represents hardware rather than stored data at all.

## Where is it used?

Every `ls -l`, every script that needs to check "is this actually a file before I try to read
it," and every troubleshooting session where "why can't I read this" turns out to be "this isn't
a regular file at all."

## How it works

> 📊 Diagram: a legend mapping `ls -l`'s first character to file type — `-` regular file, `d`
> directory, `l` symbolic link, `c` character device, `b` block device, `p` named pipe, `s`
> socket — with one example file of each type shown.

| First character | Type |
|---|---|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device (e.g. a terminal, `/dev/tty`) |
| `b` | Block device (e.g. a disk, `/dev/sda`) |
| `p` | Named pipe (FIFO) |
| `s` | Socket |

Regular files are what you interact with constantly — text, binaries, scripts, images. Device
files under `/dev` are how Linux represents hardware as part of the same filesystem, an
extension of "everything is a file" into hardware access itself.

## Real-world example

A script fails with a confusing error trying to read what the author assumed was a regular log
file. `ls -l` reveals the first character is `d` — it's actually a directory, not a file, because
a previous deployment step created a directory instead of writing the expected log file. The
type mismatch, not file permissions or content, was the actual bug.

## Commands

- [`ls`](../../commands/ls.md) — `ls -l` reveals file type via its first character

## Production example

```
$ ls -l /dev/sda /etc/passwd /home
brw-rw---- 1 root disk    8,   0 Jul 25 08:00 /dev/sda
-rw-r--r-- 1 root root  2210 Jul 25 08:00 /etc/passwd
drwxr-xr-x 1 root root  4096 Jul 25 08:00 /home
```

Three different types (`b`, `-`, `d`), visible in the same command's output without needing
anything extra.

## Do / Don't

| Do | Don't |
|---|---|
| Check `ls -l`'s first character when something behaves unexpectedly | Assume every path in a directory listing is a regular file |
| Use `[ -f file ]` in scripts to confirm a regular file before acting on it | Skip type checks in scripts that assume file type without verifying |

## Common mistakes

- Assuming a path is a regular file without checking, then getting a confusing error when it's
  actually a directory or device file.
- Not knowing device files exist as part of the normal filesystem (`/dev`), and being surprised
  they show up in directory listings.
- Writing scripts that don't verify file type before acting on a path, leading to unclear failures
  further down the line instead of a clean early check.

## Best practices

- When debugging an unexpected file-related error, check `ls -l`'s first character before
  assuming permissions or content are the problem.
- In scripts, explicitly test file type (`[ -f file ]`, `[ -d dir ]` — full syntax in
  [Module 18: Shell Scripting](../18-shell-scripting/index.md)) before acting on a path.

## Exercises

1. Run `ls -l /dev` and identify at least one character device and one block device.
2. Run `ls -l` on your home directory and identify the type of each entry.
3. Explain in one sentence why device files being "just files" matters for the Unix philosophy.

## Quiz

**Q: What does a `d` as the first character of `ls -l` output mean?**
<details><summary>Show answer</summary>
The entry is a directory, not a regular file.
</details>

**Q: Are device files (like `/dev/sda`) stored data the same way a regular file is?**
<details><summary>Show answer</summary>
No — they represent hardware access points; reading/writing them interacts with a device driver,
not stored bytes on disk the way a regular file's content is.
</details>

## Interview questions

- How would you determine a file's type from the command line? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `ls -l`'s first character reveals file type: `-` regular, `d` directory, `l` symlink, `b`/`c`
  device files.
- Device files under `/dev` are a direct extension of "everything is a file" into hardware access.
- Type mismatches (expecting a file, getting a directory) are a real, common source of confusing
  script failures.

## Related topics

- [Module 05: File System](../05-file-system/index.md)
- [Creating, Copying, Moving, Removing Files](creating-copying-moving-removing-files.md)
