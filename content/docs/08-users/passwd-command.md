---
title: "The passwd Command"
description: "Setting and changing account passwords — for yourself, or (as root) for anyone else — and what a locked or expired account looks like."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["08-users/useradd-usermod-userdel"]
relatedTopics: ["su-vs-sudo"]
relatedCommands: ["passwd"]
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#passwd-command"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "08-users/su-vs-sudo"
prevTopic: "08-users/useradd-usermod-userdel"
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["passwd command linux", "change password linux", "passwd -l lock account"]
canonicalUrl: "/docs/users/passwd-command"
---

# The passwd Command

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `passwd` alone changes your own password. `sudo passwd username` sets another
> user's password (root privilege required). `passwd -l`/`-u` locks/unlocks an account without
> deleting it.

## What is it?

`passwd` is the command that actually updates the hashed password stored in
[`/etc/shadow`](etc-passwd-and-etc-shadow.md) — the mechanism behind
`/usr/bin/passwd`'s SUID bit, covered in [Module 07's SUID topic](../07-permissions/suid-sgid-sticky-bit.md),
which is exactly what allows a regular user to update their own password despite `/etc/shadow`
being root-only.

## Why does it exist?

Users need to set and change their own passwords without needing direct root access to
`/etc/shadow` — and administrators need a safe way to set or reset other users' passwords, lock
compromised accounts, or enforce password policy, without editing the shadow file by hand.

## Where is it used?

- Setting a password immediately after `useradd` creates a new account (accounts are locked/
  unusable until a password is set)
- Users changing their own password routinely
- Administrators resetting a forgotten password or locking a compromised account

## How it works

> 📊 Diagram: `passwd`'s behavior branching on who runs it and with what argument — no argument
> as a regular user (changes your own password, prompts for current password first), `sudo
> passwd username` (sets another user's password directly, no current-password prompt needed).

```
passwd                    # change your own password (prompts for current password first)
sudo passwd username      # set another user's password (root — no current password needed)
sudo passwd -l username    # lock an account (disable password login)
sudo passwd -u username    # unlock a previously locked account
sudo passwd -e username    # force password change at next login
```

## Real-world example

A new hire's account is created with `useradd -m -s /bin/bash newhire`, but the account is locked
and unusable until `sudo passwd newhire` sets an initial password — often paired with `passwd -e`
to force the new hire to set their own password on first login rather than keeping the
admin-set one.

## Commands

- [`passwd`](../../commands/passwd.md) — full syntax and examples

## Production example

```
$ sudo useradd -m -s /bin/bash newhire
$ sudo passwd newhire
New password:
Retype new password:
passwd: password updated successfully
$ sudo passwd -e newhire
```

## Do / Don't

| Do | Don't |
|---|---|
| Set a password immediately after creating an account | Leave a newly created account with no password set, assuming it's automatically usable |
| Use `passwd -l` to quickly lock a suspicious account | Reach straight for `userdel` when a temporary lock would preserve investigation options |
| Force a password change (`-e`) after an admin-set initial password | Let a user keep an admin-known initial password indefinitely |

## Common mistakes

- Assuming a freshly `useradd`-created account is immediately loginable — it's locked until
  `passwd` sets an actual password.
- Deleting an account (`userdel`) when locking it (`passwd -l` or `usermod -L`) would have
  preserved useful investigation/audit context.
- Not forcing a password change after setting an initial admin-known password, leaving a
  known-to-others credential in place indefinitely.

## Best practices

- Always set a password (or explicitly configure key-based access instead, Module 13) immediately
  after creating an account.
- Use `passwd -e` after setting an initial password for a new user, so they immediately set their
  own.
- Prefer locking (`passwd -l`) over deletion when investigating a potentially compromised account.

## Exercises

1. Change your own password using `passwd` (in a safe test environment).
2. As root/sudo, set another test user's password.
3. Lock and then unlock a test account using `passwd -l` and `passwd -u`.

## Quiz

**Q: Why can a regular user change their own password despite `/etc/shadow` being root-only?**
<details><summary>Show answer</summary>
`/usr/bin/passwd` has the SUID bit set, so it runs with root's privileges momentarily — just long
enough to update the user's own entry in `/etc/shadow` — covered in Module 07.
</details>

**Q: What does `passwd -l` do?**
<details><summary>Show answer</summary>
Locks the account, disabling password-based login, without deleting the account or its data.
</details>

## Interview questions

- How would you temporarily disable a compromised account without deleting it? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `passwd` alone changes your own password; `sudo passwd username` sets another user's.
- New accounts are locked/unusable until a password is set.
- `passwd -l`/`-u` lock and unlock accounts without deleting them — useful during investigation.
- This command's SUID bit is the real reason non-root users can manage their own password at all.

## Related topics

- [su vs sudo](su-vs-sudo.md)
- [Module 07: SUID, SGID, Sticky Bit](../07-permissions/suid-sgid-sticky-bit.md)
