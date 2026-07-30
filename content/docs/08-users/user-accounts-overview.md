---
title: "User Accounts Overview"
description: "What a user account actually is, the UID that identifies it, and the difference between system accounts and human-facing regular accounts."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["07-permissions/common-permission-errors"]
relatedTopics: ["etc-passwd-and-etc-shadow"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#uid-explained"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "08-users/etc-passwd-and-etc-shadow"
prevTopic: "07-permissions/common-permission-errors"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["linux user account", "uid explained", "system user vs regular user", "root uid 0"]
canonicalUrl: "/docs/users/user-accounts-overview"
---

# User Accounts Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** Every user is really a UID (a number) with a username as a human-friendly label.
> Root is always UID 0. Low UIDs are reserved for system/service accounts; regular human accounts
> start at a distro-defined threshold (often 1000).

## What is it?

A user account is an identity the kernel and filesystem use to enforce permissions and track
ownership — from [The Permission Model](../07-permissions/permission-model.md), every file's
"owner" is a user account. Internally, the actual identity is a number (the UID), not the
username you type.

## Why does it exist?

Linux is fundamentally a multi-user system — even a single-person laptop runs many processes
under different, isolated accounts (a display service, a network manager, your own login) so a
compromise or bug in one doesn't automatically have access to everything else. User accounts are
the mechanism that makes that isolation possible.

## Where is it used?

- Every process runs as some user; that user's permissions bound what the process can do
- Every file has exactly one owning user (Module 07)
- Service accounts (for Nginx, databases, etc.) run isolated from human login accounts and from
  each other

## How it works

> 📊 Diagram: a number line from 0 upward, with labeled bands — "0: root," "1-999 (roughly):
> system/service accounts," "1000+: regular human accounts" (exact thresholds vary by distro) —
> making clear UID ranges are a convention, not a hard technical rule enforced by the kernel
> itself.

- **UID (User ID)**: the actual numeric identity the kernel uses. Usernames are a human-readable
  label mapped to a UID (in `/etc/passwd`, covered next).
- **Root (UID 0)**: always UID 0, on every Linux system — this is a hard rule, not convention.
  UID 0 has unrestricted access regardless of standard permission checks.
- **System/service accounts**: low, non-zero UIDs (roughly 1-999, varies by distro), created for
  services (a database, a web server) to run isolated from both root and human users, with no
  interactive login typically needed.
- **Regular user accounts**: UIDs starting at a distro-defined threshold (often 1000), for actual
  human logins.

## Real-world example

An Nginx installation creates a dedicated `www-data` (or `nginx`) service account with a low,
non-human UID specifically so the web server process runs with only the permissions it actually
needs — not as root, and not as any human's account — limiting the damage if the web server
software itself is ever compromised.

## Commands

No command example on this page — this page is conceptual context. See
[/etc/passwd and /etc/shadow](etc-passwd-and-etc-shadow.md) for the first hands-on account
inspection in this module.

## Production example

Not applicable — see [/etc/passwd and /etc/shadow](etc-passwd-and-etc-shadow.md) for the first
terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Run services under dedicated, low-privilege service accounts | Run a service as root "because it's simpler" |
| Understand root is always UID 0, a hard rule | Assume UID ranges for system vs. regular users are enforced identically across every distro |

## Common mistakes

- Running application services as root instead of a dedicated service account — unnecessarily
  broadens the impact of any vulnerability in that service.
- Assuming a username is the "real" identity — the UID is what the kernel actually checks;
  usernames are just a lookup convenience.
- Assuming UID range conventions (system vs. regular) are identical and hard-enforced across
  every distro — they're conventions that vary somewhat, not a kernel-enforced rule (unlike UID 0
  for root, which is universal).

## Best practices

- Create dedicated service accounts for anything that runs a background service, rather than
  reusing a human account or root.
- Remember that UID, not username, is the actual identity — relevant when usernames are reused or
  UIDs are inspected directly (e.g. in `/proc` or file ownership after a UID is deleted and
  reused).

## Exercises

1. Run `id` to see your own UID and group memberships.
2. Find the UID of a system service account on your machine (e.g. via `/etc/passwd`, covered
   next).
3. Explain in one sentence why root is always UID 0 on every Linux system.

## Quiz

**Q: What UID is root always assigned?**
<details><summary>Show answer</summary>
0 — this is a hard, universal rule on every Linux system, not a convention.
</details>

**Q: Why do services like Nginx run under a dedicated account instead of root?**
<details><summary>Show answer</summary>
To limit the damage if the service is ever compromised — a dedicated, low-privilege account only
has the specific permissions it actually needs, unlike root.
</details>

## Interview questions

- Why do production services typically run under dedicated, non-root accounts? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A user account is fundamentally a UID; the username is a human-readable label.
- Root is always UID 0 — a universal rule, not convention.
- System/service accounts use low UIDs and run isolated from human accounts.
- Services should run under dedicated accounts, never root, to limit compromise impact.

## Related topics

- [/etc/passwd and /etc/shadow](etc-passwd-and-etc-shadow.md)
