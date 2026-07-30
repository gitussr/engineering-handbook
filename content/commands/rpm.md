---
title: "rpm — Install and Query .rpm Package Files Directly"
description: "The low-level RHEL-family package tool — operates on a single, already-downloaded .rpm file, with no dependency resolution."
relatedConcepts: ["14-package-managers/yum-dnf-rpm"]
relatedCommands: ["dnf"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
compatibility: [{"context": "RHEL/CentOS/Rocky/Fedora", "note": "Not available on Debian/Ubuntu, which use dpkg instead."}]
updatedAt: "2026-07-27"
keywords: ["rpm -qa examples", "rpm -i install", "rpm query package", "rpm vs dnf"]
canonicalUrl: "/commands/rpm"
---

# rpm

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `rpm -i file.rpm` installs a single, already-downloaded `.rpm` file — with no
> dependency resolution. `rpm -qa` lists all installed packages. Use `dnf` instead for anything
> that needs dependencies fetched.

## Purpose

`rpm` installs and queries `.rpm` package files directly — see
[yum, dnf, rpm](../docs/14-package-managers/yum-dnf-rpm.md) for how it relates to `dnf`.

## Syntax

```
rpm [OPTIONS] [FILE|PACKAGE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | Path to a `.rpm` file (for install) | For `-i` |
| `PACKAGE` | An installed package name (for query/removal) | For query operations |

## Options

| Flag | Meaning |
|---|---|
| `-i FILE` | Install a `.rpm` file |
| `-e NAME` | Remove (erase) an installed package |
| `-qa` | Query all — list every installed package |
| `-qi NAME` | Show detailed info about an installed package |
| `-ql NAME` | List files installed by a package |
| `--checksig FILE` | Verify a package's signature |

## Examples

```
$ sudo rpm -i agent.rpm
```
Install a local `.rpm` file.

```
$ rpm -qa | grep nginx
```
Check whether a specific package is installed.

```
$ rpm -qi nginx
```
Show detailed information about an installed package.

```
$ rpm --checksig agent.rpm
```
Verify a package file's signature before installing it.

## Expected Output

```
$ rpm -qa | grep nginx
nginx-1.24.0-1.el9.x86_64
```

## Exit Status

`0` on success, non-zero if dependencies are unmet (for `-i`) or the package/file doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `failed dependencies` | The `.rpm` file has unmet dependencies | Use `dnf install ./file.rpm` instead, which resolves dependencies from repositories |
| `package ... is already installed` | Attempting to reinstall without `--force`/`-U` | Use `rpm -U` (upgrade) instead of `-i` if replacing an existing install |
| `NOKEY` warning during `--checksig` | The signing key isn't imported/trusted locally | Import the correct public key from a trusted source before proceeding |

## Security Considerations

`rpm -i` performs signature checks if a key is available, but doesn't fetch or verify against a
repository's trust configuration the way `dnf` does — only install `.rpm` files from sources you
trust directly, and use `--checksig` to verify before installing when in doubt.

## Performance Considerations

Negligible — operates on a single package file or query at a time.

## Compatibility Notes

`rpm` is specific to RHEL-family distros; Debian/Ubuntu use [`dpkg`](dpkg.md) instead, with a very
similar low-level role.

## Production Usage

`rpm -qa`/`rpm -qi` are the standard tools for auditing exactly what's installed on a system,
independent of whether `dnf` was involved in a given install.

## Related Commands

- [`dnf`](dnf.md) — the high-level tool that calls `rpm` underneath and resolves dependencies

## Related Concepts

- [yum, dnf, rpm](../docs/14-package-managers/yum-dnf-rpm.md)
