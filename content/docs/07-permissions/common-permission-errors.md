---
title: "Common Permission Errors"
description: "A systematic checklist for diagnosing permission denied errors — ownership, permission bits, directory execute, ACLs, and SELinux/AppArmor, in the right order."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/acls"]
relatedTopics: []
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#diagnose-permission-denied"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "08-users/user-accounts-overview"
prevTopic: "07-permissions/acls"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["permission denied linux troubleshooting", "diagnose permission errors", "eacces linux"]
canonicalUrl: "/docs/permissions/common-permission-errors"
---

# Common Permission Errors

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** "Permission denied" has more possible causes than just wrong `rwx` bits — ownership,
> directory execute permission, ACLs, and (on hardened systems) SELinux/AppArmor can all produce
> the identical error message. Diagnose in that order rather than guessing.

## What is it?

A systematic troubleshooting checklist for "permission denied" errors, pulling together every
concept from this module into the order you should actually check them in — because the error
message itself never tells you which layer is actually responsible.

## Why does it exist?

Every concept in this module (owner/group/other, rwx, chmod, chown, umask, SUID/SGID/sticky,
ACLs) can independently produce the exact same "permission denied" message, and beginners
routinely guess-and-check with `chmod 777` instead of diagnosing — which sometimes "fixes" the
symptom while leaving the actual cause (usually wrong ownership) unaddressed and often
introduces a real security problem in the process.

## Where is it used?

Every single time a "permission denied" error appears, from your first month of Linux to a
senior engineer's production incident response.

## How it works

> 📊 Diagram: a decision-tree flowchart — "Permission denied" at the top, branching through
> ordered checks: "Check ownership (ls -l)" → "Check permission bits for the relevant scope" →
> "If a directory, check execute (x) permission" → "Check for ACLs (+ in ls -l)" → "Check
> SELinux/AppArmor if enabled (Module 19)" — each branch showing what to do if that's the cause.

**The diagnostic order:**

1. **Check ownership** (`ls -l`) — are you the owner, in the owning group, or neither? This
   determines which permission scope actually applies to you (see
   [The Permission Model](permission-model.md)).
2. **Check permission bits for your actual scope** — don't assume owner permissions apply if
   you're not the owner.
3. **If accessing something inside a directory, check the directory's execute bit** — missing `x`
   on any directory in the path blocks access, even with correct file permissions (see
   [rwx Explained](rwx-explained.md)).
4. **Check for ACLs** — a `+` in `ls -l` output means the standard permission string isn't the
   whole story (see [ACLs](acls.md)).
5. **Check SELinux/AppArmor if enabled** — on hardened systems, a security module can deny access
   even when standard Linux permissions would allow it; full coverage in
   [Module 19: Security](../19-security/index.md).

## Real-world example

A deployment fails with "permission denied" writing to a log directory. The reflexive fix,
`chmod 777 /var/log/app`, "works" — but the actual cause was that the deployment now runs as a
different service user than before, and the correct fix was `chown` to the new user, not loosening
permissions for everyone. The `chmod 777` band-aid leaves a real security gap that the ownership
fix wouldn't have.

## Commands

No new command example on this page — this page is a diagnostic framework applying commands
already covered throughout this module (`ls -l`, `chmod`, `chown`, `getfacl`).

## Production example

```
$ echo "test" > /var/log/app/test.log
bash: /var/log/app/test.log: Permission denied
$ ls -ld /var/log/app
drwxr-xr-x 2 root root 4096 Jul 25 09:00 /var/log/app
$ whoami
appuser
```

`appuser` isn't the owner (`root`) and isn't in the owning group — "other" permissions apply,
which don't include write. The fix is `chown` or a group change, not loosening permissions
broadly.

## Do / Don't

| Do | Don't |
|---|---|
| Diagnose in order: ownership → permission bits → directory execute → ACLs → SELinux | Jump straight to `chmod 777` as a first response |
| Run `ls -l` (and `ls -ld` for directories) as the first diagnostic step | Guess at the cause without checking actual ownership and permissions |
| Fix the actual cause (usually ownership) rather than the symptom | Leave an overly permissive "fix" in place after the real problem is found |

## Common mistakes

- Reaching for `chmod 777` as a first response instead of diagnosing — it often "works" by
  accident while leaving (or creating) a real security problem.
- Forgetting to check the parent directory's execute permission when a file's own permissions
  look completely correct.
- Not checking SELinux/AppArmor on hardened systems, where permissions can look entirely correct
  by standard Linux rules while still being denied by a security module (Module 19).

## Best practices

- Always run `ls -l` (or `ls -ld` for a directory) as the very first diagnostic step — don't
  guess.
- Work through the diagnostic order in this page systematically rather than randomly trying
  fixes.
- Fix the actual cause, even when a broader permission change would also happen to work — the
  narrower, correct fix is worth the extra thirty seconds of diagnosis.

## Exercises

1. Deliberately create a permission-denied scenario (e.g. a file owned by another user with no
   group/other access) and diagnose it using the order in this page.
2. Explain in one sentence why `chmod 777` is a poor default response to a permission error.
3. Write out, from memory, the five-step diagnostic order this page teaches.

## Quiz

**Q: What should you check first when facing a "permission denied" error?**
<details><summary>Show answer</summary>
Ownership — run `ls -l` to see who owns the file and which group it belongs to, which determines
which permission scope actually applies to you.
</details>

**Q: Why is `chmod 777` a poor default fix for a permission error?**
<details><summary>Show answer</summary>
It often resolves the symptom without addressing the actual cause (frequently wrong ownership),
while introducing a real, unnecessary security exposure by granting full access to everyone.
</details>

## Interview questions

- Walk through how you'd diagnose a "permission denied" error on a production server. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- "Permission denied" has multiple possible causes that all produce the identical message.
- Diagnose in order: ownership, permission bits for your actual scope, directory execute, ACLs,
  then SELinux/AppArmor.
- Fix the actual cause — `chmod 777` is a band-aid that often masks the real problem.
- This diagnostic framework applies every concept from this entire module together.

## Related topics

- [Module 08: Users](../08-users/index.md)
- [Module 19: Security](../19-security/index.md)
