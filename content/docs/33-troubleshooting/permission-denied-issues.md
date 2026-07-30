---
title: "Permission Denied Issues"
description: "Applying this module's troubleshooting framework specifically to Permission Denied incidents in deploy scripts and services, and why the fast fix is often the wrong fix."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/common-permission-errors"]
relatedTopics: ["33-troubleshooting/service-wont-start", "33-troubleshooting/ssh-connection-issues"]
relatedCommands: ["chmod", "chown"]
careerRelevance: ["linux-administrator", "devops", "backend", "software-engineering"]
relatedLabs: ["beginner/fix-a-permission-denied-script-error"]
relatedInterviewQuestions: ["scenario-based#deploy-script-fails-permission-denied", "beginner#chmod-755-vs-644"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "33-troubleshooting/network-connectivity-issues"
prevTopic: "33-troubleshooting/service-wont-start"
estimatedReadingTime: 6
updatedAt: "2026-07-30"
keywords: ["permission denied troubleshooting incident", "deploy script permission denied", "linux permission denied in production"]
canonicalUrl: "/docs/troubleshooting/permission-denied-issues"
---

# Permission Denied Issues

🟢 Must Know · Relevant for: Linux Administrator · DevOps · Backend · Software Engineering

> **TL;DR:** The full diagnostic checklist for "permission denied" lives on
> [Common Permission Errors](../07-permissions/common-permission-errors.md) — this page is about
> applying it under real incident pressure, where the fast, wrong fix (`chmod 777`) is most tempting.

## What is it?

The specific application of this module's [troubleshooting methodology](troubleshooting-methodology.md)
to permission-denied incidents — recognizing that something changed in the environment (not the
code) and diagnosing which layer (ownership, permission bits, directory execute, ACLs, SELinux) is
actually responsible, without reaching for a broad workaround.

## Why does it exist?

Permission-denied errors are one of the most common production incidents, and they're also the
one where the fast fix (`chmod 777`, running as root) most reliably "works" while masking the
actual cause — which usually reappears, and usually leaves a real security gap behind. This page
exists to connect the diagnostic checklist (fully covered in Module 07) to the incident-response
mindset from this module.

## Where is it used?

A deploy script suddenly failing with permission denied, a service that can't write to its own log
or data directory, or a newly-provisioned server where a script that "should just work" doesn't.

## How it works

1. **Ask what changed** — a permission-denied error on something that worked before almost always
   traces to a recent change: a new deploy user, a moved directory, a service now running under a
   different account.
2. **Diagnose using the full checklist** on
   [Common Permission Errors](../07-permissions/common-permission-errors.md) — ownership, then
   permission bits for your actual scope, then directory execute bits, then ACLs, then SELinux/
   AppArmor if enabled.
3. **Resist the fast fix.** `chmod 777` or running as root will very often make the symptom go
   away — that's exactly why it's tempting under pressure, and exactly why it's wrong: it doesn't
   identify or fix the actual cause.
4. **Apply the narrow fix** the diagnosis actually points to — usually `chown` to the correct
   user/group, occasionally a specific `chmod`, rarely anything broader.
5. **Verify** by reproducing the original failing action as the actual user/process that needs
   access, not just as yourself with elevated privileges.

## Real-world example

A deploy pipeline starts failing with `Permission denied` writing to `/var/www/app/cache` — the
pipeline hasn't changed. Investigation shows the deploy user was recently migrated to run as a
new, more restricted service account as part of a security hardening pass, and nobody updated the
cache directory's ownership to match. `chmod 777` on the directory would have silently resolved
the symptom while reintroducing exactly the broad-write exposure the hardening pass was meant to
close. The correct fix — `chown` the directory to the new service account — takes the same amount
of time and doesn't undo the security work.

## Commands

No new command example on this page — see [`chmod`](../../commands/chmod.md) and
[`chown`](../../commands/chown.md), and the full diagnostic order on
[Common Permission Errors](../07-permissions/common-permission-errors.md).

## Production example

```
$ deploy.sh
Error: cannot write to /var/www/app/cache: Permission denied

$ ls -ld /var/www/app/cache
drwxr-xr-x 2 olddeployuser olddeployuser 4096 Jul 20 09:00 /var/www/app/cache

$ whoami
newdeployuser
```

`newdeployuser` is neither the owner nor in the owning group of the cache directory — the fix is
`sudo chown -R newdeployuser:newdeployuser /var/www/app/cache`, not loosening the directory's
permissions for everyone.

## Do / Don't

| Do | Don't |
|---|---|
| Ask what changed (new user, moved path, new service account) before diagnosing | Assume the error is random or the code is at fault |
| Diagnose using the full ownership → bits → directory → ACL → SELinux order | Jump to `chmod 777` as a first response |
| Fix with the narrowest correct change (usually `chown`) | Leave an overly permissive fix in place after the incident is "resolved" |
| Verify as the actual user/process that needs access | Verify only as yourself with sudo, which doesn't prove the real fix worked |

## Common mistakes

- Reaching for `chmod 777` under incident pressure because it reliably "works" — while leaving
  the actual cause (usually ownership) unaddressed and opening a real security gap.
- Not asking what changed recently — a new deploy user or migrated service account is a common,
  overlooked root cause.
- Verifying a fix as yourself (with broader privileges) instead of as the actual user/process that
  hit the original error.

## Best practices

- Treat "what changed?" as the first question in any permission-denied incident, not an afterthought.
- Default to the narrowest fix the diagnosis supports — usually `chown`, occasionally a specific `chmod`.
- Document the fix and its cause in the incident record so a recurring pattern (e.g. a service
  account migration missing a step) gets caught systemically, not just patched once.

## Exercises

1. Simulate the production example above locally and practice diagnosing it with the Module 07
   checklist before applying the `chown` fix.
2. Explain in one sentence why fixing a permission incident as `root` doesn't verify the fix for
   the actual affected user.
3. Write the incident-note sentence you'd file explaining a `chown`-based fix, including what
   changed to cause the issue.

## Quiz

**Q: Why is `chmod 777` a risky first response to a permission-denied incident, even though it usually "works"?**
<details><summary>Show answer</summary>
It resolves the symptom by granting broad access, which often masks the actual cause (usually
wrong ownership) and introduces a real, unnecessary security exposure that outlives the incident.
</details>

**Q: What's the first question worth asking when something that used to work now fails with permission denied?**
<details><summary>Show answer</summary>
What changed recently — a new deploy user, a migrated service account, or a moved directory is
the most common actual cause, not a code change.
</details>

## Interview questions

- A deploy script that has run successfully for months suddenly fails with "Permission denied" —
  nobody changed the script. What do you check? →
  [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#deploy-script-fails-permission-denied)
- What's the practical difference between `chmod 755` and `chmod 644` on a file? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md#chmod-755-vs-644)

## Key Takeaways

- The full diagnostic checklist lives on [Common Permission Errors](../07-permissions/common-permission-errors.md) — this page is the incident-response mindset applied to it.
- "What changed recently?" is almost always the fastest path to the real cause.
- `chmod 777` is a trap: it works, and that's exactly the problem.
- Verify a fix as the actual affected user/process, not as yourself with elevated privileges.

## Related topics

- [Service Won't Start](service-wont-start.md)
- [SSH Connection Issues](ssh-connection-issues.md)
- [Permissions](../07-permissions/index.md)
