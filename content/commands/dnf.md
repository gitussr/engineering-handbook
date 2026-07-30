---
title: "dnf — RHEL-Family Package Manager"
description: "The high-level tool for installing, updating, and removing .rpm packages with automatic dependency resolution — the modern replacement for yum."
relatedConcepts: ["14-package-managers/yum-dnf-rpm", "14-package-managers/package-management-overview"]
relatedCommands: ["rpm"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
difficulty: "must-know"
compatibility: [{"context": "RHEL/CentOS/Rocky/Fedora", "note": "yum is kept as a compatibility alias for dnf on current systems — same flags, same behavior, not a separate tool. Not available on Debian/Ubuntu, which use apt instead."}]
updatedAt: "2026-07-27"
keywords: ["dnf install examples", "dnf update upgrade", "dnf vs yum", "dnf remove"]
canonicalUrl: "/commands/dnf"
---

# dnf

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `dnf install name` installs a package plus dependencies. `dnf update` upgrades
> installed packages. `yum` still works on current systems as a compatibility alias pointing
> directly at `dnf`.

## Purpose

`dnf` installs, updates, and removes `.rpm` packages with automatic dependency resolution — see
[yum, dnf, rpm](../docs/14-package-managers/yum-dnf-rpm.md) for how it relates to `yum` and `rpm`.

## Syntax

```
dnf [OPTIONS] COMMAND [PACKAGE...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The action: `install`, `update`, `remove`, `list`, etc. | Yes |
| `PACKAGE` | One or more package names | Depends on command |

## Options

| Flag | Meaning |
|---|---|
| `install NAME` | Install a package plus its dependencies |
| `update` (or `upgrade`) | Upgrade installed packages |
| `remove NAME` | Remove a package |
| `list installed` | List installed packages |
| `search TERM` | Search available packages |
| `-y` | Assume "yes" to prompts (useful in scripts) |

## Examples

```
$ sudo dnf install nginx
```
Install nginx and its dependencies.

```
$ sudo dnf update -y
```
Upgrade all installed packages, without interactive confirmation.

```
$ dnf list installed | grep nginx
```
Check whether a specific package is installed.

```
$ sudo dnf remove nginx
```
Remove nginx.

## Expected Output

```
$ sudo dnf install nginx
Dependencies resolved.
================================================
 Package    Arch    Version         Repository
================================================
 nginx      x86_64  1:1.24.0-1.el9  appstream
...
Complete!
```

## Exit Status

`0` on success, non-zero on a failed install/removal or unresolvable dependencies.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `No match for argument` | Package name is wrong, or not in any configured repository | Verify the name with `dnf search` |
| `Error: GPG check FAILED` | Signature verification failure (Module 14's later topic) | Investigate the repository/key — don't disable GPG checking to bypass it |
| Lock/another process running | Another `dnf`/`rpm` operation in progress | Wait for it to finish |

## Security Considerations

Never disable GPG checking to work around a signature verification failure — see
[Package Signing and Verification](../docs/14-package-managers/package-signing-and-verification.md).

## Performance Considerations

`dnf`'s dependency resolver is significantly faster than `yum`'s older implementation, especially
noticeable on systems with many configured repositories.

## Compatibility Notes

`yum` is kept as a compatibility alias for `dnf` on current RHEL-family systems — the same
commands and flags work under either name. `dnf` is not available on Debian/Ubuntu, which use
[`apt`](apt.md) instead.

## Production Usage

`dnf install -y package` is the standard non-interactive pattern used in Dockerfiles and
provisioning scripts targeting RHEL-family images.

## Related Commands

- [`rpm`](rpm.md) — the low-level tool `dnf` calls underneath

## Related Concepts

- [yum, dnf, rpm](../docs/14-package-managers/yum-dnf-rpm.md)
