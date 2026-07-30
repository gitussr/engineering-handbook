---
title: "Virtualization Options: VirtualBox, VMware, WSL2"
description: "How to run Linux without wiping your main machine — VirtualBox, VMware, and WSL2 compared, and which one to pick for learning."
module: "02-installing-linux"
moduleTitle: "Installing Linux"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["01-linux-basics/choosing-a-distro"]
relatedTopics: ["installing-ubuntu-debian", "dual-boot-considerations"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#virtualization-basics"]
relatedCheatsheet: ""
furtherReading: [{"label": "WSL2 official documentation", "url": "https://learn.microsoft.com/windows/wsl/"}, {"label": "VirtualBox documentation", "url": "https://www.virtualbox.org/wiki/Documentation"}]
nextTopic: "02-installing-linux/installing-ubuntu-debian"
prevTopic: "01-linux-basics/choosing-a-distro"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["virtualbox", "vmware", "wsl2", "linux virtual machine", "hypervisor"]
canonicalUrl: "/docs/installing-linux/virtualization-options"
---

# Virtualization Options: VirtualBox, VMware, WSL2

🟢 Must Know · Relevant for: DevOps · Cloud · Software Engineering

> **TL;DR:** Run Linux inside your current OS with a hypervisor (VirtualBox, VMware) or, on
> Windows, WSL2 — a lightweight real-Linux-kernel environment with none of the VM overhead.
> Use WSL2 for CLI-only practice, a full VM when you need a real desktop/GUI or a second isolated
> machine.

## What is it?

A hypervisor lets you run a complete Linux system inside a virtual machine (VM) on top of your
existing OS, without repartitioning anything. WSL2 (Windows Subsystem for Linux 2) is Microsoft's
alternative: a real Linux kernel running in a lightweight virtualized environment integrated
directly into Windows, without the overhead of a full VM.

## Why does it exist?

Installing Linux directly onto your only machine is risky and irreversible without careful
partitioning. Virtualization gives you a disposable, snapshot-able, fully isolated Linux
environment — you can break it, reinstall it, or delete it entirely with zero risk to your host
OS or files.

## Where is it used?

- **VirtualBox** — free, cross-platform (Windows/macOS/Linux host), the default recommendation
  for this documentation's exercises.
- **VMware Workstation/Fusion** — commercial-grade, generally better performance and graphics
  support; common in corporate environments that standardize on VMware.
- **WSL2** — Windows-only, no full VM overhead, best when you only need a terminal and don't need
  a graphical desktop. The most common choice for DevOps/Cloud learners on a Windows machine.
- **KVM/Xen** — the production-grade Type 1 hypervisors that actually power most cloud provider
  infrastructure underneath services like AWS EC2 (mentioned here for context; not something you
  install locally as a learner).

## How it works

> 📊 Diagram: two stacked diagrams side by side — left labeled "Type 2 Hypervisor" showing
> Host OS → VirtualBox/VMware → Guest OS, right labeled "WSL2" showing Windows → Lightweight
> Linux VM (real kernel) → Linux userspace, to show WSL2 is architecturally a VM too, just a much
> lighter one with tighter OS integration.

VirtualBox and VMware are "Type 2" (hosted) hypervisors — they run as an application on top of
your existing OS and virtualize hardware for the guest. WSL2 is technically also a lightweight VM
under the hood, but Microsoft integrates it tightly enough (shared filesystem access, no separate
desktop window) that it feels more like a terminal add-on than "a VM." Both require
hardware virtualization support (Intel VT-x / AMD-V) enabled in the machine's BIOS/UEFI.

## Real-world example

An engineer needs to test a deployment script against both Ubuntu and Rocky Linux before pushing
it to production. Rather than needing two physical machines, they run both as VirtualBox VMs
side by side, snapshot each one before testing, and roll back instantly when a test run leaves a
VM in a broken state — something not possible on a single physical install.

## Commands

No command example on this page — installation and configuration here is GUI/installer-driven.
See [Installing Ubuntu/Debian](installing-ubuntu-debian.md) for the first real command example in
this module.

## Production example

Not applicable at the local-learning level this page covers. The production equivalent is cloud
providers running guest VMs on KVM/Xen-based Type 1 hypervisors at scale — covered in
[Spinning Up a Cloud VM](cloud-vm-setup.md).

## Do / Don't

| Do | Don't |
|---|---|
| Enable virtualization (VT-x/AMD-V) in BIOS before installing a hypervisor | Assume virtualization is enabled by default on every machine |
| Use WSL2 for CLI-only DevOps/Cloud practice on Windows | Confuse WSL1 with WSL2 — WSL1 has no real Linux kernel |
| Snapshot a VM before a risky change | Skip snapshots and hope a broken VM is recoverable |

## Common mistakes

- Not enabling virtualization support in BIOS/UEFI first — the hypervisor installer will fail or
  refuse to start a VM without it.
- Under-allocating resources (2GB RAM, 1 vCPU) to a VM meant to run a full desktop distro,
  leading to a frustratingly slow experience that has nothing to do with Linux itself.
- Assuming WSL1 and WSL2 are interchangeable — WSL1 translates Linux syscalls to Windows and
  lacks a real Linux kernel; WSL2 runs an actual Linux kernel and behaves much closer to a real
  Linux system.

## Best practices

- Allocate at least 2 vCPUs and 4GB RAM to a VM running Ubuntu Server comfortably; more for a
  desktop environment.
- Snapshot before any change you're not sure about (kernel upgrades, risky config edits) —
  snapshots are the actual safety net virtualization provides over a bare-metal install.
- Default to WSL2 if your goal is CLI/DevOps skills specifically — it's faster to set up and
  closer to how you'll actually work day to day.

## Exercises

1. Check whether your machine has virtualization enabled in BIOS/UEFI.
2. Install either VirtualBox or WSL2 (based on your OS and goal) before moving to the next topic.
3. If using a hypervisor, take a snapshot of a fresh VM immediately after install, before making
   any changes.

## Quiz

**Q: Is WSL2 actually running a real Linux kernel?**
<details><summary>Show answer</summary>
Yes — unlike WSL1, WSL2 runs a real Linux kernel inside a lightweight, tightly-integrated virtual
machine.
</details>

**Q: What BIOS/UEFI setting is required before installing a hypervisor?**
<details><summary>Show answer</summary>
Hardware virtualization support — Intel VT-x or AMD-V — must be enabled, or the hypervisor won't
be able to start VMs.
</details>

## Interview questions

- What's the difference between a Type 1 and Type 2 hypervisor? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Virtualization gives you a disposable, snapshot-able Linux environment with zero risk to your
  host OS.
- VirtualBox (free) and VMware (commercial) are Type 2 hypervisors; WSL2 is a lightweight
  Windows-integrated alternative with a real Linux kernel.
- Production cloud VMs run on Type 1 hypervisors (KVM/Xen), not the tools covered on this page.
- Pick WSL2 for CLI-only practice, a full hypervisor VM when you need a real desktop or a fully
  separate machine.

## Further Reading

- [WSL2 official documentation](https://learn.microsoft.com/windows/wsl/)
- [VirtualBox documentation](https://www.virtualbox.org/wiki/Documentation)

## Related topics

- [Installing Ubuntu/Debian](installing-ubuntu-debian.md)
- [Dual Boot Considerations](dual-boot-considerations.md)
