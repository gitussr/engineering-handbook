---
title: "umask"
description: "The setting that decides a new file's default permissions the moment it's created, before anyone runs chmod at all."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/chown-chgrp"]
relatedTopics: ["suid-sgid-sticky-bit"]
relatedCommands: ["umask"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#umask-explained"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/suid-sgid-sticky-bit"
prevTopic: "07-permissions/chown-chgrp"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["umask explained", "umask 022", "default file permissions linux", "umask calculation"]
canonicalUrl: "/docs/permissions/umask"
---

# umask

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `umask` sets which permission bits are subtracted from the maximum defaults (666 for
> files, 777 for directories) every time a new file or directory is created. The common default,
> `022`, is why new files typically land at `644` and new directories at `755` without anyone
> running `chmod`.

## What is it?

`umask` is a per-session (or system-wide) setting that determines a new file or directory's
default permissions at the moment of creation — before any `chmod` command ever runs.

## Why does it exist?

Without a default, every newly created file would need an explicit `chmod` immediately after
creation to have sensible permissions — a tedious and error-prone requirement for something that
should just have safe defaults. `umask` sets that default policy once, and every new file
inherits it automatically.

## Where is it used?

Every time any process creates a new file — a text editor saving a file, a script writing output,
a program creating a log — `umask` shapes its starting permissions, invisibly, unless explicitly
overridden afterward.

## How it works

> 📊 Diagram: a subtraction shown visually — maximum default permissions (666 for files, 777 for
> directories) minus the umask value (e.g. 022) equals the actual default permissions applied
> (644 for files, 755 for directories) — making clear umask is subtracted, not directly the final
> permission value.

`umask` is a value **subtracted** from the maximum default: files start at a maximum of `666`
(no execute by default, even with umask `000`), directories at `777`. The umask value's bits mark
what gets *removed*:

| umask | New file default | New directory default |
|---|---|---|
| `022` | `644` (666 - 022) | `755` (777 - 022) |
| `002` | `664` (666 - 002) | `775` (777 - 002) |
| `077` | `600` (666 - 077) | `700` (777 - 077) |

`umask 022` (the common default) removes write for group and other on new files/directories —
`022` reads as "subtract write from group, subtract write from other." A stricter `umask 077`
removes all group and other access entirely, appropriate for private/sensitive working
directories.

## Real-world example

A team notices new files created by a shared deployment script are ending up world-writable —
tracing it back, the deploy user's `umask` was set to `000` (no restriction at all) instead of a
sane default, meaning every new file inherited maximum permissions. Setting `umask 022` in the
deploy user's shell profile fixed newly created files going forward, without needing to `chmod`
each one after the fact.

## Commands

- [`umask`](../../commands/umask.md) — view or set the current umask

## Production example

```
$ umask
0022
$ touch newfile.txt
$ ls -l newfile.txt
-rw-r--r-- 1 deploy deploy 0 Jul 25 09:00 newfile.txt
```

`644` — exactly `666 - 022` — with zero `chmod` calls involved.

## Do / Don't

| Do | Don't |
|---|---|
| Set `umask` in shell config for consistent defaults across a fleet | Rely on each new file being manually `chmod`'d after creation |
| Use a stricter `umask` (e.g. `077`) for sensitive working directories | Leave `umask 000` set, granting maximum permissions to everything created |
| Check `umask` when new files have unexpectedly loose or tight permissions | Assume file permission issues are always a `chmod` problem, not a `umask` one |

## Common mistakes

- Assuming a file's permissions came from an explicit `chmod` when they actually came from
  `umask` at creation time — a common source of "why does this file have these permissions" 
  confusion.
- Setting an overly permissive `umask` (like `000`) system-wide, silently making every newly
  created file more open than intended.
- Not realizing `umask` only affects files created *after* it's set — it has no effect on
  existing files.

## Best practices

- Set a sane, consistent `umask` (commonly `022`) in shell config or system-wide profile settings
  for predictable defaults.
- Use a stricter `umask` in scripts or sessions handling sensitive data.
- When investigating unexpected file permissions, check `umask` as a possible cause, not just
  recent `chmod` history.

## Exercises

1. Check your current `umask` value.
2. Create a new file and a new directory, and verify their permissions match the expected
   `666`/`777` minus your umask.
3. Temporarily set a stricter `umask` (e.g. `077`), create a file, and observe the difference.

## Quiz

**Q: Does umask directly set a file's permissions, or subtract from a maximum?**
<details><summary>Show answer</summary>
It subtracts from a maximum default (666 for files, 777 for directories) — umask bits mark what
gets removed, not what gets granted.
</details>

**Q: Does changing umask affect files that already exist?**
<details><summary>Show answer</summary>
No — umask only affects files and directories created after the change; existing files keep
whatever permissions they already have.
</details>

## Interview questions

- What does a `umask` of `022` actually do? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `umask` sets default permissions for newly created files/directories, subtracted from a maximum
  (666/777).
- `022` is the common default, producing `644` files and `755` directories.
- `umask` only affects new files going forward, never existing ones.
- Unexpected file permissions are sometimes a `umask` issue, not a missed `chmod`.

## Related topics

- [SUID, SGID, Sticky Bit](suid-sgid-sticky-bit.md)
