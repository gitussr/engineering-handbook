---
title: "dpkg — Install and Query .deb Package Files Directly"
description: "The low-level Debian/Ubuntu package tool — operates on a single, already-downloaded .deb file, with no dependency resolution."
relatedConcepts: ["14-package-managers/apt-dpkg"]
relatedCommands: ["apt"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
compatibility: [{"context": "Debian/Ubuntu", "note": "Not available on RHEL-family distros, which use rpm instead."}]
updatedAt: "2026-07-27"
keywords: ["dpkg -i examples", "dpkg -l list packages", "dpkg vs apt", "dpkg query package"]
canonicalUrl: "/commands/dpkg"
---

# dpkg

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `dpkg -i file.deb` installs a single, already-downloaded `.deb` file — with no
> dependency resolution. `dpkg -l` lists installed packages. Use `apt` instead for anything that
> needs dependencies fetched.

## Purpose

`dpkg` installs and queries `.deb` package files directly — see
[apt, dpkg](../docs/14-package-managers/apt-dpkg.md) for how it relates to `apt`.

## Syntax

```
dpkg [OPTIONS] [FILE|PACKAGE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | Path to a `.deb` file (for install) | For `-i` |
| `PACKAGE` | An installed package name (for query/removal) | For query/removal operations |

## Options

| Flag | Meaning |
|---|---|
| `-i FILE` | Install a `.deb` file |
| `-r NAME` (or `--remove`) | Remove a package, keeping config files |
| `-P NAME` (or `--purge`) | Remove a package and its config files |
| `-l` | List installed packages |
| `-L NAME` | List files installed by a specific package |
| `-s NAME` | Show status/info for an installed package |

## Examples

```
$ sudo dpkg -i agent.deb
```
Install a local `.deb` file.

```
$ dpkg -l | grep nginx
```
Check whether a specific package is installed.

```
$ dpkg -L nginx
```
List every file installed by the `nginx` package.

```
$ sudo dpkg -P nginx
```
Remove `nginx` and its configuration files.

## Expected Output

```
$ dpkg -l | grep nginx
ii  nginx    1.24.0-2ubuntu7    amd64    small, powerful, scalable web/proxy server
```

`ii` means fully installed and configured; other status codes indicate partial or failed states.

## Exit Status

`0` on success, non-zero if dependencies are unmet (for `-i`) or the package/file doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `dependency problems - leaving unconfigured` | The `.deb` file has unmet dependencies | Run `sudo apt install -f` to resolve and fetch missing dependencies, or use `apt install ./file.deb` from the start |
| `package is in a very bad inconsistent state` | An interrupted previous install | Try `dpkg --configure -a` to resume/repair |
| `No such file or directory` | Wrong path to the `.deb` file | Verify the file path |

## Security Considerations

`dpkg -i` performs no signature verification or repository trust check on its own the way `apt`
does when installing from a configured repository — only install `.deb` files from sources you
trust directly.

## Performance Considerations

Negligible — operates on a single package file at a time.

## Compatibility Notes

`dpkg` is specific to Debian/Ubuntu-family distros; RHEL-family distros use
[`rpm`](rpm.md) instead, with a very similar low-level role.

## Production Usage

`dpkg -l`/`dpkg -L` are commonly used for auditing exactly what's installed and which files a
package owns, independent of whether `apt` was involved in a given install.

## Related Commands

- [`apt`](apt.md) — the high-level tool that calls `dpkg` underneath and resolves dependencies

## Related Concepts

- [apt, dpkg](../docs/14-package-managers/apt-dpkg.md)
