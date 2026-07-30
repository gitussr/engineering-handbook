---
title: "Groups Overview"
description: "What a group actually is — a named collection of users, identified by a GID, that exists specifically to make shared access manageable."
module: "09-groups"
moduleTitle: "Groups"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["08-users/user-account-security-hardening"]
relatedTopics: ["primary-vs-secondary-groups"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#what-is-a-group"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "09-groups/primary-vs-secondary-groups"
prevTopic: "08-users/user-account-security-hardening"
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["linux group explained", "gid explained", "what is a group linux"]
canonicalUrl: "/docs/groups/groups-overview"
---

# Groups Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** A group is a named collection of users, identified by a GID (parallel to a user's
> UID). Groups exist so permissions can be granted to a team or role once, instead of managed
> per individual user everywhere that access is needed.

## What is it?

A group is a named collection of user accounts, identified internally by a numeric GID (Group
ID), the group-side counterpart to a user's UID
([User Accounts Overview](../08-users/user-accounts-overview.md)).

## Why does it exist?

[The Permission Model](../07-permissions/permission-model.md) established that files have a
single owning group, and that group permissions apply to anyone who's a member — but that only
works if "member of this group" is itself a manageable concept. Groups exist as that concept:
a named, reusable set of users that permissions, and other group-aware system features, can
reference as a single unit instead of listing individual usernames everywhere.

## Where is it used?

- File and directory group ownership (Module 07)
- Granting a whole team access to a shared resource in one place, instead of one grant per person
- Some system services use dedicated groups to control who can interact with them (e.g. a
  `docker` group controlling who can use the Docker daemon without full root)

## How it works

> 📊 Diagram: a group shown as a labeled circle ("developers") containing several user icons
> inside it, with a GID label on the circle itself — paired with a file icon whose group
> ownership points at the same circle, showing how group membership and file group-ownership
> connect.

Every group has a name and a GID. A user's relationship to a group is either as their **primary
group** or a **secondary (supplementary) group** — a distinction significant enough to be its own
topic next, since it affects default file ownership and access differently.

## Real-world example

A company creates a `developers` group once, adds every engineer to it, and grants that group
read/write access to the shared project directory. When a new engineer joins, they're added to
`developers` and immediately have the same access as the rest of the team — no per-person
permission grants needed anywhere the group is already used.

## Commands

No command example on this page — this page is conceptual context. See
[The /etc/group File](etc-group-file.md) for the first hands-on group inspection in this module.

## Production example

Not applicable — see [The /etc/group File](etc-group-file.md) for the first terminal session in
this module.

## Do / Don't

| Do | Don't |
|---|---|
| Model shared access around a group, created once | Grant the same access to five people individually, five separate times |
| Think of a group as a reusable access unit | Treat groups as just a label with no real permission consequence |

## Common mistakes

- Granting the same access to multiple individual users repeatedly instead of creating one group
  and granting it once — harder to audit and easy to leave inconsistent over time.
- Assuming a group is purely organizational/cosmetic rather than a real mechanism tied directly
  into the permission model from Module 07.

## Best practices

- Default to creating a group the moment more than one person needs the same access, rather than
  granting it individually and "fixing it later."
- Name groups after their purpose (`developers`, `finance-readonly`) so their intent is clear
  without needing to look up why they exist.

## Exercises

1. Run `id` and identify which groups you currently belong to.
2. Name a real or hypothetical shared resource at a company and describe what group you'd create
   to manage access to it.
3. Explain in one sentence why groups scale better than granting access per individual user.

## Quiz

**Q: What identifies a group internally, parallel to a user's UID?**
<details><summary>Show answer</summary>
A GID (Group ID) — a numeric identifier, with the group name as a human-readable label, exactly
parallel to how UID and username work for users.
</details>

**Q: Why is creating a group better than granting the same access to several users individually?**
<details><summary>Show answer</summary>
It centralizes the access grant in one place — adding or removing someone's access becomes a
single group membership change instead of hunting down every place that access was granted
individually.
</details>

## Interview questions

- Why would you create a group instead of granting file access to several users individually? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A group is a named, GID-identified collection of users.
- Groups exist to make shared access manageable as a single unit, not per-person.
- Groups tie directly into the permission model from Module 07 — not just organizational labels.

## Related topics

- [Primary vs Secondary Groups](primary-vs-secondary-groups.md)
- [Module 07: The Permission Model](../07-permissions/permission-model.md)
