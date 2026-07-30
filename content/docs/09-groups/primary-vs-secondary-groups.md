---
title: "Primary vs Secondary Groups"
description: "Every user has exactly one primary group, used for new file ownership by default, plus any number of secondary groups for additional access."
module: "09-groups"
moduleTitle: "Groups"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["09-groups/groups-overview"]
relatedTopics: ["groupadd-groupmod-groupdel"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#primary-vs-secondary-group"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "09-groups/groupadd-groupmod-groupdel"
prevTopic: "09-groups/groups-overview"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["primary group linux", "secondary group linux", "supplementary group", "default group new files"]
canonicalUrl: "/docs/groups/primary-vs-secondary-groups"
---

# Primary vs Secondary Groups

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** Every user has exactly one primary group (used as the default group owner of files
> they create) and any number of secondary/supplementary groups (extra access, without changing
> what new files default to). Confusing the two is a common, confusing source of "why is this
> file's group ownership wrong" questions.

## What is it?

A user's **primary group** is the single group assigned when their account is created — used by
default as the group owner of any file they create. **Secondary (supplementary) groups** are
additional group memberships granting extra access, without affecting new-file group ownership at
all.

## Why does it exist?

A file needs exactly one group owner at creation time (Module 07's permission model), so there
has to be a default — that's the primary group's job. But a user often needs access to more than
one group's resources simultaneously (their team's group, plus a shared-tooling group, plus a
project-specific group) — that's what secondary groups are for, without disturbing what happens
by default when they create a new file.

## Where is it used?

- Every file creation event uses the creating user's primary group as the default group owner
  (unless [SGID](../07-permissions/suid-sgid-sticky-bit.md) on the containing directory overrides
  this, as covered in Module 07)
- Secondary groups grant ongoing access to additional shared resources without changing that
  default

## How it works

> 📊 Diagram: one user icon with one solid-line connection to their primary group ("developers")
> and several dashed-line connections to secondary groups ("docker," "shared-tools") — the solid
> line specifically labeled "used for new file ownership by default."

- **Primary group**: set at account creation (`useradd`'s `-g` flag, or a distro default —
  often a same-named group created automatically per user, sometimes the whole default is a
  shared `users` group, distro-dependent). Shown in `/etc/passwd`'s GID field.
- **Secondary/supplementary groups**: any number of additional memberships, set via `usermod -aG`
  (fully covered in [Module 08](../08-users/useradd-usermod-userdel.md)) or at creation via
  `useradd -G`. Listed in `/etc/group`, not in the user's `/etc/passwd` entry.

`id username` shows both clearly: `uid=1000(deploy) gid=1000(deploy) groups=1000(deploy),
27(sudo),999(docker)` — the `gid=` is the primary group, everything in `groups=` beyond that is
secondary.

## Real-world example

A developer's primary group is their own personal group (`deploy`, matching their username, a
common default convention) — so files they create in their home directory default to being
group-owned by just them. They're also a secondary member of `developers` (shared project access)
and `docker` (permission to use the Docker daemon). Creating a new file in their home directory
doesn't touch either secondary group's ownership at all — only the primary group applies by
default.

## Commands

No command example on this page — `id` (already introduced conceptually in Module 08) is used
for inspection; full group-membership commands are covered in
[Adding Users to Groups](adding-users-to-groups.md).

## Production example

```
$ id deploy
uid=1000(deploy) gid=1000(deploy) groups=1000(deploy),27(sudo),999(docker)
$ touch newfile.txt
$ ls -l newfile.txt
-rw-r--r-- 1 deploy deploy 0 Jul 25 09:00 newfile.txt
```

`newfile.txt`'s group owner is `deploy` — the primary group — even though the user also belongs
to `sudo` and `docker`.

## Do / Don't

| Do | Don't |
|---|---|
| Check `id`'s `gid=` field specifically when debugging default file ownership | Assume any of a user's group memberships could be the default — only the primary group is |
| Use secondary groups for additional access that shouldn't change default file ownership | Change a user's primary group just to grant them access to another group's resources |

## Common mistakes

- Assuming any group a user belongs to could explain a new file's group ownership — only the
  primary group does, by default.
- Changing a user's primary group (via `usermod -g`) when a secondary group membership (`usermod
  -aG`) was actually what was needed, unintentionally changing their default file ownership
  behavior as a side effect.
- Not checking `id`'s `gid=` vs `groups=` distinction when troubleshooting unexpected group
  ownership on new files.

## Best practices

- Use secondary groups (`-aG`) for granting access to additional resources — reserve primary
  group changes (`-g`) for genuine changes to what a user's new files should default to.
- When debugging unexpected file group ownership, check the creating user's primary group
  specifically (`id -gn username`), not their full group list.

## Exercises

1. Run `id` and identify your own primary group versus your secondary groups.
2. Create a new file and confirm its group ownership matches your primary group.
3. Explain in one sentence why changing a user's primary group is a different operation from
   adding them to a secondary group.

## Quiz

**Q: Which group determines a newly created file's default group ownership?**
<details><summary>Show answer</summary>
The creating user's primary group — not any of their secondary/supplementary groups.
</details>

**Q: In `id`'s output, which field shows the primary group?**
<details><summary>Show answer</summary>
`gid=` — everything listed in `groups=` beyond that first one represents secondary group
memberships.
</details>

## Interview questions

- What determines a new file's group ownership when it's created? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Every user has exactly one primary group and any number of secondary groups.
- Only the primary group affects new file group ownership by default.
- `id`'s `gid=` field is the primary group; `groups=` lists everything including secondary
  memberships.
- Use secondary groups for additional access; reserve primary group changes for a genuine default
  change.

## Related topics

- [groupadd, groupmod, groupdel](groupadd-groupmod-groupdel.md)
