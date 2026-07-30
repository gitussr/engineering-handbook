---
title: "Post-Install Setup Checklist"
description: "The standard steps a professional runs immediately after any fresh Linux install or cloud VM launch, before doing anything else with it."
module: "02-installing-linux"
moduleTitle: "Installing Linux"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["02-installing-linux/dual-boot-considerations"]
relatedTopics: ["cloud-vm-setup", "installing-ubuntu-debian"]
relatedCommands: ["apt", "dnf", "hostnamectl", "useradd"]
careerRelevance: ["devops", "cloud", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#post-install-checklist"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "03-terminal/terminal-vs-shell-vs-console"
prevTopic: "02-installing-linux/dual-boot-considerations"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["linux post install checklist", "server hardening basics", "hostnamectl", "initial server setup"]
canonicalUrl: "/docs/installing-linux/post-install-checklist"
---

# Post-Install Setup Checklist

🟢 Must Know · Relevant for: DevOps · Cloud · Linux Administrator · SRE

> **TL;DR:** Every fresh install — local VM or cloud instance — gets the same five steps before
> real use: update packages, set the hostname, confirm a non-root sudo user exists, lock down SSH
> to key-only auth, and sync the clock. Script this once you're doing it more than twice.

## What is it?

The fixed sequence of steps a professional runs on any brand-new Linux system — whether it's a
local VM from [Installing Ubuntu/Debian](installing-ubuntu-debian.md) or a cloud instance from
[Spinning Up a Cloud VM](cloud-vm-setup.md) — before treating it as ready for real work.

## Why does it exist?

A fresh install is not a finished, safe starting point by itself: it may have out-of-date
packages, a generic hostname, permissive defaults, and a clock that isn't synced. Without a
consistent checklist, every machine in a fleet starts from a slightly different, unverified
baseline — which becomes a debugging and security problem later.

## Where is it used?

Every new VM, every new cloud instance, every reinstall — this is the single most-repeated
procedure in this entire documentation, which is exactly why it's worth memorizing rather than
looking up each time.

## How it works

> 📊 Diagram: a checklist-style flowchart — Update Packages → Set Hostname → Confirm Non-Root
> Sudo User → Configure Key-Only SSH → Sync Time (NTP) → (Ready for use) — framed as a linear
> gate, not optional steps.

1. **Update packages** — a fresh ISO is frequently already behind on security patches by release
   day.
2. **Set the hostname** — a machine named `localhost` or `ubuntu-server` is unidentifiable in a
   fleet; give it a meaningful name immediately.
3. **Confirm a non-root sudo user exists** — covered during install
   ([Installing Ubuntu/Debian](installing-ubuntu-debian.md)), verify it here before moving on.
4. **Configure key-only SSH access** — disable password authentication so only key-holders can
   log in (full detail in [Module 13: SSH](../13-ssh/index.md); this page only flags it as a
   required step, not the full how-to).
5. **Sync time (NTP)** — an unsynced clock silently corrupts log timestamps, which becomes a real
   problem the first time you're correlating logs across multiple servers during an incident.

## Real-world example

A platform team encodes this exact five-step checklist into their provisioning automation (a
preview of what [Module 18: Shell Scripting](../18-shell-scripting/index.md) and later
Ansible-style tooling formalize) so every new server in their fleet starts from the same known,
patched, correctly-named, key-only-SSH baseline — with zero manual steps and zero chance of a
team member forgetting one.

## Commands

- [`apt`](../../commands/apt.md) — update packages on Debian/Ubuntu (`apt update && apt upgrade`)
- [`dnf`](../../commands/dnf.md) — update packages on RHEL/Rocky (`dnf upgrade`)
- [`hostnamectl`](../../commands/hostnamectl.md) — view and set the system hostname
- [`useradd`](../../commands/useradd.md) — confirm/create a non-root user (full detail in
  [Module 08: Users](../08-users/index.md))

## Production example

```
$ sudo apt update && sudo apt upgrade -y
$ sudo hostnamectl set-hostname web-prod-07
$ hostnamectl
   Static hostname: web-prod-07
```

Three commands, run in this order, on every single new machine — this is the actual muscle
memory the checklist is building.

## Do / Don't

| Do | Don't |
|---|---|
| Update packages before doing anything else on a fresh install | Assume a fresh ISO is already fully patched |
| Give every machine a meaningful hostname | Leave the default `localhost`/generic hostname in a fleet |
| Disable SSH password auth in favor of keys | Leave password-based root SSH login enabled |
| Sync the clock (NTP) on every new machine | Ignore clock sync until log timestamps stop matching |

## Common mistakes

- Skipping the update step because "it's a fresh install, it should be current" — ISOs are built
  before their release date and are frequently already behind by the time you install them.
- Leaving default SSH settings (password auth enabled, sometimes root login enabled) — the exact
  exposure flagged in [Spinning Up a Cloud VM](cloud-vm-setup.md)'s common mistakes.
- Skipping time sync — logs across multiple servers become impossible to correlate accurately
  during an incident if their clocks drift apart.

## Best practices

- Treat this checklist as non-negotiable, run in this order, on every new machine without
  exception.
- Once you're repeating it by hand more than a couple of times, script it (Module 18) or automate
  it with a provisioning tool — manual repetition is where steps get skipped.
- Keep the checklist itself under version control so it evolves deliberately, not by accident.

## Exercises

1. On a fresh VM or cloud instance, run through all five checklist steps in order.
2. Verify the hostname change with `hostnamectl` after setting it.
3. Explain in one sentence why an unsynced clock is a problem specifically during incident
   response, not just in general.

## Quiz

**Q: Why update packages immediately after a fresh install, even though the ISO is "new"?**
<details><summary>Show answer</summary>
ISOs are built before their release date and often ship with packages that already have
published security patches by the time you actually install from them.
</details>

**Q: Why does clock sync matter for a server that isn't doing anything time-sensitive?**
<details><summary>Show answer</summary>
Even a non-time-sensitive server writes logs with timestamps — an unsynced clock makes those
timestamps unreliable for correlating events across multiple servers during troubleshooting.
</details>

## Interview questions

- What steps would you take immediately after provisioning a new production server? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Five steps, every time: update packages, set hostname, confirm non-root sudo user, key-only
  SSH, sync time.
- A fresh install is a starting point, not a finished, safe baseline.
- This checklist is the most-repeated procedure in the entire documentation — worth memorizing.
- Script it once you're running it more than a couple of times by hand.

## Related topics

- [Spinning Up a Cloud VM](cloud-vm-setup.md)
- [Module 13: SSH](../13-ssh/index.md)
- [Module 18: Shell Scripting](../18-shell-scripting/index.md)
