---
title: "Linux Distributions"
description: "The major Linux distribution families, how they differ, and which ones you'll actually meet in professional environments."
module: "01-linux-basics"
moduleTitle: "Linux Basics"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["01-linux-basics/what-is-linux"]
relatedTopics: ["kernel-vs-os-vs-distro", "choosing-a-distro"]
relatedCommands: ["cat"]
careerRelevance: ["devops", "cloud", "linux-administrator", "wordpress-web-hosting"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#distro-differences"]
relatedCheatsheet: "package-managers"
furtherReading: [{"label": "Debian vs Red Hat family comparison (DistroWatch)", "url": "https://distrowatch.com"}]
nextTopic: "01-linux-basics/kernel-vs-os-vs-distro"
prevTopic: "01-linux-basics/what-is-linux"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["linux distributions", "ubuntu", "debian", "centos", "rhel", "rocky linux", "arch linux"]
canonicalUrl: "/docs/linux-basics/linux-distributions"
---

# Linux Distributions

🟢 Must Know · Relevant for: DevOps · Cloud · Linux Administrator · WordPress/Web Hosting

> **TL;DR:** A distribution ("distro") packages the Linux kernel with a package manager and
> defaults into something installable. Two families matter most professionally:
> Debian/Ubuntu (`apt`) and RHEL/CentOS/Rocky (`dnf`/`yum`).

## What is it?

A distribution ("distro" from here on) is the Linux kernel packaged together with a package
manager, default software, and configuration conventions into something installable. Different
distros make different tradeoffs on stability, package freshness, and target use case.

## Why does it exist?

The kernel alone isn't usable. Distributions exist because someone has to decide: which package
manager, which default shell, how often to update, how long to support a release. Different
organizations made different decisions, which is why multiple major distro families exist instead
of one universal Linux.

## Where is it used?

- **Debian/Ubuntu family** — the most common choice for cloud VMs, CI/CD runners, Docker base
  images, and general DevOps work. `apt`/`dpkg` package management.
- **RHEL/CentOS/Rocky/Alma family** — dominant in enterprise data centers, government, and
  regulated industries. `yum`/`dnf`/`rpm` package management. Red Hat sells paid support (RHEL);
  Rocky/Alma are free rebuilds used when you want RHEL compatibility without the subscription.
- **Arch Linux** — rolling release, minimal, used by engineers who want full control and the
  newest packages; rare in production servers, common among power users and some SRE/platform
  engineers on their own machines.
- **SUSE/openSUSE** — common in enterprise Europe, some SAP-heavy environments.
- **Alpine Linux** — extremely small, common as a base image for Docker containers.

## How it works

> 📊 Diagram: tree showing distro families branching from their base — Debian → Ubuntu → Linux
> Mint; Red Hat → CentOS/Rocky/Alma/Fedora — to show lineage and package-manager inheritance.

Distros in the same family share a package format and largely compatible tooling. Knowing `apt`
transfers across all Debian-derived distros; knowing `dnf`/`yum` transfers across all
RHEL-derived ones. This is why "which distro family" matters more early on than "which specific
distro." Most distros also publish an **LTS (Long Term Support)** release — a version with an
extended, predictable support window (e.g. 5 years) — which is what production systems run
instead of short-support interim releases.

## Real-world example

A company's platform team standardizes every new cloud VM and every Docker base image on Ubuntu
LTS, specifically so an engineer who learns `apt` once can operate the entire fleet. A separate
finance-systems team at the same company runs RHEL on-prem, because their compliance auditor
requires vendor-backed support contracts that Ubuntu Server doesn't offer at the same tier. Same
company, two distro families, both deliberate — not accidental.

## Commands

- [`apt`](../../commands/apt.md) — Debian/Ubuntu package manager
- [`dnf`](../../commands/dnf.md) — modern RHEL/Rocky/Fedora package manager
- [`cat /etc/os-release`](../../commands/cat.md) — identify which distro you're on

## Production example

```
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="22.04.4 LTS (Jammy Jellyfish)"
ID=ubuntu
ID_LIKE=debian
```

This is the first command an engineer runs after SSHing into an unfamiliar server — it tells you
immediately which package manager and conventions apply, before you run anything else.

## Do / Don't

| Do | Don't |
|---|---|
| Run `cat /etc/os-release` before assuming a package manager | Assume `apt install` works everywhere — RHEL-family needs `dnf`/`yum` |
| Pick LTS releases for anything production | Run short-support interim releases in production |

## Common mistakes

- Assuming a command that works on Ubuntu (`apt install`) will work identically on CentOS (it
  won't — you need `dnf install` or `yum install`).
- Picking a distro because it's popular for desktops (e.g. Arch, Fedora Workstation) without
  checking whether it's actually common in the target job's production environment.
- Not checking LTS status — running a short-support release in production is a maintenance
  liability.

## Best practices

- For cloud/DevOps career paths: default to Ubuntu LTS or Debian stable unless a job specifically
  requires RHEL-family knowledge.
- For enterprise/sysadmin career paths: get comfortable with RHEL/Rocky, since that's still the
  default in many large, regulated organizations.
- Always check `/etc/os-release` on an unfamiliar server before assuming a package manager.

## Exercises

1. Run `cat /etc/os-release` on a Linux system and identify the distro family.
2. List which package manager each of these use: Ubuntu, CentOS, Alpine, Arch.
3. Find one distro-specific reason a company might choose RHEL over Ubuntu (hint: support
   contracts, compliance).

## Quiz

**Q: Why does "distro family" matter more than the specific distro name early in learning?**
<details><summary>Show answer</summary>
Because package manager and tooling knowledge transfers across an entire family (all
Debian-derived distros use `apt`), so learning one distro well in a family gets you most of the
way on every distro in that family.
</details>

**Q: What's the difference between CentOS and Rocky Linux?**
<details><summary>Show answer</summary>
CentOS was historically a free rebuild of RHEL; after Red Hat changed CentOS's model, Rocky Linux
emerged as a community-driven free RHEL-compatible rebuild filling that role.
</details>

## Interview questions

- What's the difference between Debian-based and RHEL-based distributions? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)
- How would you identify which distro a server is running? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A distro is the kernel plus a package manager, defaults, and support model.
- Debian/Ubuntu and RHEL/CentOS/Rocky are the two families you'll meet most in professional work.
- Package manager knowledge transfers within a family, not across families.
- `cat /etc/os-release` is the fastest way to identify what you're working with.

## Further Reading

- [DistroWatch — distro family comparisons](https://distrowatch.com)

## Related topics

- [Kernel vs OS vs Distro](kernel-vs-os-vs-distro.md)
- [Choosing a Distro for Your Career Path](choosing-a-distro.md)
- Cheat sheet: [Package Managers](../../cheatsheets/package-managers.md)
