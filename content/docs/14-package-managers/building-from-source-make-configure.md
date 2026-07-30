---
title: "Building from Source: make, ./configure"
description: "What to do when software isn't packaged for your distro at all — the standard configure/make/make install sequence, and why it should be a last resort."
module: "14-package-managers"
moduleTitle: "Package Managers"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["14-package-managers/snap-flatpak"]
relatedTopics: ["repository-management"]
relatedCommands: ["make"]
careerRelevance: ["devops", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#build-from-source-tradeoffs"]
relatedCheatsheet: "package-managers"
furtherReading: []
nextTopic: "14-package-managers/repository-management"
prevTopic: "14-package-managers/snap-flatpak"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["configure make make install", "build from source linux", "make command explained", "compiling software from source linux"]
canonicalUrl: "/docs/package-managers/building-from-source-make-configure"
---

# Building from Source: make, ./configure

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · Software Engineering

> **TL;DR:** `./configure && make && sudo make install` is the classic sequence for compiling and
> installing software that isn't available as a package at all. It skips the package manager
> entirely — no dependency tracking, no easy uninstall, no automatic updates — so it's a
> deliberate last resort, not a default.

## What is it?

The traditional sequence for compiling software directly from its source code when no `.deb`,
`.rpm`, `snap`, or `flatpak` package exists for it: `./configure` (checks the system and
generates a build configuration), `make` (compiles), and `make install` (copies the built files
into place).

## Why does it exist?

Not every piece of software is packaged for every distro, especially niche, very new, or
custom-built tools. Building from source is the fallback that works regardless of packaging
status, since it only requires the source code and a compiler — at the cost of everything the
package manager would otherwise provide automatically.

## Where is it used?

Installing software with no available package for your distro, building a specific version or
custom configuration a package doesn't offer, and compiling software with custom optimizations or
feature flags not present in a distro's default build.

## How it works

> 📊 Diagram: three sequential stages — `./configure` (checking the system for required
> libraries/compilers and generating a `Makefile`), `make` (reading that `Makefile` and compiling
> source code into binaries), `make install` (copying the compiled binaries and files into system
> directories) — contrasted with a package manager's single `install` command that handles all
> three internally, tracked.

| Stage | What happens |
|---|---|
| `./configure` | Checks for required dependencies/compilers, generates a `Makefile` tailored to this system |
| `make` | Reads the `Makefile` and actually compiles the source code |
| `sudo make install` | Copies the compiled output into system directories (commonly `/usr/local/`) |

**The core tradeoff**: none of this is tracked by the package manager. There's no
`apt remove`/`dnf remove` equivalent, no automatic dependency tracking, and no automatic security
updates — uninstalling means either running `make uninstall` (if the project provides it) or
manually removing files, and updates mean repeating the whole process by hand.

## Real-world example

An engineer needs a very recent version of a tool that hasn't reached their distro's package
repositories yet. They build it from source successfully. Six months later, a security
vulnerability is announced in that tool — `apt upgrade`/`dnf upgrade` silently skips it entirely,
since it was never installed through the package manager in the first place, leaving a
vulnerable, unpatched build in place until someone remembers it was built manually and needs a
manual rebuild.

## Commands

- [`make`](../../commands/make.md) — full syntax and examples

`./configure` is a project-generated script (produced by a project's own build tooling, commonly
Autotools), not a standalone system command with its own canonical page — its exact behavior
varies per project.

## Production example

```
$ ./configure --prefix=/usr/local
$ make
$ sudo make install
```

`--prefix` controls where the software installs to — `/usr/local` is the conventional location
for manually-built software, deliberately kept separate from files the package manager itself
manages.

## Do / Don't

| Do | Don't |
|---|---|
| Treat building from source as a last resort after checking for a package first | Default to building from source when a maintained package already exists |
| Install to `/usr/local` (or another clearly separate prefix), not over package-manager-owned paths | Overwrite files the package manager considers its own |
| Track manually-built software yourself, since the package manager won't | Forget a manually-built tool exists until a security issue forces the question |

## Common mistakes

- Building from source when a perfectly good package already exists, taking on unnecessary
  maintenance burden for no real benefit.
- Losing track of manually-built software over time, missing security updates the package manager
  would have applied automatically to anything it manages.
- Installing to a system path the package manager also manages, risking a conflict or an
  overwritten file during a future package update.

## Best practices

- Always check for an existing package (native, or `snap`/`flatpak`) before building from source.
- Use `--prefix=/usr/local` (or similar) to keep manually-built software cleanly separated from
  package-manager-owned files.
- Keep a personal record of anything built from source, specifically so security updates aren't
  silently missed.

## Exercises

1. Explain in one sentence why building from source should be a last resort, not a default.
2. Describe what `--prefix=/usr/local` accomplishes and why it matters.
3. Explain why a security patch applied via `apt upgrade`/`dnf upgrade` wouldn't reach software
   installed by building from source.

## Quiz

**Q: Why does `apt upgrade`/`dnf upgrade` not patch software that was built from source?**
<details><summary>Show answer</summary>
The package manager has no record of it — it was never installed through `apt`/`dnf` in the first
place, so it's entirely outside the package manager's tracking and update mechanism.
</details>

**Q: What does the `--prefix` flag in `./configure` control?**
<details><summary>Show answer</summary>
Where the compiled software gets installed — conventionally `/usr/local`, kept separate from
paths the package manager manages, to avoid conflicts.
</details>

## Interview questions

- What are the risks of building software from source instead of installing it via a package
  manager? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `./configure && make && make install` is the classic build-from-source sequence.
- Building from source skips the package manager entirely — no dependency tracking, no automatic
  updates, no easy uninstall.
- It should be a deliberate last resort after confirming no package (native or universal) exists.
- `--prefix=/usr/local` keeps manually-built software separate from package-manager-owned files.

## Related topics

- [Repository Management](repository-management.md)
- [snap, flatpak](snap-flatpak.md)
