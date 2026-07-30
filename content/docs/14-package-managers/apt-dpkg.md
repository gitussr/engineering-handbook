---
title: "apt, dpkg"
description: "Debian/Ubuntu's package tools — apt for everyday installs with dependency resolution, dpkg for operating on a single already-downloaded .deb file."
module: "14-package-managers"
moduleTitle: "Package Managers"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["14-package-managers/package-management-overview"]
relatedTopics: ["yum-dnf-rpm"]
relatedCommands: ["apt", "dpkg"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#apt-vs-dpkg"]
relatedCheatsheet: "package-managers"
furtherReading: []
nextTopic: "14-package-managers/yum-dnf-rpm"
prevTopic: "14-package-managers/package-management-overview"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["apt install examples", "apt update vs upgrade", "dpkg -i explained", "apt vs dpkg"]
canonicalUrl: "/docs/package-managers/apt-dpkg"
---

# apt, dpkg

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `apt update` refreshes the package list; `apt install name` installs a package plus
> its dependencies from configured repositories. `dpkg -i file.deb` installs a single,
> already-downloaded `.deb` file with no dependency resolution.

## What is it?

The Debian/Ubuntu-family package tools: `apt`, the high-level tool for everyday, repository-based
installs, and `dpkg`, the low-level tool that operates on individual `.deb` package files
directly — the concrete, hands-on application of the
[high-level/low-level split](package-management-overview.md) from this module's first topic.

## Why does it exist?

Debian and its derivatives (Ubuntu foremost) needed a consistent way to install and manage
`.deb`-format software with dependency resolution — `apt` provides that for everyday use, while
`dpkg` remains the underlying tool for the cases that genuinely need direct, single-file control
(a locally built package, a vendor-provided `.deb` not in any repository).

## Where is it used?

Installing and updating software on essentially every Debian/Ubuntu server and desktop —
provisioning scripts, Dockerfiles targeting Debian-based images, and routine patching all use
`apt` directly; `dpkg` shows up specifically when installing a `.deb` file obtained outside a
configured repository.

## How it works

> 📊 Diagram: `apt install nginx` shown querying the local package index (built from configured
> repositories), resolving nginx's full dependency tree, downloading every `.deb` in that tree,
> and handing each one to `dpkg` underneath to actually unpack and install — `apt` as the
> orchestrator, `dpkg` as the mechanism doing the actual file-level work.

| Command | Purpose |
|---|---|
| `apt update` | Refresh the local package index from configured repositories (doesn't install anything) |
| `apt upgrade` | Upgrade all installed packages to their latest available version |
| `apt install NAME` | Install a package plus its dependencies |
| `apt remove NAME` | Remove a package, keeping its config files |
| `apt purge NAME` | Remove a package and its config files |
| `dpkg -i FILE.deb` | Install a single, already-downloaded `.deb` file (no dependency resolution) |
| `dpkg -l` | List installed packages |

**`apt` actually calls `dpkg` underneath** for the final unpack-and-install step of each package
— `apt`'s job is orchestration (dependency resolution, downloading, ordering); `dpkg`'s job is
actually installing one package file's contents onto the filesystem.

## Real-world example

A vendor ships a proprietary monitoring agent only as a standalone `.deb` file, not through any
repository. `dpkg -i agent.deb` fails with unmet dependency errors because `dpkg` doesn't fetch
anything on its own. Running `apt install ./agent.deb` instead (recent `apt` versions accept a
local file path directly) resolves and installs the missing dependencies from configured
repositories while still installing the local file — combining both tools' strengths in one
command.

## Commands

- [`apt`](../../commands/apt.md) — full syntax and examples
- [`dpkg`](../../commands/dpkg.md) — full syntax and examples

## Production example

```
$ sudo apt update
$ sudo apt install nginx
$ dpkg -l | grep nginx
ii  nginx    1.24.0-2ubuntu7    amd64    small, powerful, scalable web/proxy server
```

`apt update` always runs before `install`/`upgrade` in practice — installing against a stale
package index can pull an outdated version or miss a package entirely.

## Do / Don't

| Do | Don't |
|---|---|
| Run `apt update` before `apt install`/`upgrade` | Assume the local package index is always current without refreshing it |
| Use `apt install ./file.deb` for a local file that also needs dependencies resolved | Use raw `dpkg -i` on a file with unmet dependencies and expect it to resolve them |
| Use `apt purge` when you also want config files removed | Assume `apt remove` deletes configuration files too |

## Common mistakes

- Running `apt install`/`upgrade` without first running `apt update`, working against a stale
  package index.
- Using `dpkg -i` on a `.deb` with unresolved dependencies and being surprised by failure — `dpkg`
  was never going to fetch anything.
- Confusing `apt remove` (keeps config files) with `apt purge` (removes them too), leading to
  stale leftover configuration after an intended full removal.

## Best practices

- Always `apt update` immediately before `install` or `upgrade` in scripts and provisioning code.
- Use `apt install ./local-file.deb` (not raw `dpkg -i`) when a local `.deb` also has
  repository-available dependencies.
- Use `apt purge` (not `remove`) when a package is being removed for good and its configuration
  shouldn't linger.

## Exercises

1. Run `apt update` followed by `apt install` for any package on a Debian/Ubuntu system you have
   access to.
2. Run `dpkg -l` and identify at least one installed package.
3. Explain in one sentence why `dpkg -i` can fail on a file that `apt install` would handle
   successfully.

## Quiz

**Q: What does `apt update` actually do?**
<details><summary>Show answer</summary>
Refreshes the local package index from configured repositories — it does not install or upgrade
any packages itself.
</details>

**Q: Why might `dpkg -i somepackage.deb` fail with a dependency error that `apt install` wouldn't
hit?**
<details><summary>Show answer</summary>
`dpkg` only installs the exact file given and doesn't fetch dependencies from anywhere; `apt`
resolves and downloads the full dependency tree from configured repositories.
</details>

## Interview questions

- What's the practical difference between `apt` and `dpkg`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `apt` is the high-level tool (repository-aware, resolves dependencies); `dpkg` is the low-level
  tool (single-file, no dependency resolution).
- `apt` calls `dpkg` underneath to actually install a package's files.
- `apt update` must run before `install`/`upgrade` to work against a current package index.
- `apt purge` removes config files that `apt remove` leaves behind.

## Related topics

- [yum, dnf, rpm](yum-dnf-rpm.md)
- [Package Management Overview](package-management-overview.md)
