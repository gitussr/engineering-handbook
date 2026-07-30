---
title: "Groups"
description: "Module 09 of the Linux roadmap — what groups are, primary vs secondary group membership, group lifecycle commands, and designing a real group-based permission strategy."
module: "09-groups"
moduleTitle: "Groups"
stage: "beginner"
type: "module-index"
nextTopic: "09-groups/groups-overview"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/groups"
---

# Groups

Module 09 of 34 · Stage: Beginner · Previous: [08 Users](../08-users/index.md)

Groups have already appeared throughout this documentation — the "group" scope in Module 07's
permission model, and `usermod -aG` in Module 08. This module completes the picture: what a group
actually is, the primary/secondary distinction, the group lifecycle commands, and how to design
group structure deliberately instead of ad hoc. Where a topic here overlaps with something
Module 08 already taught in full (like `usermod -aG`'s syntax), this module links to it rather
than repeating it, and focuses on the group-side angle instead.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Groups Overview](groups-overview.md) | 🟢 Must Know |
| [Primary vs Secondary Groups](primary-vs-secondary-groups.md) | 🟢 Must Know |
| [groupadd, groupmod, groupdel](groupadd-groupmod-groupdel.md) | 🟢 Must Know |
| [The /etc/group File](etc-group-file.md) | 🟢 Must Know |
| [Adding Users to Groups](adding-users-to-groups.md) | 🟢 Must Know |
| [Group-Based Permission Strategy](group-based-permission-strategy.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain what a group is and why it exists as a distinct concept from a user account.
- Explain the difference between a user's primary group and their secondary groups.
- Create, modify, and delete groups with `groupadd`, `groupmod`, `groupdel`.
- Read `/etc/group` and explain its fields.
- Check group membership from both the user side and the group side, and change a user's
  primary group when needed.
- Design a group structure for a team or shared resource instead of granting ad hoc access.

## Known, intentional gaps in this module

- Canonical command pages exist for `groupadd`, `groupmod`, `groupdel`, and `groups`.
- `getent` (for querying `/etc/group` and other name service databases generically) is
  referenced but doesn't yet have a canonical command page.

**Previous module:** [08 Users](../08-users/index.md)
**Next module:** [10 Processes →](../10-processes/index.md)
