---
title: "apt — Debian/Ubuntu Package Manager"
description: "The high-level tool for installing, updating, and removing .deb packages with automatic dependency resolution from configured repositories."
relatedConcepts: ["14-package-managers/apt-dpkg", "14-package-managers/package-management-overview"]
relatedCommands: ["dpkg"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
difficulty: "must-know"
compatibility: [{"context": "Debian/Ubuntu", "note": "Not available on RHEL-family distros, which use dnf instead."}]
updatedAt: "2026-07-27"
keywords: ["apt install examples", "apt update upgrade", "apt remove purge", "apt command linux"]
canonicalUrl: "/commands/apt"
---

# apt

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `apt update` refreshes the package index. `apt install name` installs a package plus
> dependencies. `apt upgrade` upgrades everything installed. Always `update` before
> `install`/`upgrade`.

## Purpose

`apt` installs, updates, and removes `.deb` packages with automatic dependency resolution — see
[apt, dpkg](../docs/14-package-managers/apt-dpkg.md) for how it relates to `dpkg`.

## Syntax

```
apt [OPTIONS] COMMAND [PACKAGE...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The action: `update`, `install`, `remove`, `upgrade`, etc. | Yes |
| `PACKAGE` | One or more package names | Depends on command |

## Options

| Flag | Meaning |
|---|---|
| `update` | Refresh the local package index from configured repositories |
| `upgrade` | Upgrade all installed packages to their latest available version |
| `install NAME` | Install a package plus its dependencies |
| `remove NAME` | Remove a package, keeping its configuration files |
| `purge NAME` | Remove a package and its configuration files |
| `search TERM` | Search available packages by name/description |
| `-y` | Assume "yes" to prompts (useful in scripts) |

## Examples

```
$ sudo apt update
```
Refresh the package index.

```
$ sudo apt install nginx
```
Install nginx and its dependencies.

```
$ sudo apt upgrade -y
```
Upgrade all installed packages, without interactive confirmation.

```
$ sudo apt purge nginx
```
Remove nginx along with its configuration files.

## Expected Output

```
$ sudo apt install nginx
Reading package lists... Done
The following additional packages will be installed:
  libnginx-mod-http-image-filter
...
Setting up nginx (1.24.0-2ubuntu7) ...
```

## Exit Status

`0` on success, non-zero on a failed install/removal or unmet dependencies that can't be
resolved.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Unable to locate package` | Package index is stale, or the package name/repository is wrong | Run `apt update` first; verify the package name |
| `Could not get lock` | Another package manager process is running | Wait for it to finish, or check for a stuck process holding the lock |
| Signature verification failure | Repository's signing key not trusted (Module 14's later topic) | Investigate — don't disable verification to work around it |

## Security Considerations

Never disable signature verification to work around an installation error — see
[Package Signing and Verification](../docs/14-package-managers/package-signing-and-verification.md).
Only add repositories you genuinely trust.

## Performance Considerations

`apt update` against many configured repositories takes longer with more sources; scripts running
frequent installs should combine operations rather than calling `update` redundantly in a tight
loop.

## Compatibility Notes

`apt` is specific to Debian/Ubuntu-family distros; RHEL-family distros use
[`dnf`](dnf.md) instead.

## Production Usage

`apt update && apt install -y package` is the standard non-interactive pattern used in Dockerfiles
and provisioning scripts targeting Debian-based images.

## Related Commands

- [`dpkg`](dpkg.md) — the low-level tool `apt` calls underneath

## Related Concepts

- [apt, dpkg](../docs/14-package-managers/apt-dpkg.md)
- [Package Management Overview](../docs/14-package-managers/package-management-overview.md)
