---
title: "rwx Explained"
description: "What read, write, and execute actually mean for files versus directories — the same three letters, different consequences depending on the type."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/permission-model"]
relatedTopics: ["chmod-symbolic-and-octal"]
relatedCommands: ["ls"]
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#rwx-on-directories"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/chmod-symbolic-and-octal"
prevTopic: "07-permissions/permission-model"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["rwx permissions explained", "execute permission on directory", "read write execute linux"]
canonicalUrl: "/docs/permissions/rwx-explained"
---

# rwx Explained

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `r` (read), `w` (write), `x` (execute) mean different things for files versus
> directories. On a directory, `x` means "can enter/traverse it" — not "can execute it" — the
> single most common source of rwx confusion.

## What is it?

The three permission types available in every scope (owner/group/other, from
[The Permission Model](permission-model.md)): read, write, and execute — represented as `r`,
`w`, `x` in `ls -l` output.

## Why does it exist?

Reading, modifying, and running/entering something are three genuinely distinct operations that
need independent control — you might want someone to read a script without being able to modify
it, or execute a program without being able to read its source.

## Where is it used?

Every file and directory permission check on every operation — opening a file, writing to it,
running it, or listing/entering a directory.

## How it works

> 📊 Diagram: two rows — "On a file": r = read content, w = modify content, x = run as a program;
> "On a directory": r = list contents, w = create/delete entries inside it, x = enter/traverse
> it — visually split to emphasize the meaning changes based on file type.

| Permission | On a file | On a directory |
|---|---|---|
| `r` | Read the file's content | List the directory's contents (`ls`) |
| `w` | Modify the file's content | Create, delete, or rename entries inside it |
| `x` | Run the file as a program/script | Enter the directory (`cd` into it) or access files inside by path |

The directory `x` behavior is the one that trips people up: without execute permission on a
directory, you can't `cd` into it or access anything inside it by path — **even if you have read
permission on the directory itself**, and even if you have full permissions on a file inside it.
Execute on a directory is the gatekeeper for entering it at all.

## Real-world example

An engineer sets a directory to `r--r--r--` (read-only, no execute) intending to let a team browse
its contents, but the team reports they can't open any files inside it — because without `x` on
the directory, nothing inside is reachable by path, regardless of read permission on the
directory or permissions on the files themselves. The fix is adding `x`, not touching the files
at all.

## Commands

- [`ls`](../../commands/ls.md) — `ls -l` reveals the full rwx string for owner/group/other

## Production example

```
$ ls -ld /opt/shared-data
d r--r--r--  1 root root 4096 Jul 25 09:00 /opt/shared-data
$ cd /opt/shared-data
bash: cd: /opt/shared-data: Permission denied
```

Read-only, no execute — exactly the broken configuration from the real-world example above,
reproduced.

## Do / Don't

| Do | Don't |
|---|---|
| Grant `x` on any directory you need to enter or traverse into | Assume read permission on a directory is enough to access what's inside it |
| Remember `x` on a file means "runnable," not "readable" | Confuse execute permission with read permission when granting script access |

## Common mistakes

- Granting read-only directory permissions and being confused when nothing inside is accessible —
  missing `x`, the actual gatekeeper for entering a directory.
- Assuming file execute permission implies read permission (or vice versa) — they're fully
  independent; a file can be executable without being readable, and vice versa.
- Setting `x` on a regular text file by habit or mistake, which has no real effect (it's not an
  executable program) but can cause confusing tooling behavior expecting scripts to be marked
  executable deliberately.

## Best practices

- When granting directory access, always check that `x` is included alongside `r` if the
  directory's contents need to be reachable, not just listable.
- Set `x` deliberately on scripts you intend to run directly (`./script.sh`), not as a reflexive
  default on every file.

## Exercises

1. Create a directory with read but no execute permission and try to `cd` into it.
2. Create a script file, make it executable with the appropriate permission, and run it directly.
3. Explain in one sentence why directory `x` is different from file `x`.

## Quiz

**Q: What does execute permission mean on a directory?**
<details><summary>Show answer</summary>
The ability to enter/traverse the directory (`cd` into it) and access files inside it by path —
not "run the directory," which isn't a meaningful operation.
</details>

**Q: Can a file be executable without being readable?**
<details><summary>Show answer</summary>
Yes — read and execute are independent permissions; a binary can be set executable without read
permission, though this is an unusual, specific configuration.
</details>

## Interview questions

- Why might a user with read permission on a directory still be unable to access files inside it? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `r`, `w`, `x` mean different things for files versus directories.
- Directory `x` is the gatekeeper for entering/traversing it — the most commonly confused rwx
  behavior.
- Read and execute are fully independent permissions, on both files and directories.

## Related topics

- [chmod: Symbolic and Octal](chmod-symbolic-and-octal.md)
