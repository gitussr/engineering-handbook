---
title: "Lab: Fix a \"Permission Denied\" Script Error"
description: "A beginner Linux lab, framed as a real support ticket: diagnose and fix a script that fails with Permission Denied, and understand why it happened."
type: "lab"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops", "software-engineering"]
updatedAt: "2026-07-30"
keywords: ["permission denied lab linux", "chmod exercise beginner", "fix bash script permission error", "linux permissions troubleshooting lab"]
canonicalUrl: "/labs/beginner/fix-a-permission-denied-script-error"
---

# Lab: Fix a "Permission Denied" Script Error

🟢 Beginner · Relevant for: Linux Administrator, DevOps, Software Engineering

## Scenario

**Ticket #LX-103:** "A teammate wrote a deploy script (`deploy.sh`) and committed it to the repo.
When another engineer pulled it down and tried to run it with `./deploy.sh`, they got
`Permission denied`. The file is clearly there and clearly has content — figure out what's wrong
and fix it, then explain to the team why it happened so it doesn't recur."

## Environment Setup

Reproduce the ticket yourself:

```bash
echo -e '#!/bin/bash\necho "Deploying..."' > deploy.sh
chmod 644 deploy.sh
./deploy.sh
```

You should see `-bash: ./deploy.sh: Permission denied`.

## Tasks

1. Confirm exactly why the script can't be executed (don't just guess — check).
2. Fix it so `./deploy.sh` runs successfully.
3. Choose a permission mode that's appropriate for a script other engineers will also run —
   not the loosest possible setting.
4. Write a one-sentence explanation of the root cause you'd put in the ticket resolution.

## Hints

<details><summary>Show hint</summary>
`ls -l` shows the current permission bits as ten characters — compare what's there against what a
runnable script needs. Git famously doesn't always preserve the execute bit across a clone if it
wasn't set before committing.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Confirm the cause
ls -l deploy.sh
# -rw-r--r-- 1 user user 30 Jul 30 10:00 deploy.sh
# No 'x' anywhere in the permission string — nobody can execute it, regardless of ownership.

# Fix it
chmod 755 deploy.sh
ls -l deploy.sh
# -rwxr-xr-x 1 user user 30 Jul 30 10:00 deploy.sh

./deploy.sh
# Deploying...
```

`755` is the right choice here: the owner and everyone else can execute and read it, but only the
owner can modify it — appropriate for a shared script, unlike `777` which would let anyone edit it
too.

**Root cause for the ticket:** the file never had its execute bit set — likely created by an
editor or `echo`/redirection (as reproduced above), which never sets execute, rather than being
made executable explicitly before the first commit.
</details>

## What You Learned

- `Permission denied` on a script you can clearly see and read almost always means the execute
  bit isn't set — not a deeper ownership or filesystem problem.
- The difference between `644` (readable/writable, not executable) and `755` (also executable).
- Why picking the *right* permission (not just "whatever works") matters for shared scripts.

## Related Modules

- [Permissions](../../docs/07-permissions/index.md)
- [Files](../../docs/06-files/index.md)

## Related topics

- [Permissions Cheat Sheet](../../cheatsheets/permissions.md)
- [Common Permission Errors](../../docs/07-permissions/common-permission-errors.md)
- [Labs Hub](../index.md)
