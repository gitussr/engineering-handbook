---
title: "The sudoers File and visudo"
description: "Controlling exactly who can run what via sudo — the /etc/sudoers file, its syntax, and why visudo is the only safe way to edit it."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["08-users/su-vs-sudo"]
relatedTopics: ["root-account-best-practices"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#visudo-purpose"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "sudoers(5) man page", "url": "https://man7.org/linux/man-pages/man5/sudoers.5.html"}]
nextTopic: "08-users/root-account-best-practices"
prevTopic: "08-users/su-vs-sudo"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["visudo command", "etc sudoers syntax", "sudoers file example", "sudo group configuration"]
canonicalUrl: "/docs/users/sudoers-and-visudo"
---

# The sudoers File and visudo

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** `/etc/sudoers` defines who can run what via `sudo`. Always edit it with `visudo`,
> never a regular text editor — `visudo` validates syntax before saving, and a broken sudoers
> file directly edited can lock out sudo access entirely, including your own.

## What is it?

`/etc/sudoers` is the configuration file that defines exactly who can use `sudo`, for which
commands, as which users, and whether a password is required each time. `visudo` is the dedicated
tool for editing it safely.

## Why does it exist?

`sudo`'s security value ([su vs sudo](su-vs-sudo.md)) depends entirely on precise, correct
configuration — a syntax error in `sudoers` can silently break sudo access for everyone,
including whoever needs to fix it. `visudo` exists specifically to prevent that failure mode by
validating syntax before the file is actually saved.

## Where is it used?

Every time sudo access needs to be granted, restricted, or reviewed — onboarding a new admin,
scoping a service account to a narrow set of allowed commands, or auditing who currently has
what level of access.

## How it works

> 📊 Diagram: `visudo`'s safety flow — open a working copy → edit → attempt to save → syntax
> validated → if valid, the real `/etc/sudoers` is replaced; if invalid, the edit is rejected and
> the original file is left untouched — contrasted with a direct edit in a regular editor, where
> a syntax error is saved immediately with no validation.

Common sudoers entry patterns:

```
deploy ALL=(ALL) ALL              # deploy can run any command, as any user, on any host
%developers ALL=(ALL) ALL          # everyone in the developers group gets the same
backupuser ALL=(root) /usr/bin/rsync   # backupuser can run ONLY rsync, as root — nothing else
deploy ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx  # no password prompt for this one command
```

The general form is `who where=(as-whom) what` — reading left to right: which user or `%group`,
on which hosts (almost always `ALL`), running as which target user, allowed to run which
commands (`ALL` for everything, or a specific path to restrict tightly).

## Real-world example

A company grants a backup service account sudo access scoped to exactly one command
(`/usr/bin/rsync`) rather than full `ALL` access — so even if that account is ever compromised,
the attacker's `sudo` capability is limited to running rsync, not arbitrary root commands. This
kind of narrow scoping is only practical because `sudoers` syntax supports it directly, and
`visudo` makes editing it safely straightforward.

## Commands

No new canonical command page on this page — `visudo` is a thin, single-purpose wrapper around
safely editing one file, covered here as a topic rather than a full command reference.

## Production example

```
$ sudo visudo
# opens /etc/sudoers (or a working copy) in your default editor
# add: deploy ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx
# save — visudo validates syntax before committing the change
```

## Do / Don't

| Do | Don't |
|---|---|
| Always use `visudo` to edit sudoers | Edit `/etc/sudoers` directly with `nano`/`vim` |
| Scope service account sudo access narrowly (specific commands) | Grant blanket `ALL` access when a narrow command list would do |
| Use `%groupname` to grant access to a whole team at once | Add every team member individually when a group entry would be cleaner |

## Common mistakes

- Editing `/etc/sudoers` directly with a regular editor and introducing a syntax error, which can
  lock out sudo access system-wide, including for the person who made the mistake.
- Granting blanket `ALL=(ALL) ALL` access to service accounts that only ever need one specific
  command — unnecessarily broad and a real security risk if that account is compromised.
- Not using `%groupname` entries, instead managing sudo access user by user and letting it drift
  out of sync with actual team membership.

## Best practices

- Always use `visudo`, without exception — the syntax validation is the entire point.
- Scope service account sudo access to the narrowest set of commands that actually satisfies the
  need.
- Use group-based sudoers entries (`%developers`) rather than individual per-user entries where
  the access genuinely applies to a whole team.

## Exercises

1. Run `sudo visudo` and locate an existing sudoers entry (don't change anything yet).
2. Explain in one sentence why `visudo` prevents a class of failure that direct editing doesn't.
3. Write a hypothetical sudoers line granting a service account access to exactly one command.

## Quiz

**Q: Why must `/etc/sudoers` be edited with `visudo` instead of a regular editor?**
<details><summary>Show answer</summary>
`visudo` validates the file's syntax before saving; a direct edit with a regular editor can save
a syntax error immediately, potentially breaking sudo access for everyone, including root.
</details>

**Q: What does scoping a sudoers entry to a specific command instead of `ALL` accomplish?**
<details><summary>Show answer</summary>
It limits the blast radius if that account is ever compromised — the attacker's `sudo` capability
is restricted to exactly what was granted, not arbitrary root access.
</details>

## Interview questions

- Why is `visudo` required for editing `/etc/sudoers`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/etc/sudoers` controls exactly who can run what via `sudo`.
- `visudo` validates syntax before saving — the only safe way to edit this file.
- Scope service accounts to specific commands, not blanket `ALL` access.
- `%groupname` entries keep sudo access in sync with team membership more cleanly than per-user
  entries.

## Further Reading

- [sudoers(5) man page](https://man7.org/linux/man-pages/man5/sudoers.5.html)

## Related topics

- [Root Account Best Practices](root-account-best-practices.md)
