---
title: "chmod: Symbolic and Octal"
description: "Two notations for setting permissions with chmod — symbolic (u+x) for targeted changes, octal (755) for setting everything at once."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/rwx-explained"]
relatedTopics: ["chown-chgrp"]
relatedCommands: ["chmod"]
careerRelevance: ["devops", "linux-administrator", "backend", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#chmod-755-vs-644"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/chown-chgrp"
prevTopic: "07-permissions/rwx-explained"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["chmod 755", "chmod 644", "chmod symbolic notation", "chmod octal explained"]
canonicalUrl: "/docs/permissions/chmod-symbolic-and-octal"
---

# chmod: Symbolic and Octal

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Cybersecurity

> **TL;DR:** `chmod u+x file` (symbolic) changes one thing without disturbing the rest. `chmod
> 755 file` (octal) sets all three scopes at once. `755` = owner rwx, group r-x, other r-x — the
> single most common permission setting in daily use, worth memorizing outright.

## What is it?

`chmod` ("change mode") sets a file or directory's permissions, in one of two notations:
**symbolic** (readable, targeted — `u+x` adds execute for the owner) or **octal** (compact,
sets all three scopes at once — `755`).

## Why does it exist?

Two notations exist because they suit different needs: symbolic is easier to reason about when
changing just one thing ("add execute for the owner, leave everything else alone"); octal is
faster once you're fluent, and is what you'll see in scripts, documentation, and interviews
constantly, since it fully specifies a file's permissions in exactly three digits.

## Where is it used?

Everywhere permissions need to change: making a script executable, locking down a config file,
fixing a "permission denied" error, setting up shared directory access.

## How it works

> 📊 Diagram: a single permission string `rwxr-xr-x` broken into its three scopes with two
> parallel translations shown underneath — symbolic (`u=rwx,g=rx,o=rx`) and octal (`755`) — so
> the two notations are visibly two representations of the exact same permission state.

**Octal**: each scope's `rwx` is a 3-bit binary value, converted to one digit — `r`=4, `w`=2,
`x`=1, summed. `rwx` = 4+2+1 = `7`. `r-x` = 4+0+1 = `5`. `rw-` = 4+2+0 = `6`. Three digits, one
per scope, in owner-group-other order:

| Octal | Meaning |
|---|---|
| `755` | Owner: rwx, Group: r-x, Other: r-x — standard for executable scripts/programs |
| `644` | Owner: rw-, Group: r--, Other: r-- — standard for regular files (configs, documents) |
| `700` | Owner: rwx, Group: ---, Other: --- — private to the owner only |
| `600` | Owner: rw-, Group: ---, Other: --- — private file, no execute |

**Symbolic**: `u`(user/owner)/`g`(group)/`o`(other)/`a`(all), then `+`/`-`/`=`, then the
permission letters:

```
chmod u+x file       # add execute for owner
chmod g-w file        # remove write for group
chmod o=r file         # set other to exactly read-only
chmod a+r file          # add read for everyone
```

## Real-world example

An engineer downloads a deployment script and tries to run it, getting "permission denied" —
`chmod +x deploy.sh` (symbolic, shorthand for `a+x` in most implementations, though `chmod u+x`
is more precise if only the owner needs it) fixes it in one command, the single most common
`chmod` use case in daily work. Separately, setting a new config file to `644` ensures it's
readable by the service that needs it and by admins, but not writable by anyone except the owner.

## Commands

- [`chmod`](../../commands/chmod.md) — full syntax and examples

## Production example

```
$ ls -l deploy.sh
-rw-r--r-- 1 deploy deploy 220 Jul 25 09:00 deploy.sh
$ chmod +x deploy.sh
$ ls -l deploy.sh
-rwxr--r-- 1 deploy deploy 220 Jul 25 09:00 deploy.sh
$ chmod 644 config.yaml
```

## Do / Don't

| Do | Don't |
|---|---|
| Memorize `755` (executables) and `644` (regular files) as defaults | Reach for `777` as a quick fix for a permission error |
| Use symbolic notation for a single targeted change | Use octal when you only want to change one bit and risk resetting the others incorrectly |
| Double-check octal digit order (owner-group-other) | Mix up which digit controls which scope |

## Common mistakes

- Using `chmod 777` (full access for everyone) as a lazy fix for a permission error — this is a
  real, common, and serious security anti-pattern, not just sloppy; it should almost never be the
  actual fix.
- Miscounting octal digits — forgetting `rwx` sums as 4+2+1=7 and guessing instead of calculating.
- Using octal to change one thing and accidentally resetting other permissions that weren't
  meant to change, because octal always sets the complete state, not an incremental change.

## Best practices

- Default to `755` for executables and `644` for regular files unless there's a specific reason
  to deviate.
- Never use `777` as a fix — it almost always indicates the real problem (often ownership, not
  permission bits) hasn't actually been diagnosed.
- Use symbolic notation (`u+x`) when you want to change one thing without touching the rest;
  octal when you want to set the complete, exact state.

## Exercises

1. Create a script, confirm it's not executable, then make it executable with symbolic notation.
2. Set a file to `644` using octal notation and verify with `ls -l`.
3. Explain in one sentence why `chmod 777` is considered a security anti-pattern rather than a
   legitimate fix.

## Quiz

**Q: What does `755` mean in octal permission notation?**
<details><summary>Show answer</summary>
Owner: read+write+execute (7), Group: read+execute (5), Other: read+execute (5) — the standard
setting for executable scripts and programs.
</details>

**Q: Why is `chmod 777` generally considered bad practice?**
<details><summary>Show answer</summary>
It grants full read/write/execute access to everyone, which almost never reflects genuinely
intended access and usually masks the real underlying problem (often ownership, not permissions).
</details>

## Interview questions

- What's the difference between `chmod 755` and `chmod 644`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Octal (`755`, `644`) sets all three scopes at once; symbolic (`u+x`) changes one thing at a
  time.
- `755` for executables, `644` for regular files — memorize these as defaults.
- `chmod 777` is a security anti-pattern, not a legitimate quick fix.
- Octal digit order is always owner-group-other.

## Related topics

- [chown and chgrp](chown-chgrp.md)
- Cheat sheet: [Permissions](../../cheatsheets/permissions.md)
