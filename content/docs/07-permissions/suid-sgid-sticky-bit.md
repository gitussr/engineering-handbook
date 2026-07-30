---
title: "SUID, SGID, Sticky Bit"
description: "Three special permission bits that change what execute means — run as the file's owner, inherit a directory's group, or restrict deletion to the file's own owner."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["07-permissions/umask"]
relatedTopics: ["acls"]
relatedCommands: ["chmod"]
careerRelevance: ["linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#suid-explained"]
relatedCheatsheet: "permissions"
furtherReading: []
nextTopic: "07-permissions/acls"
prevTopic: "07-permissions/umask"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["suid explained", "sgid explained", "sticky bit tmp", "chmod 4755", "suid security risk"]
canonicalUrl: "/docs/permissions/suid-sgid-sticky-bit"
---

# SUID, SGID, Sticky Bit

🔴 Expert · Relevant for: Linux Administrator · Cybersecurity

> **TL;DR:** SUID makes a program run with its owner's privileges, not the invoking user's — how
> `passwd` lets any user update a root-owned file safely. SGID does the same for group, and on a
> directory makes new files inherit its group. The sticky bit (seen on `/tmp`) restricts deletion
> to a file's own owner, even when the directory is writable by everyone.

## What is it?

Three special permission bits, beyond the standard `rwx` for owner/group/other: **SUID** (Set
User ID), **SGID** (Set Group ID), and the **sticky bit** — each changes normal behavior in a
specific, narrow way.

## Why does it exist?

Some operations need a controlled privilege escalation — a normal user running `passwd` needs to
modify `/etc/shadow`, a file only root can normally write. Rather than giving every user broad
root access, SUID lets a specific, trusted program run with its owner's (root's) privileges just
for that operation. SGID and the sticky bit solve related but distinct problems around group
inheritance and shared-directory deletion control.

## Where is it used?

- **SUID**: `/usr/bin/passwd` is the canonical example — runs as root momentarily so any user can
  update their own password, which requires writing to a root-owned file.
- **SGID on a directory**: shared team directories, so every new file automatically inherits the
  directory's group instead of the creating user's primary group.
- **Sticky bit**: `/tmp` is the canonical example — world-writable, but each user can only delete
  their own files within it, not anyone else's.

## How it works

> 📊 Diagram: three `ls -l` permission strings shown side by side, each with the special bit
> highlighted in its unusual position — `-rwsr-xr-x` (SUID, `s` replacing owner's `x`),
> `-rwxr-sr-x` (SGID, `s` replacing group's `x`), `drwxrwxrwt` (sticky bit, `t` replacing other's
> `x`) — showing exactly where each bit appears in the permission string.

| Bit | Octal prefix | On a file | On a directory |
|---|---|---|---|
| SUID | `4` (e.g. `4755`) | Runs with the file owner's privileges, not the invoking user's | No effect (ignored) |
| SGID | `2` (e.g. `2755`) | Runs with the file's group privileges | New files/subdirectories inherit the directory's group automatically |
| Sticky | `1` (e.g. `1755`) | Historically affected swap behavior (obsolete on modern Linux) | Only the file's own owner (or root) can delete/rename files inside, even if the directory itself is world-writable |

Set with a fourth leading octal digit: `chmod 4755 file` (SUID), `chmod 2755 directory` (SGID),
`chmod 1755 directory` (sticky). In `ls -l`, they appear as a lowercase `s`/`s`/`t` replacing the
normal `x` in the relevant position (or uppercase `S`/`T` if the underlying execute bit isn't
also set).

## Real-world example

`/usr/bin/passwd` is owned by root and has SUID set (`-rwsr-xr-x`). When a regular user runs
`passwd` to change their own password, the program executes with root's privileges just long
enough to write the updated hash to `/etc/shadow` (a file only root can normally write) — then
returns to normal user privileges. This is precisely why `passwd` works for regular users at all
without giving them direct root access to sensitive files.

## Commands

- [`chmod`](../../commands/chmod.md) — sets SUID/SGID/sticky bit via the fourth octal digit

## Production example

```
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 Jan  1 2026 /usr/bin/passwd
$ ls -ld /tmp
drwxrwxrwt 1 root root 4096 Jul 25 09:00 /tmp
$ chmod 2775 /opt/shared-team-dir
```

## Do / Don't

| Do | Don't |
|---|---|
| Audit SUID binaries periodically on security-sensitive systems | Set SUID on a custom script without understanding the privilege-escalation implications |
| Use SGID on shared team directories for automatic group inheritance | Assume SGID on a file behaves the same as SGID on a directory |
| Rely on the sticky bit for shared world-writable directories like `/tmp` | Assume a world-writable directory without the sticky bit is safe from other users deleting your files |

## Common mistakes

- Setting SUID on a custom script (especially a shell script) without understanding that this is
  a significant, often discouraged privilege-escalation mechanism — many systems disable SUID on
  scripts specifically because of the security risk.
- Confusing SGID's effect on files (runs with group privileges) versus directories (new files
  inherit the group) — genuinely different behaviors depending on file type.
- Assuming a world-writable directory without a sticky bit protects users' files from deletion by
  others — without the sticky bit, anyone with write access can delete anyone else's files there.

## Best practices

- Periodically audit for unexpected SUID binaries (`find / -perm -4000`) on security-sensitive
  systems — an unexpected SUID binary is a classic privilege-escalation attack vector worth
  actively monitoring, directly relevant to [Module 19: Security](../19-security/index.md) and
  [Module 27: Linux for Cybersecurity](../27-linux-for-cybersecurity/index.md).
- Use SGID specifically for shared team directories where consistent group ownership on new files
  matters.
- Always pair a world-writable shared directory with the sticky bit, following `/tmp`'s example.

## Exercises

1. Find `passwd`'s permissions with `ls -l /usr/bin/passwd` and identify the SUID bit.
2. Find `/tmp`'s permissions and identify the sticky bit.
3. Explain in one sentence why SUID on a custom script is considered a security risk.

## Quiz

**Q: What does SUID actually do when a program runs?**
<details><summary>Show answer</summary>
The program runs with the privileges of its file owner, not the user who invoked it — the
mechanism that lets `passwd` write to root-owned files on behalf of a regular user.
</details>

**Q: What does the sticky bit on `/tmp` prevent?**
<details><summary>Show answer</summary>
It prevents users from deleting or renaming files they don't own, even though `/tmp` itself is
world-writable — each user can only remove their own files.
</details>

## Interview questions

- Why does `/usr/bin/passwd` need SUID, and what would happen without it? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- SUID runs a program with its owner's privileges — `passwd` is the canonical example.
- SGID on a directory makes new files inherit the directory's group automatically.
- The sticky bit restricts deletion to a file's own owner within a shared, writable directory.
- SUID binaries are a real security audit concern — an unexpected one is a red flag.

## Related topics

- [ACLs: setfacl, getfacl](acls.md)
- [Module 19: Security](../19-security/index.md)
