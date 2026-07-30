---
title: "chown and chgrp"
description: "Changing who owns a file and which group owns it — the other half of access control, separate from permission bits."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/chmod-symbolic-and-octal"]
relatedTopics: ["umask"]
relatedCommands: ["chown", "chgrp"]
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#chown-vs-chmod"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/umask"
prevTopic: "07-permissions/chmod-symbolic-and-octal"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["chown command", "chgrp command", "change file owner linux", "chown recursive"]
canonicalUrl: "/docs/permissions/chown-chgrp"
---

# chown and chgrp

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `chmod` controls *what* permissions apply; `chown`/`chgrp` control *who* they apply
> to. `chown user:group file` changes both owner and group in one command — the form you'll use
> most.

## What is it?

`chown` ("change owner") changes a file's owning user (and optionally group in the same command);
`chgrp` ("change group") changes just the owning group. Both act on the ownership half of the
permission model from [The Permission Model](permission-model.md) — `chmod` never touches
ownership, only the permission bits themselves.

## Why does it exist?

Permission bits (`chmod`) only matter relative to who owns the file — `rwx` for "owner" is
meaningless without knowing who the owner is. `chown`/`chgrp` are how that owner and group
assignment itself gets set or changed, independent of what permissions are granted to each scope.

## Where is it used?

- Fixing files with the wrong owner (e.g. files extracted from an archive as root, needing to be
  owned by a service's actual runtime user)
- Setting up shared directories with the correct group ownership
- Deployment scripts that need artifacts owned by a specific service account, not whoever ran the
  deployment

## How it works

> 📊 Diagram: a file's ownership metadata shown as two labeled fields — "Owner: deploy" and
> "Group: developers" — with `chown` and `chgrp` shown as arrows pointing at each field
> respectively, and `chown user:group` shown as a single arrow that sets both at once.

```
chown newowner file            # change owner only
chown newowner:newgroup file   # change owner and group together
chown :newgroup file           # change group only (via chown, alternative to chgrp)
chgrp newgroup file            # change group only (dedicated command)
```

`chown` alone requires root/sudo privileges (a regular user can't give away files they own to
someone else) — this is a deliberate security boundary, not an oversight.

## Real-world example

A deployment process extracts an application archive as root, leaving every file owned by root.
The application actually runs as a dedicated, unprivileged `appuser` account — without `chown -R
appuser:appuser /opt/app`, the application can't read its own files at runtime, a very common
"works during deployment, fails at startup" class of bug.

## Commands

- [`chown`](../../commands/chown.md) — full syntax and examples
- [`chgrp`](../../commands/chgrp.md) — full syntax and examples

## Production example

```
$ ls -l app.py
-rw-r--r-- 1 root root 1024 Jul 25 09:00 app.py
$ sudo chown appuser:appuser app.py
$ ls -l app.py
-rw-r--r-- 1 appuser appuser 1024 Jul 25 09:00 app.py
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `chown user:group` to set both in one command | Run `chown` then `chgrp` separately when one command does both |
| Fix ownership issues with `chown`, not by loosening permissions with `chmod` | Reach for `chmod 777` when the real problem is wrong ownership |
| Use `-R` deliberately, understanding it touches everything inside a directory | Run `chown -R` without confirming the scope is exactly what's intended |

## Common mistakes

- Trying to fix a permission-denied error with `chmod` alone when the actual problem is
  ownership — no permission setting fixes an access problem caused by the wrong owner.
- Forgetting `chown` (unlike `chmod`) generally requires root/sudo privileges, and being confused
  by a permission-denied error on the `chown` command itself.
- Running `chown -R` on a directory without confirming the recursive scope is actually intended —
  it changes ownership of everything inside, no exceptions.

## Best practices

- Diagnose whether a permission-denied error is actually an ownership problem before reaching for
  `chmod` — `ls -l` shows both, check both.
- Use the combined `chown user:group` form rather than two separate commands when both need to
  change.
- Be as deliberate with `chown -R`'s scope as you would be with `rm -rf`'s — both act on
  everything inside a directory with no per-file confirmation.

## Exercises

1. Check a file's current owner and group with `ls -l`.
2. Change both owner and group in one `chown user:group` command (requires appropriate
   privileges).
3. Explain in one sentence why `chown` typically requires root/sudo while `chmod` (on your own
   files) doesn't.

## Quiz

**Q: What's the difference between what `chmod` and `chown` control?**
<details><summary>Show answer</summary>
`chmod` controls what permissions apply (read/write/execute per scope); `chown`/`chgrp` control
who the owner and group actually are. They're independent, complementary controls.
</details>

**Q: Why might `chown` fail with "Operation not permitted" for a regular user?**
<details><summary>Show answer</summary>
Changing a file's owner typically requires root/sudo privileges — a regular user generally can't
give away ownership of files they own.
</details>

## Interview questions

- A file has correct permission bits but a user still can't access it — what would you check
  next? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `chmod` controls permission bits; `chown`/`chgrp` control who owns the file and group.
- `chown user:group file` sets both in one command.
- `chown` generally requires root/sudo privileges — a real security boundary.
- Ownership problems need `chown`, not a `chmod` workaround.

## Related topics

- [umask](umask.md)
- [Module 08: Users](../08-users/index.md)
