---
title: "Installing Linux"
description: "Module 02 of the Linux roadmap — getting a real Linux system running, whether in a VM, on a cloud provider, or dual-booted, plus the checklist every professional runs right after."
module: "02-installing-linux"
moduleTitle: "Installing Linux"
stage: "beginner"
type: "module-index"
nextTopic: "02-installing-linux/virtualization-options"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/installing-linux"
---

# Installing Linux

Module 02 of 34 · Stage: Beginner · Previous: [01 Linux Basics](../01-linux-basics/index.md)

By the end of Module 01 you picked a distro. This module gets it actually running — locally in a
VM, on a cloud VM, or dual-booted — and ends with the checklist every professional runs on a
fresh install before doing anything else with it.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Virtualization Options: VirtualBox, VMware, WSL2](virtualization-options.md) | 🟢 Must Know |
| [Installing Ubuntu/Debian](installing-ubuntu-debian.md) | 🟢 Must Know |
| [Installing RHEL/CentOS/Rocky](installing-rhel-centos-rocky.md) | 🟡 Good to Know |
| [Spinning Up a Cloud VM](cloud-vm-setup.md) | 🟢 Must Know |
| [Dual Boot Considerations](dual-boot-considerations.md) | 🟡 Good to Know |
| [Post-Install Setup Checklist](post-install-checklist.md) | 🟢 Must Know |

## What you should be able to do after this module

- Choose between a local VM, WSL2, and a cloud VM based on what you're trying to learn or build.
- Install Ubuntu/Debian and, at a working level, RHEL/CentOS/Rocky.
- Launch and safely tear down a cloud VM without leaving a billing or security surprise behind.
- Know when dual boot is (and mostly isn't) the right call for a learner.
- Run the same post-install checklist a production team would run on day zero.

## Known, intentional gaps in this module

- `relatedLabs` links point to labs that don't exist in the repo yet — they resolve once
  [Module 32](../../labs/index.md) is generated later in Phase 2, same known gap as Module 01.
- Interview question and cheat sheet links resolve once
  [Module 29](../../interview-questions/index.md) and
  [Module 30](../../cheatsheets/index.md) exist.
- Command pages linked from this module (`apt`, `dnf`, `ssh`, etc.) are referenced but not all have
  their canonical `/commands/{cmd}` page written yet — full command reference content is generated
  as each command's owning module is reached (SSH commands in Module 13, package manager commands
  in Module 14, etc.). Links here are forward references. `hostnamectl` and `sha256sum` are
  exceptions — both got standalone canonical pages directly, since neither has a later "owning"
  module that would otherwise deepen them.

**Previous module:** [01 Linux Basics](../01-linux-basics/index.md)
**Next module:** [03 Terminal →](../03-terminal/index.md)
