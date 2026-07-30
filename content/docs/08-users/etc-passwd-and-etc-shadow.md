---
title: "/etc/passwd and /etc/shadow"
description: "Where account information actually lives — why passwd is world-readable and shadow isn't, and how to read both files' fields."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["08-users/user-accounts-overview"]
relatedTopics: ["useradd-usermod-userdel"]
relatedCommands: ["cat"]
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#passwd-vs-shadow"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "passwd(5) man page", "url": "https://man7.org/linux/man-pages/man5/passwd.5.html"}]
nextTopic: "08-users/useradd-usermod-userdel"
prevTopic: "08-users/user-accounts-overview"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["etc passwd format", "etc shadow format", "why is shadow file restricted", "passwd file fields"]
canonicalUrl: "/docs/users/etc-passwd-and-etc-shadow"
---

# /etc/passwd and /etc/shadow

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** `/etc/passwd` holds account metadata (username, UID, home directory, shell) and is
> world-readable by design. `/etc/shadow` holds the actual password hashes and is restricted to
> root — split specifically so every process can look up account info without ever being able to
> read password hashes.

## What is it?

Two files that together define every user account on the system. `/etc/passwd` stores account
metadata; `/etc/shadow` stores password hashes and password aging policy, separately and with
much tighter access control.

## Why does it exist?

Historically, password hashes lived directly in `/etc/passwd` — which is world-readable, because
countless system operations need to look up account info (resolving a UID to a username, checking
a home directory) without needing root. Once offline password-cracking against exposed hashes
became a practical threat, the hashes were split into `/etc/shadow`, readable only by root,
while `/etc/passwd` stayed world-readable for its legitimate lookup purposes.

## Where is it used?

Every single account lookup on the system — logging in, resolving `ls -l`'s numeric UID to a
username, `sudo` checking who's allowed to do what. This is foundational plumbing every other
user/permission mechanism in Linux depends on.

## How it works

> 📊 Diagram: two files side by side — `/etc/passwd` labeled "world-readable" with a green
> lock icon, `/etc/shadow` labeled "root-only" with a red lock icon — each showing one example
> line broken into its colon-separated fields.

**`/etc/passwd`** — one line per user, colon-separated fields:

```
deploy:x:1000:1000:Deploy User:/home/deploy:/bin/bash
```

| Field | Meaning |
|---|---|
| `deploy` | Username |
| `x` | Password placeholder — the real hash lives in `/etc/shadow` |
| `1000` | UID |
| `1000` | Primary GID |
| `Deploy User` | GECOS field — display name/comment |
| `/home/deploy` | Home directory |
| `/bin/bash` | Login shell |

**`/etc/shadow`** — one line per user, colon-separated, readable only by root:

```
deploy:$6$hashvalue...:19500:0:99999:7:::
```

Key fields: the hashed password, last change date, minimum/maximum password age, and warning
period — the actual password aging policy lives here, not in `/etc/passwd`.

## Real-world example

A security audit checks `/etc/shadow` (root-only) to confirm no accounts have empty password
hashes and that password aging policy is enforced — a check that would be meaningless against
`/etc/passwd` alone, since that file has never stored the actual hash, by design, for exactly this
kind of exposure-prevention reason.

## Commands

- [`cat`](../../commands/cat.md) — `/etc/passwd` is readable directly; `/etc/shadow` requires root

## Production example

```
$ cat /etc/passwd | grep deploy
deploy:x:1000:1000:Deploy User:/home/deploy:/bin/bash
$ sudo cat /etc/shadow | grep deploy
deploy:$6$abc123...:19500:0:99999:7:::
```

Note the `sudo` required for the second command — a direct, visible demonstration of the access
difference between the two files.

## Do / Don't

| Do | Don't |
|---|---|
| Use `/etc/passwd` for account lookups that don't need password data | Try to read `/etc/shadow` without root — expect it to fail by design |
| Understand password aging policy lives in `/etc/shadow`, not `/etc/passwd` | Assume all account configuration lives in one file |

## Common mistakes

- Assuming `/etc/passwd` still contains password hashes (it hasn't, on any modern system, for
  decades) — the `x` placeholder is the signal that shadow passwords are in use.
- Not realizing `/etc/shadow`'s restrictive permissions are the actual security control here —
  `/etc/passwd` being world-readable is intentional and not itself a vulnerability.
- Editing either file directly instead of using the proper commands (`useradd`, `usermod`,
  `passwd`) — direct edits risk syntax errors that can break login entirely.

## Best practices

- Never edit `/etc/passwd` or `/etc/shadow` directly — use `useradd`/`usermod`/`passwd`, which
  validate and update both files safely and consistently.
- When auditing account security, check `/etc/shadow` specifically for password aging and empty
  hash fields, not `/etc/passwd`.

## Exercises

1. Read your own entry in `/etc/passwd` and identify each field.
2. Attempt to read `/etc/shadow` without `sudo` and observe the permission denied error.
3. Explain in one sentence why splitting these two files improved security over the historical
   single-file approach.

## Quiz

**Q: What does the `x` in the password field of `/etc/passwd` mean?**
<details><summary>Show answer</summary>
It's a placeholder indicating the actual password hash is stored in `/etc/shadow` instead —
not a real password value.
</details>

**Q: Why is `/etc/passwd` world-readable while `/etc/shadow` isn't?**
<details><summary>Show answer</summary>
Many legitimate system operations need to look up account metadata (username, UID, shell)
without needing root, while password hashes specifically need much tighter protection against
offline cracking — splitting the files serves both needs simultaneously.
</details>

## Interview questions

- Why was the password hash moved out of `/etc/passwd` and into `/etc/shadow`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/etc/passwd` holds account metadata and is world-readable by design.
- `/etc/shadow` holds password hashes and aging policy, restricted to root.
- The `x` in `/etc/passwd`'s password field signals shadow passwords are in use.
- Never edit either file directly — use the proper account management commands.

## Further Reading

- [passwd(5) man page](https://man7.org/linux/man-pages/man5/passwd.5.html)

## Related topics

- [useradd, usermod, userdel](useradd-usermod-userdel.md)
