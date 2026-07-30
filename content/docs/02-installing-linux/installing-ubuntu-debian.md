---
title: "Installing Ubuntu/Debian"
description: "A professional, repeatable process for installing Ubuntu or Debian — checksum verification, partitioning choices, and the install-time decisions that matter later."
module: "02-installing-linux"
moduleTitle: "Installing Linux"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["02-installing-linux/virtualization-options"]
relatedTopics: ["installing-rhel-centos-rocky", "post-install-checklist"]
relatedCommands: ["sha256sum"]
careerRelevance: ["devops", "linux-administrator", "wordpress-web-hosting"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ubuntu-install"]
relatedCheatsheet: ""
furtherReading: [{"label": "Ubuntu Server installation guide", "url": "https://ubuntu.com/server/docs/installation"}]
nextTopic: "02-installing-linux/installing-rhel-centos-rocky"
prevTopic: "02-installing-linux/virtualization-options"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["install ubuntu", "install debian", "ubuntu server installation", "iso checksum verification"]
canonicalUrl: "/docs/installing-linux/installing-ubuntu-debian"
---

# Installing Ubuntu/Debian

🟢 Must Know · Relevant for: DevOps · Linux Administrator · WordPress/Web Hosting

> **TL;DR:** Download the official ISO, verify its checksum before using it, choose Server
> (minimal, no desktop) for anything production-relevant, and create a non-root user with sudo
> access during setup — never plan to work as root day to day.

## What is it?

The process of getting Ubuntu or Debian installed and booted, whether onto a VM (from
[Virtualization Options](virtualization-options.md)) or a physical machine. Debian and Ubuntu
share an installer lineage and nearly identical decision points, so this page covers both.

## Why does it exist?

A install done carelessly (wrong edition, no checksum check, sloppy partitioning, root-only
login) creates problems that surface much later — a security gap, a partition that fills up, or
a machine that's inconsistent with the rest of a fleet. Professionals treat installation as a
repeatable, checked process, not a series of "next, next, next" clicks.

## Where is it used?

- **Ubuntu Desktop** — learning environments, developer workstations.
- **Ubuntu Server** (no GUI) — the far more common professional target: cloud VM base images,
  bare-metal servers, CI runners.
- **Debian** — the upstream Ubuntu is built from; chosen directly when a team wants maximum
  stability and a smaller footprint over Ubuntu's more frequent release cadence.

## How it works

> 📊 Diagram: flowchart of the install decision path — Download ISO → Verify Checksum → Create
> Bootable Media/Attach to VM → Choose Server or Desktop → Partitioning (guided vs manual) →
> Create User Account → First Boot.

The decisions that actually matter, in order:

1. **Verify the ISO checksum** before using it — corrupted or tampered installer media is a real
   (if uncommon) risk, and checking costs one command.
2. **Server vs Desktop edition** — Server has no GUI and a much smaller footprint; it's what
   virtually every production and cloud install uses. Desktop is for a learning/dev workstation.
3. **Partitioning** — guided (automatic, fine for learning) vs manual (lets you separate `/`,
   `/home`, `/var` onto different partitions/volumes — useful in production so a runaway log file
   in `/var` can't fill the entire disk).
4. **User account creation** — the installer creates a non-root user with sudo access; you should
   never plan to log in and work as `root` directly (full reasoning in
   [Module 08: Users](../08-users/index.md)).

## Real-world example

A platform team builds a "golden image" — an Ubuntu Server install done once, checksum-verified,
partitioned deliberately, hardened per their checklist — then clones that image for every new
cloud VM instead of manually installing Ubuntu on each one. The care taken on the first install
is what makes that cloning safe to trust.

## Commands

- [`sha256sum`](../../commands/sha256sum.md) — verify a downloaded ISO's checksum

## Production example

```
$ sha256sum ubuntu-22.04.4-live-server-amd64.iso
a435f6f13d6...  ubuntu-22.04.4-live-server-amd64.iso
```

Compare this output against the checksum published on Ubuntu's official download page — if they
don't match, the ISO is corrupted or tampered with and should not be used.

## Do / Don't

| Do | Don't |
|---|---|
| Verify the ISO checksum before installing | Trust a downloaded ISO without checking it |
| Choose Server edition for anything production-bound | Install Desktop edition on a server "just in case" |
| Create a non-root sudo user during setup | Plan to log in and work as root day to day |

## Common mistakes

- Skipping checksum verification — rare to matter, cheap to check, and the one time it does
  matter it saves you from installing from corrupted or compromised media.
- Installing Desktop edition on what will become a server — wastes resources and adds unnecessary
  attack surface (see [GUI vs CLI](../01-linux-basics/gui-vs-cli.md)).
- Accepting fully automatic partitioning on a machine that will run production workloads with
  large, growing log files — a single partition means `/var` filling up can crash the whole system.

## Best practices

- Always verify checksums for installer media, even when it feels unnecessary.
- Use Server edition and manual partitioning (separating at least `/`, `/home`, `/var`) for
  anything beyond a disposable learning VM.
- Standardize install decisions into a checklist or golden image once you're doing this more than
  once — see [Post-Install Setup Checklist](post-install-checklist.md).

## Exercises

1. Download an Ubuntu Server ISO and verify its checksum against the official published value.
2. Install Ubuntu Server into the VM you set up in [Virtualization Options](virtualization-options.md),
   choosing manual partitioning with separate `/`, `/home`, and `/var`.
3. Confirm you can log in as the non-root user created during install.

## Quiz

**Q: Why verify an ISO's checksum before installing from it?**
<details><summary>Show answer</summary>
To confirm the download wasn't corrupted or tampered with — a mismatched checksum means you
shouldn't trust or use that installer media.
</details>

**Q: Why is Server edition preferred over Desktop for production installs?**
<details><summary>Show answer</summary>
No GUI means less resource usage and a smaller attack surface — production servers are managed
over CLI/SSH, not a local desktop session (see GUI vs CLI).
</details>

## Interview questions

- Why would you separate `/var` onto its own partition on a server? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Verify checksums before using installer media — cheap insurance against corrupted downloads.
- Server edition, not Desktop, is the professional default for anything production-bound.
- Manual partitioning that separates `/var` from `/` prevents one runaway directory from filling
  the whole disk.
- Never plan to work as root directly — the installer's non-root sudo user is the correct default.

## Further Reading

- [Ubuntu Server installation guide](https://ubuntu.com/server/docs/installation)

## Related topics

- [Installing RHEL/CentOS/Rocky](installing-rhel-centos-rocky.md)
- [Post-Install Setup Checklist](post-install-checklist.md)
