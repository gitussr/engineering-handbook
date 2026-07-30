---
title: "Package Managers"
description: "Module 14 of the Linux roadmap — dependency resolution fundamentals, apt/dpkg, yum/dnf/rpm, universal packages, building from source, repository management, and package signing."
module: "14-package-managers"
moduleTitle: "Package Managers"
stage: "intermediate"
type: "module-index"
nextTopic: "14-package-managers/package-management-overview"
updatedAt: "2026-07-27"
canonicalUrl: "/docs/package-managers"
---

# Package Managers

Module 14 of 34 · Stage: Intermediate · Previous: [13 SSH](../13-ssh/index.md)

Every Linux system needs software installed, updated, and removed in a tracked, repeatable way.
This module covers both major distro-family toolchains (`apt`/`dpkg` for Debian/Ubuntu,
`dnf`/`rpm` for RHEL/CentOS/Rocky), the cross-distro alternative (`snap`/`flatpak`), the last-resort
fallback (building from source), and the two topics that determine how much you should trust any
of it (repository management, package signing).

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Package Management Overview](package-management-overview.md) | 🟢 Must Know |
| [apt, dpkg](apt-dpkg.md) | 🟢 Must Know |
| [yum, dnf, rpm](yum-dnf-rpm.md) | 🟢 Must Know |
| [snap, flatpak](snap-flatpak.md) | 🟡 Good to Know |
| [Building from Source: make, ./configure](building-from-source-make-configure.md) | 🟡 Good to Know |
| [Repository Management](repository-management.md) | 🟡 Good to Know |
| [Package Signing and Verification](package-signing-and-verification.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain what a package manager does and the difference between a high-level and a low-level
  package tool.
- Install, update, and remove software with `apt`/`dpkg` on Debian/Ubuntu and `dnf`/`rpm` on
  RHEL-family distros.
- Explain when a universal package format (`snap`/`flatpak`) is the right choice over a native
  package.
- Build software from source when no package exists, and explain the risks of doing so.
- Explain where repository configuration lives and why adding a repository is a security
  decision.
- Explain how package signing and verification protects against a tampered or compromised
  repository.

## Known, intentional gaps in this module

- Canonical command pages exist for `apt`, `dpkg`, `dnf`, `rpm`, `snap`, `flatpak`, and `make` —
  every command this module centers on.
- `yum` is deliberately not given a separate canonical command page — on current RHEL-family
  systems it's a compatibility alias for `dnf`, covered within `dnf`'s Compatibility Notes (the
  same treatment `ifconfig`, `netstat`, and `mtr` received in Module 12).
- `./configure` is covered in full within its topic page but isn't a standalone system command
  with its own canonical page — it's a project-generated script whose exact behavior varies per
  project.
- `add-apt-repository`, `rpm --checksig`, and `dpkg-sig` are referenced but don't have canonical
  pages — narrow, situational tools rather than commands central to this module's core teaching.

**Previous module:** [13 SSH](../13-ssh/index.md)
**Next module:** [15 Storage →](../15-storage/index.md)
