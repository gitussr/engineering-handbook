---
title: "Users"
description: "Module 08 of the Linux roadmap — user accounts, /etc/passwd and /etc/shadow, the account lifecycle commands, su vs sudo, sudoers, and account hardening."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
type: "module-index"
nextTopic: "08-users/user-accounts-overview"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/users"
---

# Users

Module 08 of 34 · Stage: Beginner · Previous: [07 Permissions](../07-permissions/index.md)

Module 07 explained who a permission scope applies to; this module explains what a user account
actually is, how the system tracks accounts and their credentials, and the two ways to act with
elevated privileges — `su` and `sudo` — plus why one is generally preferred over the other in
modern practice.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [User Accounts Overview](user-accounts-overview.md) | 🟢 Must Know |
| [/etc/passwd and /etc/shadow](etc-passwd-and-etc-shadow.md) | 🟢 Must Know |
| [useradd, usermod, userdel](useradd-usermod-userdel.md) | 🟢 Must Know |
| [The passwd Command](passwd-command.md) | 🟢 Must Know |
| [su vs sudo](su-vs-sudo.md) | 🟢 Must Know |
| [The sudoers File and visudo](sudoers-and-visudo.md) | 🟢 Must Know |
| [Root Account Best Practices](root-account-best-practices.md) | 🟡 Good to Know |
| [User Account Security Hardening](user-account-security-hardening.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain what a user account is, including the difference between system and regular users.
- Read `/etc/passwd` and explain why `/etc/shadow` exists separately.
- Create, modify, and delete user accounts with `useradd`, `usermod`, `userdel`.
- Set and change passwords correctly and safely.
- Explain the real difference between `su` and `sudo`, and when each is appropriate.
- Edit `/etc/sudoers` safely using `visudo`, and explain why that matters.
- Apply root account best practices, including why direct root login is generally disabled.
- Describe basic account hardening measures beyond the defaults.

## Known, intentional gaps in this module

- Canonical command pages exist for `useradd`, `usermod`, `userdel`, `passwd`, `su`, and `sudo` —
  every account-lifecycle and privilege-elevation command this module centers on.
- `visudo` is taught in full within its topic page but doesn't yet have a separate canonical
  command page — it's a thin wrapper around editing one file safely, not a broad command surface.

**Previous module:** [07 Permissions](../07-permissions/index.md)
**Next module:** [09 Groups →](../09-groups/index.md)
