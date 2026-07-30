---
title: "umask — View or Set the Default Permission Mask"
description: "Display or change the umask value that determines default permissions for newly created files and directories."
relatedConcepts: ["07-permissions/umask"]
relatedCommands: ["chmod"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "good-to-know"
updatedAt: "2026-07-25"
keywords: ["umask command", "umask 022", "set umask bash"]
canonicalUrl: "/commands/umask"
---

# umask

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `umask` with no arguments shows the current value. `umask 022` sets it for the
> current shell session — put it in `.bashrc`/`/etc/profile` to make it persistent.

## Purpose

`umask` displays or sets the permission mask applied to newly created files and directories — see
[umask](../docs/07-permissions/umask.md) for the full subtraction mechanics.

## Syntax

```
umask [MODE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `MODE` | New umask value in octal (e.g. `022`) | No (omit to display current value) |

## Options

`umask` is a shell built-in with no commonly used flags beyond the optional mode argument.

## Examples

```
$ umask
```
Show the current umask.

```
$ umask 077
```
Set a stricter umask for the current shell session.

## Expected Output

```
$ umask
0022
```

## Exit Status

`0` on success.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| umask change doesn't persist to new sessions | Set only in the current shell, not in a config file | Add the `umask` line to `.bashrc` or `/etc/profile` (Module 04) |
| Newly created files still seem too permissive | Umask was changed after files were already created | Umask only affects files created after the change — existing files need `chmod` directly |

## Security Considerations

A misconfigured, overly permissive umask (like `000`) silently affects every file created in that
session or system-wide — a subtle but real security exposure worth auditing on shared/service
accounts.

## Performance Considerations

Not applicable.

## Production Usage

Service accounts running production applications often set a specific `umask` (frequently `027`
or `077` for anything handling sensitive data) in their startup environment, so every file the
application creates at runtime has appropriately restrictive default permissions without the
application itself needing to `chmod` anything explicitly.

## Related Commands

- [`chmod`](chmod.md) — change permissions on existing files (umask only affects new ones)

## Related Concepts

- [umask](../docs/07-permissions/umask.md)
