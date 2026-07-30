---
title: "Installing RHEL/CentOS/Rocky"
description: "Installing a RHEL-family distro — the Anaconda installer, package group selection, and where RHEL's subscription model differs from its free rebuilds."
module: "02-installing-linux"
moduleTitle: "Installing Linux"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["02-installing-linux/installing-ubuntu-debian"]
relatedTopics: ["installing-ubuntu-debian", "post-install-checklist"]
relatedCommands: ["sha256sum"]
careerRelevance: ["linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#rhel-install"]
relatedCheatsheet: ""
furtherReading: [{"label": "Rocky Linux installation guide", "url": "https://docs.rockylinux.org/"}]
nextTopic: "02-installing-linux/cloud-vm-setup"
prevTopic: "02-installing-linux/installing-ubuntu-debian"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["install rhel", "install centos", "install rocky linux", "anaconda installer", "subscription-manager"]
canonicalUrl: "/docs/installing-linux/installing-rhel-centos-rocky"
---

# Installing RHEL/CentOS/Rocky

🟡 Good to Know · Relevant for: Linux Administrator · Cybersecurity

> **TL;DR:** RHEL-family distros install via the Anaconda installer — the flow mirrors Ubuntu's
> (verify checksum, partition deliberately, create a non-root user) with two differences: package
> group selection instead of Server/Desktop, and RHEL specifically requires subscription
> registration that Rocky/Alma don't.

## What is it?

Installing Red Hat Enterprise Linux (RHEL), or one of its free, binary-compatible rebuilds
(Rocky Linux, AlmaLinux — CentOS's former role). All three use the same Anaconda installer and
near-identical install flow.

## Why does it exist?

Enterprise, government, and regulated environments frequently standardize on RHEL specifically
for its vendor support contract, or on Rocky/Alma to get RHEL compatibility without the licensing
cost. Knowing this installer flow matters for the Linux Administrator career path even if your
daily driver elsewhere is Ubuntu.

## Where is it used?

- **RHEL** — paid subscription required for updates/support; standard in banks, government,
  healthcare, and other environments requiring a vendor support contract.
- **Rocky Linux / AlmaLinux** — free, binary-compatible with RHEL, used when an organization wants
  RHEL's stability and package ecosystem without the subscription.
- **Fedora** — RHEL's upstream, fast-moving, rarely used in production — relevant mainly for
  previewing what RHEL will look like several versions ahead.

## How it works

> 📊 Diagram: the same install-decision flowchart as
> [Installing Ubuntu/Debian](installing-ubuntu-debian.md), with one branch swapped — "Choose
> Server or Desktop" becomes "Choose Package Group (Minimal Install, Server, Server with GUI)"
> and an added "Register with Red Hat Subscription (RHEL only)" step.

The decision points that differ from Debian/Ubuntu's installer:

1. **Package group selection** — Anaconda asks you to pick a base environment (Minimal Install,
   Server, Server with GUI, Workstation) rather than a binary Server/Desktop choice.
2. **RHEL subscription registration** — RHEL specifically requires registering with
   `subscription-manager` (or during install) to get package updates; Rocky and AlmaLinux skip
   this entirely since they're free rebuilds with open package repositories.
3. Partitioning and user account creation follow the same principles as
   [Installing Ubuntu/Debian](installing-ubuntu-debian.md) — separate `/var`, create a non-root
   sudo user.

## Real-world example

A company's compliance team requires vendor-supported operating systems on any server handling
regulated data. Their infrastructure team installs RHEL specifically (not Rocky) on those
servers because the support contract — not the OS itself — is the compliance requirement; the
same team runs Rocky Linux on internal, non-regulated servers to avoid unnecessary subscription
costs.

## Commands

- [`sha256sum`](../../commands/sha256sum.md) — verify a downloaded ISO's checksum (same practice
  as covered in [Installing Ubuntu/Debian](installing-ubuntu-debian.md) — not repeated in full
  here)

## Production example

Not repeated here — the checksum verification workflow is identical to the example already shown
in [Installing Ubuntu/Debian](installing-ubuntu-debian.md).

## Do / Don't

| Do | Don't |
|---|---|
| Choose Rocky/AlmaLinux for RHEL compatibility without a subscription need | Assume CentOS still functions the way it did before its model changed |
| Register RHEL with `subscription-manager` before expecting updates | Try to run RHEL indefinitely unregistered and expect a fully patched system |

## Common mistakes

- Assuming CentOS is still the free RHEL rebuild it used to be — its role changed, and
  Rocky/AlmaLinux are now the community rebuilds filling that gap.
- Forgetting RHEL requires subscription registration to receive updates — an unregistered RHEL
  install will not patch itself the way Ubuntu or Rocky will out of the box.
- Picking "Minimal Install" for a server that will actually need common admin tools, then having
  to add them back one by one — "Server" package group is usually the better default.

## Best practices

- Default to Rocky Linux or AlmaLinux for learning and for internal, non-regulated servers.
- Reach for RHEL specifically when a compliance or vendor-support requirement actually calls for
  it — not as a default choice.
- Register RHEL installs with `subscription-manager` immediately after install, before anything
  else.

## Exercises

1. Download a Rocky Linux ISO and verify its checksum.
2. Install Rocky Linux into a VM, choosing the "Server" package group.
3. Explain in one sentence why a company might choose RHEL over the free-equivalent Rocky Linux.

## Quiz

**Q: What's the practical difference between installing RHEL and installing Rocky Linux?**
<details><summary>Show answer</summary>
The installer flow is nearly identical, but RHEL requires subscription registration for updates
and support, while Rocky Linux is free and open with no registration step.
</details>

## Interview questions

- Why might an enterprise choose RHEL over a free RHEL-compatible rebuild? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- RHEL, Rocky Linux, and AlmaLinux all use the same Anaconda installer and nearly identical flow.
- The real difference is the subscription/support model, not the technical install process.
- Package group selection (Minimal/Server/Server with GUI) replaces Ubuntu's simpler
  Server/Desktop choice.
- Choose RHEL only when a support contract or compliance requirement specifically calls for it.

## Further Reading

- [Rocky Linux installation guide](https://docs.rockylinux.org/)

## Related topics

- [Installing Ubuntu/Debian](installing-ubuntu-debian.md)
- [Post-Install Setup Checklist](post-install-checklist.md)
