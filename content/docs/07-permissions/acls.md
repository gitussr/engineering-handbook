---
title: "ACLs: setfacl, getfacl"
description: "When owner/group/other isn't enough — granting a specific extra user or group access without changing the file's actual owner or group."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["07-permissions/suid-sgid-sticky-bit"]
relatedTopics: ["common-permission-errors"]
relatedCommands: ["setfacl", "getfacl"]
careerRelevance: ["linux-administrator", "cybersecurity", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#acl-vs-standard-permissions"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/common-permission-errors"
prevTopic: "07-permissions/suid-sgid-sticky-bit"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["setfacl example", "getfacl explained", "linux acl permissions", "extended permissions linux"]
canonicalUrl: "/docs/permissions/acls"
---

# ACLs: setfacl, getfacl

🔴 Expert · Relevant for: Linux Administrator · Cybersecurity · SRE

> **TL;DR:** The owner/group/other model only supports one group. ACLs (Access Control Lists) let
> you grant a *specific additional* user or group access without changing the file's actual owner
> or group — the tool for "everyone in Group A, plus this one extra person from Group B."

## What is it?

An ACL (Access Control List) extends standard Linux permissions with additional, specific
user/group entries beyond the single owner and single group the standard model
([The Permission Model](permission-model.md)) supports.

## Why does it exist?

Sometimes access requirements genuinely don't fit three scopes — "everyone in the `finance`
group, plus this one specific contractor who isn't in that group, needs access to this file."
Standard permissions can't express that without either adding the contractor to `finance`
(over-broad) or changing "other" permissions (far too broad). ACLs solve exactly this gap.

## Where is it used?

- Fine-grained access control on shared filesystems where the owner/group/other model is too
  coarse
- Situations requiring temporary or exceptional access for a specific user without altering group
  membership
- Enterprise/regulated environments where access needs to be precisely scoped and auditable per
  user

## How it works

> 📊 Diagram: a file with its normal owner/group/other permissions shown, plus an additional
> "ACL entries" list attached alongside — one entry granting a specific extra user read access,
> visually distinct from (additive to) the standard three scopes.

```
setfacl -m u:contractor:r-- file.txt   # grant a specific user read-only access
setfacl -m g:auditors:r-x directory/    # grant a specific group read+execute
getfacl file.txt                        # view all ACL entries on a file
setfacl -x u:contractor file.txt        # remove a specific ACL entry
```

A file with ACL entries shows a `+` after its permission string in `ls -l` output — a visible
signal that standard `rwx` alone doesn't tell the full access story for that file.

## Real-world example

A finance team's shared directory is group-owned by `finance`, with group permissions granting
read/write to the whole team. A single external auditor needs temporary read-only access without
being added to the `finance` group (which would grant broader access than intended, and would
need to be remembered and revoked later). `setfacl -m u:auditor:r-- /shared/finance` grants
exactly that one user exactly read access, cleanly revocable with `setfacl -x` when the audit
ends — without ever touching the file's actual owner or group.

## Commands

- [`setfacl`](../../commands/setfacl.md) — set ACL entries
- [`getfacl`](../../commands/getfacl.md) — view ACL entries

## Production example

```
$ setfacl -m u:auditor:r-- /shared/finance/report.csv
$ getfacl /shared/finance/report.csv
# file: report.csv
# owner: finance-admin
# group: finance
user::rw-
user:auditor:r--
group::rw-
mask::rw-
other::---
$ ls -l /shared/finance/report.csv
-rw-rw----+ 1 finance-admin finance 2048 Jul 25 09:00 report.csv
```

Notice the `+` at the end of the permission string — the signal that ACL entries exist beyond
what the standard string shows.

## Do / Don't

| Do | Don't |
|---|---|
| Use ACLs for genuinely exceptional, narrowly-scoped access needs | Reach for ACLs as a default instead of proper group design |
| Check for the `+` in `ls -l` when standard permissions don't explain observed access | Assume `ls -l`'s permission string is the complete access picture |
| Remove ACL entries (`setfacl -x`) once temporary access is no longer needed | Leave stale ACL entries granting access no longer required |

## Common mistakes

- Not noticing the `+` in `ls -l` output and being confused why a user has access the standard
  permission string doesn't seem to grant.
- Reaching for ACLs by default instead of well-designed groups, adding unnecessary per-file
  complexity when a group restructure would be cleaner and more maintainable.
- Leaving temporary ACL grants in place indefinitely instead of removing them once no longer
  needed — a real, recurring access-hygiene problem in audited environments.

## Best practices

- Prefer standard group-based permissions for anything that isn't genuinely exceptional; reserve
  ACLs for access needs that truly don't fit three scopes.
- Always check for `+` in `ls -l` when investigating access that doesn't match the visible
  permission string.
- Treat ACL grants as needing the same lifecycle discipline as any other access grant — remove
  them when no longer needed.

## Exercises

1. Grant a specific user read-only ACL access to a test file and confirm with `getfacl`.
2. Confirm the `+` appears in `ls -l` output for that file.
3. Remove the ACL entry and confirm it's gone.

## Quiz

**Q: What does a `+` at the end of an `ls -l` permission string indicate?**
<details><summary>Show answer</summary>
The file has ACL entries beyond the standard owner/group/other permissions — the standard
permission string alone doesn't tell the complete access story.
</details>

**Q: When would you use an ACL instead of just adding a user to the file's group?**
<details><summary>Show answer</summary>
When the access need is narrow and exceptional — adding the user to the group would grant
broader access than intended (everything else the group can access), where an ACL grants access
to just that one file/directory.
</details>

## Interview questions

- When would standard owner/group/other permissions be insufficient, and how would ACLs solve it? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- ACLs extend standard permissions with specific additional user/group entries.
- `setfacl` sets entries, `getfacl` views them, and `+` in `ls -l` signals their presence.
- Reserve ACLs for genuinely exceptional access needs — group design should handle the common
  case.
- Stale ACL grants are a real access-hygiene issue worth periodically auditing.

## Related topics

- [Common Permission Errors](common-permission-errors.md)
