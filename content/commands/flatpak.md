---
title: "flatpak — Install Universal, Sandboxed Packages"
description: "Install and manage Flatpak packages — self-contained, sandboxed applications distributed primarily through Flathub."
relatedConcepts: ["14-package-managers/snap-flatpak"]
relatedCommands: ["snap"]
careerRelevance: ["devops", "linux-administrator", "platform"]
difficulty: "good-to-know"
compatibility: [{"context": "General availability", "note": "Requires installing the flatpak package on most distros; not pre-installed by default the way snap is on Ubuntu."}]
updatedAt: "2026-07-27"
keywords: ["flatpak install examples", "flatpak list", "flathub", "flatpak remove"]
canonicalUrl: "/commands/flatpak"
---

# flatpak

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · Platform

> **TL;DR:** `flatpak install flathub app.id` installs a sandboxed, self-contained package from
> the Flathub repository — the community/Red Hat-affiliated competitor to `snap`, same
> cross-distro consistency goal.

## Purpose

`flatpak` installs and manages Flatpak packages — self-contained, sandboxed applications — see
[snap, flatpak](../docs/14-package-managers/snap-flatpak.md) for the full concept and tradeoffs.

## Syntax

```
flatpak [OPTIONS] COMMAND [ARGS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The action: `install`, `uninstall`, `list`, `update`, etc. | Yes |
| `ARGS` | Repository/application ID, depending on command | Depends on command |

## Options

| Flag | Meaning |
|---|---|
| `install REMOTE APP_ID` | Install an application from a given remote (repository) |
| `uninstall APP_ID` | Remove an installed application |
| `list` | List installed applications |
| `update` | Update installed applications |
| `remote-add` | Add a new Flatpak remote (repository) |

## Examples

```
$ flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```
Add the Flathub remote (the primary Flatpak repository), if not already configured.

```
$ flatpak install flathub org.gimp.GIMP
```
Install GIMP from Flathub.

```
$ flatpak list
```
List installed Flatpak applications.

```
$ flatpak update
```
Update all installed Flatpak applications.

## Expected Output

```
$ flatpak list
Name  Application ID   Version  Branch  Origin
GIMP  org.gimp.GIMP    2.10.36  stable  flathub
```

## Exit Status

`0` on success, non-zero if the application or remote isn't found, or the operation fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `error: No remote refs found` | The Flathub (or other) remote isn't configured yet | Run `flatpak remote-add` first |
| Application can't access an expected file/device | Sandboxing restricting access | Grant specific permissions with `flatpak override`, or check the app's declared permissions |
| `flatpak` command not found | Not installed on this distro | Install the `flatpak` package via the native package manager first |

## Security Considerations

Sandboxing is the core security benefit — use `flatpak override` deliberately and narrowly to
grant additional access, rather than broadly disabling sandboxing.

## Performance Considerations

Shared runtimes (common dependencies used across multiple Flatpak apps) reduce duplicate disk
usage compared to each app bundling everything independently, partially offsetting the general
"universal packages use more space" tradeoff.

## Production Usage

`flatpak` is most common for desktop application installs where cross-distro consistency matters
— rarely used for production server software, where native packages remain standard.

## Related Commands

- [`snap`](snap.md) — a competing universal package format with a similar goal

## Related Concepts

- [snap, flatpak](../docs/14-package-managers/snap-flatpak.md)
