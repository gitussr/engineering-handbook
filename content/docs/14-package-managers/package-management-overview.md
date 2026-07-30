---
title: "Package Management Overview"
description: "Why software isn't just downloaded and run on Linux — dependency resolution, repositories, and the distro-family split every later topic in this module builds on."
module: "14-package-managers"
moduleTitle: "Package Managers"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["13-ssh/ssh-agent-and-agent-forwarding"]
relatedTopics: ["apt-dpkg", "yum-dnf-rpm"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#package-management-overview"]
relatedCheatsheet: "package-managers"
furtherReading: []
nextTopic: "14-package-managers/apt-dpkg"
prevTopic: "13-ssh/ssh-agent-and-agent-forwarding"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["what is a package manager", "linux dependency resolution", "package repository explained", "debian vs rhel packages"]
canonicalUrl: "/docs/package-managers/package-management-overview"
---

# Package Management Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** A package manager installs, updates, and removes software while automatically
> resolving dependencies and tracking exactly what's installed. Debian/Ubuntu use `.deb` packages
> via `apt`/`dpkg`; RHEL/CentOS/Rocky use `.rpm` packages via `dnf`/`rpm` — different formats,
> same underlying job.

## What is it?

Software that installs, upgrades, and removes other software in a tracked, repeatable way —
resolving dependencies automatically, verifying package integrity, and keeping a record of
exactly what's installed and which version, rather than requiring manual download-and-compile for
every piece of software.

## Why does it exist?

Installing software by hand means manually finding every dependency, resolving version conflicts,
and tracking what you installed and how, entirely yourself — this doesn't scale past a
handful of programs, and becomes actively dangerous at the fleet scale most production Linux work
happens at. A package manager automates dependency resolution, provides a consistent way to
update or remove software cleanly, and gives every machine running the same package version an
identical, verifiable installation.

## Where is it used?

Provisioning any new server or container image, keeping fleets of machines patched consistently,
installing dependencies for an application before it can run, and — for security-sensitive
roles — auditing exactly what's installed and verifying it hasn't been tampered with.

## How it works

> 📊 Diagram: a package manager shown sitting between a local machine and a remote repository —
> resolving a requested package's dependency tree, downloading each piece from the repository,
> verifying signatures, and recording every installed package and version in a local database.

| Distro family | Package format | High-level tool | Low-level tool |
|---|---|---|---|
| Debian/Ubuntu | `.deb` | `apt` | `dpkg` |
| RHEL/CentOS/Rocky, Fedora | `.rpm` | `dnf` (formerly `yum`) | `rpm` |

**High-level vs. low-level tools**: the high-level tool (`apt`, `dnf`) talks to repositories over
the network and resolves dependencies automatically. The low-level tool (`dpkg`, `rpm`) operates
only on a single, already-downloaded package file and does *not* resolve dependencies —
understanding this split is the key to the next two topics in this module.

**Dependency resolution**: when you ask to install one package, the package manager calculates
every other package it requires (and every dependency of those, recursively), and installs the
full set together — the alternative (installing one package at a time and manually chasing
"missing dependency" errors) is exactly what package managers exist to eliminate.

## Real-world example

A new engineer, used to downloading `.exe` installers manually, tries to install a `.deb` package
file directly with `dpkg` on a fresh Ubuntu server. It fails with unmet dependency errors — `dpkg`
doesn't fetch dependencies from anywhere, it only installs the exact file given. Switching to
`apt install packagename` (the high-level tool, which does resolve and fetch dependencies from
configured repositories) succeeds immediately, illustrating exactly the high-level/low-level split
this module is built around.

## Commands

No command example on this page — this page is conceptual foundation. See
[apt, dpkg](apt-dpkg.md) for the first hands-on commands in this module.

## Production example

Not applicable — see [apt, dpkg](apt-dpkg.md) for the first terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Use the high-level tool (`apt`/`dnf`) for normal installs so dependencies resolve automatically | Reach for the low-level tool (`dpkg`/`rpm`) expecting it to fetch dependencies |
| Understand which package format (`.deb`/`.rpm`) your target distro uses before troubleshooting | Assume install commands are portable across distro families |
| Trust the package manager's installed-package record as the source of truth for what's on a system | Track installed software manually alongside the package manager |

## Common mistakes

- Trying to install a downloaded package file directly with the low-level tool and being confused
  by dependency errors it was never designed to resolve.
- Assuming a command or package name is identical across Debian/Ubuntu and RHEL-family distros —
  package names and available tools frequently differ between the two families.
- Manually tracking installed software in a separate document instead of trusting the package
  manager's own installed-package database.

## Best practices

- Default to the high-level tool (`apt`/`dnf`) for everyday installs; reach for the low-level tool
  (`dpkg`/`rpm`) specifically when working with an already-downloaded package file or inspecting
  installed package metadata directly.
- Know which distro family (and therefore which package format) a target system uses before
  writing or running install commands.
- Treat the package manager's database as the authoritative record of installed software.

## Exercises

1. Identify which package format (`.deb` or `.rpm`) the distro you're using (or most familiar
   with) uses.
2. Explain in one sentence the difference between a high-level and a low-level package tool.
3. Explain why installing a `.deb` file directly with `dpkg` can fail with dependency errors that
   `apt install` wouldn't hit.

## Quiz

**Q: What's the key difference between a high-level tool like `apt`/`dnf` and a low-level tool
like `dpkg`/`rpm`?**
<details><summary>Show answer</summary>
The high-level tool resolves and fetches dependencies from configured repositories automatically;
the low-level tool only operates on a single, already-downloaded package file and does not
resolve dependencies.
</details>

**Q: Which package format does Debian/Ubuntu use, and which does RHEL/CentOS/Rocky use?**
<details><summary>Show answer</summary>
Debian/Ubuntu use `.deb`; RHEL/CentOS/Rocky (and Fedora) use `.rpm`.
</details>

## Interview questions

- What problem does a package manager's dependency resolution actually solve? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A package manager installs, updates, and removes software with automatic dependency
  resolution and a tracked installed-package record.
- Debian/Ubuntu use `.deb` packages (`apt`/`dpkg`); RHEL-family distros use `.rpm` packages
  (`dnf`/`rpm`).
- High-level tools resolve dependencies from repositories; low-level tools only act on a single,
  already-downloaded file.
- Package names and available tooling commonly differ between distro families.

## Related topics

- [apt, dpkg](apt-dpkg.md)
- [yum, dnf, rpm](yum-dnf-rpm.md)
