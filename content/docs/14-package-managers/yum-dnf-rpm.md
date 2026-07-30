---
title: "yum, dnf, rpm"
description: "RHEL/CentOS/Rocky's package tools — dnf for everyday installs with dependency resolution, rpm for operating on a single already-downloaded .rpm file, and yum as dnf's predecessor name."
module: "14-package-managers"
moduleTitle: "Package Managers"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["14-package-managers/apt-dpkg"]
relatedTopics: ["package-management-overview"]
relatedCommands: ["dnf", "rpm"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#yum-vs-dnf"]
relatedCheatsheet: "package-managers"
furtherReading: []
nextTopic: "14-package-managers/snap-flatpak"
prevTopic: "14-package-managers/apt-dpkg"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["dnf install examples", "yum vs dnf", "rpm -qa explained", "rpm install package"]
canonicalUrl: "/docs/package-managers/yum-dnf-rpm"
---

# yum, dnf, rpm

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `dnf install name` installs a package plus dependencies — the modern replacement for
> `yum`, which it's largely command-compatible with (`yum install` still works on most current
> systems, aliased straight to `dnf`). `rpm -i file.rpm` installs a single, already-downloaded
> file with no dependency resolution — the RHEL-family equivalent of `dpkg`.

## What is it?

The RHEL/CentOS/Rocky-family (and Fedora) package tools: `dnf`, the current high-level tool for
repository-based installs, and `rpm`, the low-level tool that operates on individual `.rpm`
package files directly. `yum` is `dnf`'s direct predecessor — largely the same commands, older
and slower dependency-resolution internals.

## Why does it exist?

RHEL-family distros needed the same high-level/low-level split
[introduced in this module's first topic](package-management-overview.md), applied to the `.rpm`
package format: `dnf`/`yum` for everyday, dependency-resolving installs, `rpm` for direct,
single-file operations and package metadata inspection.

## Where is it used?

Installing and updating software on RHEL, CentOS, Rocky Linux, AlmaLinux, and Fedora systems —
the RHEL-family equivalent of everything `apt`/`dpkg` do on Debian/Ubuntu.

## How it works

> 📊 Diagram: `dnf install nginx` shown resolving nginx's dependency tree against configured
> repositories, downloading each `.rpm`, and handing each one to `rpm` underneath to actually
> unpack and install — directly mirroring the `apt`-calls-`dpkg` relationship from the previous
> topic, with `yum` shown as an older, still-present alias pointing at the same `dnf` binary on
> current systems.

| Command | Purpose |
|---|---|
| `dnf install NAME` | Install a package plus dependencies |
| `dnf update` (or `upgrade`) | Upgrade installed packages |
| `dnf remove NAME` | Remove a package |
| `dnf list installed` | List installed packages |
| `rpm -i FILE.rpm` | Install a single, already-downloaded `.rpm` file (no dependency resolution) |
| `rpm -qa` | List installed packages (query all) |
| `rpm -qi NAME` | Show detailed info about an installed package |

**`yum` is not a separate, competing tool on current RHEL-family systems** — it's kept as a
command name (often literally symlinked to `dnf`) for compatibility with muscle memory and older
scripts; `dnf` is the actual, actively developed implementation underneath.

## Real-world example

An engineer familiar with an older CentOS 7 system runs `yum install httpd` out of habit on a new
Rocky Linux 9 server. The command works identically — because `yum` on Rocky 9 is `dnf`
underneath — but the engineer notices the dependency resolution feels noticeably faster than they
remembered, a direct, visible result of `dnf`'s improved resolver replacing `yum`'s older one.

## Commands

- [`dnf`](../../commands/dnf.md) — full syntax and examples
- [`rpm`](../../commands/rpm.md) — full syntax and examples

`yum` is not given a separate canonical command page — on current RHEL-family systems it's an
alias for `dnf` with the same flags and behavior; see [`dnf`](../../commands/dnf.md)'s
Compatibility Notes.

## Production example

```
$ sudo dnf install nginx
$ rpm -qa | grep nginx
nginx-1.24.0-1.el9.x86_64
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `dnf` as the default on any current RHEL-family system | Assume `yum` and `dnf` are two separate tools you need to choose between |
| Use `rpm -qa`/`rpm -qi` for querying installed package metadata directly | Reach for `rpm -i` expecting it to resolve dependencies from a repository |
| Recognize `yum` commands in older docs/scripts as directly portable to `dnf` | Rewrite every `yum` reference assuming incompatibility |

## Common mistakes

- Treating `yum` and `dnf` as fundamentally different tools requiring separate learning, instead
  of recognizing `yum` as a compatibility alias for `dnf` on current systems.
- Using `rpm -i` on a package with unresolved dependencies and expecting it to fetch them —
  exactly the same category of mistake as `dpkg -i` in the previous topic.
- Not realizing `rpm -qa`/`rpm -qi` are extremely useful for auditing installed software even
  though `rpm` itself doesn't install from repositories.

## Best practices

- Default to `dnf` on any current RHEL-family system; treat `yum` command familiarity as directly
  transferable, not something requiring relearning.
- Use `rpm -qa`/`rpm -qi` for package auditing and inspection tasks, even when `dnf` handles the
  actual installs.
- Don't rewrite working `yum`-based scripts to `dnf` purely out of principle — they're commonly
  interchangeable on current systems.

## Exercises

1. Run `dnf install` for any package on a RHEL-family system you have access to (or `yum install`
   if that's what's available, noting whether it's aliased to `dnf`).
2. Run `rpm -qa` and identify at least one installed package.
3. Explain in one sentence the relationship between `yum` and `dnf` on a current RHEL-family
   system.

## Quiz

**Q: Is `yum` a separate tool from `dnf` on current RHEL-family distros?**
<details><summary>Show answer</summary>
No — on current systems, `yum` is typically an alias/compatibility layer pointing directly at
`dnf`, which is the actual, actively developed implementation.
</details>

**Q: What does `rpm -qa` do, and does it install anything?**
<details><summary>Show answer</summary>
It queries and lists all currently installed packages — a read-only inspection command, not an
install operation.
</details>

## Interview questions

- What's the relationship between `yum` and `dnf` on a modern RHEL-family system? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `dnf` is the current high-level tool on RHEL-family distros; `rpm` is the low-level, single-file
  tool.
- `yum` is `dnf`'s predecessor name, kept as a compatibility alias on current systems rather than
  a separate tool.
- `rpm -qa`/`rpm -qi` are useful for auditing installed packages even though `rpm` itself doesn't
  resolve dependencies.
- This mirrors the `apt`/`dpkg` split from the previous topic exactly, applied to the `.rpm`
  format.

## Related topics

- [apt, dpkg](apt-dpkg.md)
- [Package Management Overview](package-management-overview.md)
