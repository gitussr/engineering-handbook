---
title: "The /etc/group File"
description: "Where group membership actually lives — reading its fields, and why it only lists secondary members, not primary ones."
module: "09-groups"
moduleTitle: "Groups"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["09-groups/groupadd-groupmod-groupdel"]
relatedTopics: ["adding-users-to-groups"]
relatedCommands: ["cat"]
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#etc-group-format"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "group(5) man page", "url": "https://man7.org/linux/man-pages/man5/group.5.html"}]
nextTopic: "09-groups/adding-users-to-groups"
prevTopic: "09-groups/groupadd-groupmod-groupdel"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["etc group file format", "etc group fields explained", "group file vs passwd file"]
canonicalUrl: "/docs/groups/etc-group-file"
---

# The /etc/group File

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `/etc/group` lists every group and its **secondary** members — a user's primary
> group membership doesn't appear in the member list here at all, only in their `/etc/passwd`
> entry's GID field. This is the single most common source of "why isn't this user listed" confusion.

## What is it?

`/etc/group` is the file defining every group on the system, one line per group, parallel in
structure to [`/etc/passwd`](../08-users/etc-passwd-and-etc-shadow.md) but for groups instead of
users.

## Why does it exist?

Every group needs a definition somewhere — a name, a GID, and a member list. `/etc/group` is
that definition, read by every tool that needs to resolve group names, check membership, or
enumerate who belongs to a group.

## Where is it used?

Every group-membership check ultimately traces back to this file (and the corresponding GID
field in `/etc/passwd` for primary memberships) — `id`, `groups`, permission checks, and `sudo`
group-based rules (`%groupname` in sudoers, Module 08) all depend on it.

## How it works

> 📊 Diagram: one `/etc/group` line broken into its four colon-separated fields, with an arrow
> pointing out that this member list only shows secondary members — a separate arrow points to a
> `/etc/passwd` line's GID field, labeled "primary group membership lives here instead."

```
developers:x:1001:alice,bob,carol
```

| Field | Meaning |
|---|---|
| `developers` | Group name |
| `x` | Password placeholder (group passwords are rarely used; see below) |
| `1001` | GID |
| `alice,bob,carol` | Comma-separated list of **secondary** members |

**The critical detail**: this member list only includes users for whom `developers` is a
*secondary* group. A user whose *primary* group is `developers` won't appear in this list at
all — their membership is recorded only via their GID in `/etc/passwd`. Checking `/etc/group`
alone can make it look like someone isn't a member of a group they're actually in via primary
membership.

`/etc/gshadow` exists alongside `/etc/group`, parallel to how `/etc/shadow` relates to
`/etc/passwd` — it can store a group password and group administrators, though group passwords
are rarely used in modern practice.

## Real-world example

An engineer greps `/etc/group` for a specific username to confirm group membership, doesn't find
them, and concludes they're not in the group — but they actually are, via primary group
membership, which never appears in `/etc/group`'s member list. The correct check is `id username`
or `groups username`, which correctly account for both primary and secondary membership,
covered next.

## Commands

- [`cat`](../../commands/cat.md) — `/etc/group` is world-readable, like `/etc/passwd`

## Production example

```
$ cat /etc/group | grep developers
developers:x:1001:alice,bob,carol
```

If `dave`'s primary group is `developers`, this line still won't show his name — a real,
common source of confusion this page exists to prevent.

## Do / Don't

| Do | Don't |
|---|---|
| Use `id username` or `groups username` to check membership completely | Rely on grepping `/etc/group` alone and conclude someone isn't a member |
| Remember `/etc/group`'s member list is secondary members only | Assume `/etc/group` is the complete picture of group membership |

## Common mistakes

- Grepping `/etc/group` and concluding a user isn't a group member, when they actually are via
  primary group membership — the single most common mistake this page exists to prevent.
- Editing `/etc/group` directly instead of using `groupadd`/`groupmod`/`usermod`, risking syntax
  errors.
- Confusing `/etc/gshadow`'s rarely-used group password feature with anything relevant to normal
  day-to-day group management.

## Best practices

- Always use `id username` or `groups username` (next topic) to check complete group membership,
  never `/etc/group` alone.
- Never edit `/etc/group` directly — use the dedicated commands from this module.

## Exercises

1. Read `/etc/group` and find a group with multiple listed members.
2. Find a user whose primary group (via `/etc/passwd`'s GID field) doesn't appear as a member in
   that group's `/etc/group` line.
3. Explain in one sentence why grepping `/etc/group` alone can give an incomplete answer about
   group membership.

## Quiz

**Q: Does `/etc/group`'s member list include users whose primary group it is?**
<details><summary>Show answer</summary>
No — only secondary/supplementary members are listed. Primary group membership is recorded via
the GID field in `/etc/passwd` instead.
</details>

**Q: What's the correct way to check a user's complete group membership?**
<details><summary>Show answer</summary>
`id username` or `groups username` — both correctly account for primary and secondary membership
together, unlike grepping `/etc/group` alone.
</details>

## Interview questions

- Why might a user not appear in `/etc/group`'s member list for a group they actually belong to? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/etc/group` defines every group: name, GID, and secondary member list.
- Primary group membership never appears in `/etc/group`'s member list — only in `/etc/passwd`.
- Use `id`/`groups` for a complete membership check, never `/etc/group` alone.
- Never edit `/etc/group` directly — use the dedicated group management commands.

## Further Reading

- [group(5) man page](https://man7.org/linux/man-pages/man5/group.5.html)

## Related topics

- [Adding Users to Groups](adding-users-to-groups.md)
- [Module 08: /etc/passwd and /etc/shadow](../08-users/etc-passwd-and-etc-shadow.md)
