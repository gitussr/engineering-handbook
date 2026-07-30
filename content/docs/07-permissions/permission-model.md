---
title: "The Permission Model: Owner, Group, Other"
description: "Why every Linux file has exactly three permission scopes, and how that maps to real access-control decisions."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/regular-expressions-basics"]
relatedTopics: ["rwx-explained"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#owner-group-other"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/rwx-explained"
prevTopic: "06-files/regular-expressions-basics"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["linux permission model", "owner group other", "unix file permissions basics"]
canonicalUrl: "/docs/permissions/permission-model"
---

# The Permission Model: Owner, Group, Other

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** Every file has exactly three permission scopes — the owning user, the owning group,
> and everyone else — each with their own read/write/execute permissions. That's the entire
> model; every permissions command in this module is just a way of setting these three scopes.

## What is it?

Linux's permission model assigns access rights to a file across exactly three scopes: the
**owner** (one specific user), the **group** (one specific group), and **other** (everyone
else). Each scope gets its own read/write/execute permissions, covered in depth in
[rwx Explained](rwx-explained.md).

## Why does it exist?

A single "owner" scope alone can't express "the whole finance team should be able to edit this,
but no one outside it" — that needs a group concept. And a system needs a fallback scope
("everyone else") for anyone not the owner and not in the group. Three scopes are the minimum
needed to express the vast majority of real-world access control needs without something more
complex like per-user permission lists (which [ACLs](acls.md) exist for, when three scopes
genuinely aren't enough).

## Where is it used?

Every file and directory on every Linux system, permanently — this isn't an optional feature you
turn on, it's the permission system every file has by default from the moment it's created.

## How it works

> 📊 Diagram: a file icon with three labeled brackets radiating from it — "Owner: one specific
> user," "Group: one specific group," "Other: everyone else" — each bracket showing its own
> independent read/write/execute toggle set, to make clear these are three separate, independent
> permission sets on the same file.

Every file has exactly one owner and one owning group (both assignable, see
[chown and chgrp](chown-chgrp.md)) — never more than one of each. A user's actual access to a
file is determined by which scope applies to them: if they're the owner, owner permissions apply;
if not the owner but a member of the owning group, group permissions apply; otherwise, other
permissions apply. Only one scope ever applies to a given user for a given file — they don't
stack.

## Real-world example

A shared project directory is owned by a specific deploy user, with its group set to a
`developers` group that every team member belongs to. Developers (via group permissions) can read
and write files; the deploy user (owner) has full control; anyone else on the system (other) has
no access at all. Three scopes, one directory, exactly the access control the team actually
needs — no per-user configuration required.

## Commands

No command example on this page — this page is model-level context. See
[rwx Explained](rwx-explained.md) for the first hands-on permission inspection in this module.

## Production example

Not applicable — see [rwx Explained](rwx-explained.md) for the first terminal session in this
module.

## Do / Don't

| Do | Don't |
|---|---|
| Use group ownership to share access among a team | Grant "other" write access as a shortcut for team sharing |
| Remember only one scope applies per user, per file | Assume owner and group permissions both apply to the file's owner if they're also in the group |

## Common mistakes

- Granting broad "other" permissions as a shortcut instead of setting up proper group-based
  access — a real, common security weakening.
- Assuming permissions "stack" — if you're the owner, only owner permissions apply to you, even
  if you're also a member of the owning group with different (say, more restrictive) group
  permissions.
- Forgetting a file has exactly one owner and one group at any given time — not a list of either.

## Best practices

- Model access control around groups for anything shared by more than one person, rather than
  loosening "other" permissions.
- When permissions look wrong, check which scope actually applies to the user in question before
  assuming the permission bits themselves are misconfigured.

## Exercises

1. Run `ls -l` on any file and identify its owner and group from the output.
2. Explain in one sentence why "other" permissions being too permissive is a common security
   issue.
3. Explain what happens if a file's owner is also a member of its owning group with more
   restrictive group permissions — which applies?

## Quiz

**Q: How many permission scopes does a Linux file have?**
<details><summary>Show answer</summary>
Exactly three: owner, group, and other.
</details>

**Q: If you're the owner of a file, do the group permissions also apply to you?**
<details><summary>Show answer</summary>
No — only one scope applies per user per file. If you're the owner, owner permissions apply,
regardless of your group membership.
</details>

## Interview questions

- Explain the owner/group/other permission model. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Three scopes: owner (one user), group (one group), other (everyone else).
- Each scope has independent read/write/execute permissions.
- Only one scope applies per user per file — they don't combine or stack.
- Group-based access is the standard way to share access among a team, not loosened "other" bits.

## Related topics

- [rwx Explained](rwx-explained.md)
- [Module 08: Users](../08-users/index.md)
