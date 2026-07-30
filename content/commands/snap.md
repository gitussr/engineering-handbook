---
title: "snap — Install Universal, Sandboxed Packages"
description: "Install and manage snap packages — self-contained, sandboxed applications that run consistently across distros."
relatedConcepts: ["14-package-managers/snap-flatpak"]
relatedCommands: ["flatpak"]
careerRelevance: ["devops", "linux-administrator", "platform"]
difficulty: "good-to-know"
compatibility: [{"context": "Ubuntu", "note": "Pre-installed by default on Ubuntu; requires separate installation on most other distros."}]
updatedAt: "2026-07-27"
keywords: ["snap install examples", "snap list", "snap classic confinement", "snap remove"]
canonicalUrl: "/commands/snap"
---

# snap

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · Platform

> **TL;DR:** `snap install name` installs a sandboxed, self-contained package that runs
> identically across distros. `--classic` relaxes sandboxing for applications that need broader
> system access.

## Purpose

`snap` installs and manages "snap" packages — self-contained, sandboxed applications — see
[snap, flatpak](../docs/14-package-managers/snap-flatpak.md) for the full concept and tradeoffs.

## Syntax

```
snap [OPTIONS] COMMAND [PACKAGE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The action: `install`, `remove`, `list`, `refresh`, etc. | Yes |
| `PACKAGE` | The snap package name | Depends on command |

## Options

| Flag | Meaning |
|---|---|
| `install NAME` | Install a snap package |
| `--classic` | Install with reduced sandboxing (for apps that need broader system access) |
| `remove NAME` | Remove a snap package |
| `list` | List installed snaps |
| `refresh` | Update installed snaps (also happens automatically by default) |

## Examples

```
$ sudo snap install code --classic
```
Install an application that needs relaxed sandboxing.

```
$ snap list
```
List installed snap packages.

```
$ sudo snap refresh
```
Manually trigger an update check for installed snaps.

```
$ sudo snap remove code
```
Remove a snap package.

## Expected Output

```
$ snap list
Name    Version   Rev    Tracking       Publisher     Notes
code    1.90.0    145    latest/stable  vscode        classic
```

## Exit Status

`0` on success, non-zero if the package isn't found or the operation fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `error: This revision of snap "X" was published using classic confinement` | Attempted install without `--classic` for an app that requires it | Re-run with `--classic` |
| Application can't access an expected file/device | Sandboxing restricting access | Check the snap's declared interfaces/permissions, or use `--classic` if appropriate |
| `snap` command not found | Not installed on this distro | Install the `snapd` package first |

## Security Considerations

Sandboxing is the core security benefit of `snap` — using `--classic` opts out of much of it, so
reserve it for applications that genuinely require broader access, not as a default troubleshooting
step.

## Performance Considerations

Snaps mount as compressed filesystem images and can have a slightly slower first-launch time than
a native package; automatic background updates (`refresh`) can also use bandwidth without
explicit user action.

## Production Usage

`snap` is most common for desktop and developer-tool installs where cross-distro consistency
matters more than minimal footprint — less common for production server software, where native
packages remain standard.

## Related Commands

- [`flatpak`](flatpak.md) — a competing universal package format with a similar goal

## Related Concepts

- [snap, flatpak](../docs/14-package-managers/snap-flatpak.md)
