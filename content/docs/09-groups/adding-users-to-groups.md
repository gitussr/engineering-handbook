---
title: "Adding Users to Groups"
description: "The group-side view of membership — checking who's in a group, changing a user's primary group, and where usermod -aG (already covered) fits in."
module: "09-groups"
moduleTitle: "Groups"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["09-groups/etc-group-file"]
relatedTopics: ["group-based-permission-strategy"]
relatedCommands: ["groups"]
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#change-primary-group"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "09-groups/group-based-permission-strategy"
prevTopic: "09-groups/etc-group-file"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["groups command linux", "usermod -g primary group", "getent group", "check group membership"]
canonicalUrl: "/docs/groups/adding-users-to-groups"
---

# Adding Users to Groups

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** Adding a *secondary* membership is `usermod -aG group user`, already fully covered
> in [Module 08](../08-users/useradd-usermod-userdel.md) — this page doesn't repeat that. What's
> new here: changing a user's *primary* group (`usermod -g`), and checking membership from the
> group side (`groups`, `getent group`) rather than the user side.

## What is it?

The group-membership operations this module hasn't already covered: viewing who belongs to a
group (from the group's perspective, not just one user's `id` output), and changing which group
is a user's *primary* one — a different, less common operation than adding a secondary
membership.

## Why does it exist?

[Module 08](../08-users/useradd-usermod-userdel.md) taught `usermod -aG` for adding a secondary
membership, from the perspective of managing one user's account. This page fills the remaining
gap: sometimes you need to ask "who's in this group" rather than "what groups is this user in,"
and sometimes the actual need is changing someone's *primary* group, a genuinely different
operation with different consequences (it affects default file ownership, per
[Primary vs Secondary Groups](primary-vs-secondary-groups.md)).

## Where is it used?

- Auditing exactly who has access via a specific group (the group-first view)
- Correcting a user's primary group assignment (rare, but distinct from adding a secondary
  membership)
- Scripting or automation that needs to enumerate group membership programmatically

## How it works

> 📊 Diagram: two arrows into the same membership data — "user → groups" (what Module 08's
> `id`/`usermod -aG` approach shows) and "group → members" (`groups`/`getent group`, this page's
> new angle) — converging on the same underlying `/etc/passwd`+`/etc/group` data, viewed from two
> directions.

```
groups username           # list all groups a user belongs to (primary + secondary)
getent group groupname     # show a specific group's full entry, including all secondary members
usermod -g newgroup username   # change a user's PRIMARY group (different from -aG!)
```

`usermod -g` (lowercase, no `-a`) changes the *primary* group — a meaningfully different
operation from `usermod -aG` (adds a *secondary* group, preserving existing ones). Confusing the
two matters: `-g` changes what new files default to; `-aG` doesn't touch that at all.

## Real-world example

A security review needs to know exactly who has access to a sensitive `finance-readonly` group,
not just check individual users one at a time. `getent group finance-readonly` gives the
authoritative member list directly (correctly reflecting `/etc/group`, including any recent
`usermod -aG` changes) — the group-first view this page adds, distinct from asking "what groups
does Alice have" one person at a time.

## Commands

- [`groups`](../../commands/groups.md) — list a user's group memberships

`usermod -aG` (adding a secondary membership) is fully covered in
[Module 08: useradd, usermod, userdel](../08-users/useradd-usermod-userdel.md) — not repeated
here. `getent` is referenced as a forward-referenced command.

## Production example

```
$ groups deploy
deploy : deploy sudo docker
$ getent group developers
developers:x:1001:alice,bob,carol
$ sudo usermod -g developers deploy
```

The last command changes `deploy`'s *primary* group to `developers` — distinct from adding it as
a secondary membership, and something that changes their new files' default group ownership
going forward.

## Do / Don't

| Do | Don't |
|---|---|
| Use `getent group name` to audit a group's full membership | Manually cross-reference `/etc/passwd` and `/etc/group` by hand |
| Use `usermod -g` deliberately, understanding it changes default file ownership | Confuse `-g` (primary) with `-aG` (secondary) — they do different things |
| Use `groups username` for a quick check of one user's full membership | Grep `/etc/group` alone and miss primary memberships (previous topic) |

## Common mistakes

- Confusing `usermod -g` (change primary group) with `usermod -aG` (add secondary group) — using
  the wrong one changes default file ownership behavior unintentionally, or fails to grant the
  access actually needed.
- Not knowing `getent group` exists and manually reading `/etc/group` instead, missing the
  correctly-resolved, authoritative view `getent` provides (relevant on systems using non-file
  identity backends too, like LDAP, where `/etc/group` alone wouldn't even be complete).
- Auditing group membership user-by-user with `id` when a single `getent group name` answers it
  directly for a specific group.

## Best practices

- Use `getent group name` for group-first audits instead of cross-referencing files manually.
- Reserve `usermod -g` (primary group change) for genuine cases where default file ownership
  should actually change — use `-aG` for ordinary access grants.
- Document which command was used (`-g` vs `-aG`) in any change record, since the two have
  materially different effects.

## Exercises

1. Run `groups` on your own account and identify your full group list.
2. Use `getent group` on a group you belong to and confirm it matches what `groups` reported.
3. Explain in one sentence the practical difference between `usermod -g` and `usermod -aG`.

## Quiz

**Q: What's the difference between `usermod -g group` and `usermod -aG group`?**
<details><summary>Show answer</summary>
`-g` changes the user's primary group (affecting default new-file group ownership); `-aG` adds a
secondary group membership without touching the primary group or default file ownership.
</details>

**Q: Why might `getent group name` be more reliable than reading `/etc/group` directly?**
<details><summary>Show answer</summary>
`getent` queries whatever identity backend is actually configured (which could include LDAP or
other directory services beyond just local files), giving the authoritative answer rather than
assuming everything is defined locally in `/etc/group`.
</details>

## Interview questions

- What's the difference between changing a user's primary group and adding them to a secondary
  group? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Adding a secondary group membership (`usermod -aG`) was fully covered in Module 08 — this page
  doesn't repeat it.
- `usermod -g` changes a user's *primary* group, a distinct operation affecting default file
  ownership.
- `groups`/`getent group` give the group-first view of membership, complementing `id`'s
  user-first view.
- `getent` resolves against whatever identity backend is actually configured, not just local
  files.

## Related topics

- [Group-Based Permission Strategy](group-based-permission-strategy.md)
- [Module 08: useradd, usermod, userdel](../08-users/useradd-usermod-userdel.md)
