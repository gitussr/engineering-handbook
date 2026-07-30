---
title: "su vs sudo"
description: "su switches your entire session to another user; sudo runs one command with elevated privilege. The difference matters for security and auditability, not just convenience."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["08-users/passwd-command"]
relatedTopics: ["sudoers-and-visudo"]
relatedCommands: ["su", "sudo"]
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#su-vs-sudo"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "08-users/sudoers-and-visudo"
prevTopic: "08-users/passwd-command"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["su vs sudo difference", "sudo su", "su - dash", "sudo audit log"]
canonicalUrl: "/docs/users/su-vs-sudo"
---

# su vs sudo

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** `su` switches your entire session to another user (needs *that user's* password).
> `sudo` runs a single command as another user, usually root (needs *your own* password). `sudo`
> is generally preferred in modern practice because it's per-command, auditable, and doesn't
> require sharing the root password at all.

## What is it?

Two different ways to act with another user's (usually root's) privileges: `su` ("switch user")
starts an entirely new login session as that user; `sudo` ("superuser do") runs a single specified
command with elevated privileges, then returns you to your normal session immediately.

## Why does it exist?

Both solve "I need elevated privileges," but with very different security properties. `su`
predates `sudo` and requires knowing the target account's actual password — meaning multiple
admins sharing root access all need the same shared root password, with no way to tell who did
what afterward. `sudo` solves both problems: each admin authenticates with their *own* password,
and every command run through `sudo` is logged with who ran it.

## Where is it used?

- `sudo` — the standard way to run individual privileged commands in modern practice: `sudo apt
  update`, `sudo systemctl restart nginx`
- `su` — less common now, but still used to fully switch into another account's session (e.g.
  `su - serviceaccount` to test as that account, or `su` where `sudo` isn't configured at all)

## How it works

> 📊 Diagram: two flows side by side — `su`: enter target user's password → entire new session as
> that user, everything you do afterward is "as them" with no per-command record. `sudo command`:
> enter your own password → one command runs elevated → immediately back to your normal session,
> logged.

| | `su` | `sudo` |
|---|---|---|
| Password required | The target account's password | Your own password |
| Scope | Entire new session | One command (unless `sudo -i` / `sudo su`) |
| Audit trail | None built in — no record of who did what | Every command logged (typically to `/var/log/auth.log` or `/var/log/secure`) |
| Requires knowing root's actual password | Yes | No — never needs to know or share it |

`su -` (with the dash) starts a full login shell as the target user, including their environment;
`su` without the dash keeps your current environment, which can cause subtle, confusing bugs — the
dash matters more than it looks like it should.

## Real-world example

A team of five engineers all need occasional root access to a server. With `su`, they'd all need
to know and share the same root password, with zero record of who actually ran what if something
goes wrong. With `sudo` (configured per Module 08's next topic), each engineer authenticates with
their own account, every privileged command is individually logged, and root's actual password
never needs to be shared or even known by anyone.

## Commands

- [`su`](../../commands/su.md) — full syntax and examples
- [`sudo`](../../commands/sudo.md) — full syntax and examples

## Production example

```
$ sudo systemctl restart nginx
[sudo] password for deploy:
$ su - serviceaccount
Password:
serviceaccount@server:~$
```

## Do / Don't

| Do | Don't |
|---|---|
| Default to `sudo` for individual privileged commands | Share the root password across a team so everyone can `su` |
| Use `su -` (with dash) for a genuinely full session switch | Use `su` without the dash and get a confusing mixed environment |
| Configure `sudo` per-user for auditability | Rely on `su` as the only privilege-elevation mechanism on a team-managed server |

## Common mistakes

- Sharing a root password across a team so everyone can `su` — eliminates any audit trail and is
  a real, common security anti-pattern.
- Using `su` without the dash and getting confusing environment-related bugs from a mixed
  environment (your own environment variables applied to another user's session).
- Not realizing `sudo -i` or `sudo su` effectively gives you a full root session too — `sudo`
  isn't inherently limited to single commands if configured/used that way, which matters for
  understanding the actual security boundary in a given setup.

## Best practices

- Default to `sudo` for privileged commands on any multi-admin system — it's what makes the
  "who did this" question answerable later.
- Never share root's actual password among a team; configure `sudo` per-user instead (Module 08's
  next topic).
- Use `su -` deliberately with the dash whenever a full session switch is genuinely needed.

## Exercises

1. Run a command with `sudo` and check the resulting log entry (commonly `/var/log/auth.log` or
   `/var/log/secure`).
2. Compare `su` and `su -` and observe the environment difference (e.g. `pwd` right after
   switching).
3. Explain in one sentence why `sudo` is generally preferred for team-managed systems.

## Quiz

**Q: Whose password does `sudo` require — yours or the target account's?**
<details><summary>Show answer</summary>
Your own password — this is a key difference from `su`, which requires the target account's
password.
</details>

**Q: Why is `sudo` generally considered more auditable than `su`?**
<details><summary>Show answer</summary>
Every `sudo` command is logged with who ran it, while `su` provides no built-in per-command
record of what was done during the switched session.
</details>

## Interview questions

- Why is `sudo` generally preferred over `su` on a team-managed production server? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `su` switches your entire session to another user, requiring their password.
- `sudo` runs a single command with elevated privileges, requiring your own password, and logs
  every use.
- `sudo` is generally preferred for auditability and for never needing to share root's password.
- `su -` (with dash) matters — it gives a clean, correct environment for the target user.

## Related topics

- [The sudoers File and visudo](sudoers-and-visudo.md)
