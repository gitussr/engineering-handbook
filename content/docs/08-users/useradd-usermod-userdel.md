---
title: "useradd, usermod, userdel"
description: "The three commands covering a user account's entire lifecycle — create, modify, remove — and the flags that matter most in each."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["08-users/etc-passwd-and-etc-shadow"]
relatedTopics: ["passwd-command"]
relatedCommands: ["useradd", "usermod", "userdel"]
careerRelevance: ["devops", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#useradd-flags"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "08-users/passwd-command"
prevTopic: "08-users/etc-passwd-and-etc-shadow"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["useradd command", "usermod command", "userdel command", "create user linux", "delete user and home directory"]
canonicalUrl: "/docs/users/useradd-usermod-userdel"
---

# useradd, usermod, userdel

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `useradd -m -s /bin/bash username` creates a user with a home directory and shell.
> `usermod` modifies an existing account (most often `-aG group` to add a group membership).
> `userdel -r username` removes an account and its home directory — without `-r`, the home
> directory is left behind.

## What is it?

The three commands that safely create, modify, and remove entries in `/etc/passwd` and
`/etc/shadow` (Module 08's previous topic) — you should never edit those files directly; these
commands do it correctly and consistently.

## Why does it exist?

Directly editing `/etc/passwd`/`/etc/shadow` risks syntax errors that can break login for
everyone, and misses related setup (creating a home directory, setting default shell, adding to
initial groups). These three commands handle the full lifecycle safely and consistently.

## Where is it used?

Every time an account needs to be provisioned, adjusted (added to a group, shell changed), or
removed — onboarding/offboarding, service account setup, cleaning up unused accounts during a
security review.

## How it works

> 📊 Diagram: a lifecycle arrow — "useradd (create)" → "usermod (modify, any number of times)" →
> "userdel (remove)" — with a callout on `userdel -r` specifically, since it's the one flag
> beginners most often forget and later regret forgetting.

| Command | Purpose | Key flags |
|---|---|---|
| `useradd` | Create a new account | `-m` (create home directory), `-s` (login shell), `-G` (supplementary groups) |
| `usermod` | Modify an existing account | `-aG group` (add to a group — the `-a` matters, see below), `-s` (change shell), `-L`/`-U` (lock/unlock) |
| `userdel` | Remove an account | `-r` (also remove home directory and mail spool) |

**The `usermod -aG` gotcha**: `-a` means "append." `usermod -G group username` **without** `-a`
*replaces* all of a user's supplementary group memberships with just the one specified — a
common, damaging mistake. Always use `-aG`, never bare `-G`, when adding a group.

## Real-world example

An engineer onboarding a new team member runs `useradd -m -s /bin/bash newhire` to create the
account with a home directory and bash shell, then `usermod -aG developers newhire` to add them
to the team's group — using `-aG`, not bare `-G`, specifically to avoid wiping out any other
group memberships that might already exist (relevant for accounts created with initial group
assignments).

## Commands

- [`useradd`](../../commands/useradd.md) — full syntax and examples
- [`usermod`](../../commands/usermod.md) — full syntax and examples
- [`userdel`](../../commands/userdel.md) — full syntax and examples

## Production example

```
$ sudo useradd -m -s /bin/bash newhire
$ sudo usermod -aG developers newhire
$ sudo userdel -r newhire
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `-aG` (append) when adding a user to a group | Use bare `-G` and accidentally wipe existing group memberships |
| Use `userdel -r` when the home directory should also be removed | Forget `-r` and leave orphaned home directories accumulating over time |
| Use `-m` with `useradd` to create a home directory | Assume a home directory is created automatically without `-m` on every distro |

## Common mistakes

- Using `usermod -G group` (no `-a`) and unintentionally removing a user from every other group
  they belonged to — a genuinely damaging, hard-to-notice mistake.
- Forgetting `userdel -r`, leaving home directories and mail spools behind indefinitely after an
  account is removed.
- Forgetting `-m` with `useradd` on distros where it's not the default, resulting in a user with
  no home directory at all.

## Best practices

- Always use `-aG`, never bare `-G`, when adding group memberships to an existing user.
- Decide deliberately whether `userdel -r` is appropriate — sometimes preserving the home
  directory for a short retention period before actual deletion is the safer organizational
  policy.
- Verify account creation with `id username` or by checking `/etc/passwd` after running
  `useradd`, rather than assuming success.

## Exercises

1. Create a test user with a home directory and bash shell using `useradd -m -s /bin/bash`.
2. Add that user to a group using `usermod -aG`, then verify with `id`.
3. Remove the test user and its home directory using `userdel -r`.

## Quiz

**Q: What's the difference between `usermod -G group user` and `usermod -aG group user`?**
<details><summary>Show answer</summary>
Without `-a`, `-G` replaces all of the user's supplementary group memberships with just the
specified group. With `-a` (append), it adds the group while preserving existing memberships.
</details>

**Q: What does `userdel -r` do that plain `userdel` doesn't?**
<details><summary>Show answer</summary>
It also removes the user's home directory and mail spool. Without `-r`, the account is deleted
but the home directory is left behind.
</details>

## Interview questions

- Why is `usermod -aG` preferred over `usermod -G` when adding a user to a group? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `useradd`, `usermod`, `userdel` cover an account's full lifecycle — never edit `/etc/passwd`
  directly.
- `usermod -aG` (append) vs. bare `-G` (replace) is a critical, easy-to-get-wrong distinction.
- `userdel -r` removes the home directory too; without it, home directories accumulate.

## Related topics

- [The passwd Command](passwd-command.md)
- [Module 09: Groups](../09-groups/index.md)
