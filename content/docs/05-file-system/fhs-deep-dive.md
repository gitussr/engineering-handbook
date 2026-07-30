---
title: "FHS Deep Dive"
description: "The Filesystem Hierarchy Standard as an actual maintained specification, not just convention — versioning, compliance, and why it matters for automation."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["01-linux-basics/linux-philosophy-and-fhs"]
relatedTopics: ["core-directories-explained"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#fhs-deep-dive"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "Filesystem Hierarchy Standard 3.0 specification", "url": "https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html"}]
nextTopic: "05-file-system/core-directories-explained"
prevTopic: "04-bash/bash-history-tricks"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["filesystem hierarchy standard", "fhs version", "linux foundation fhs", "fhs compliance"]
canonicalUrl: "/docs/file-system/fhs-deep-dive"
---

# FHS Deep Dive

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Software Engineering

> **TL;DR:** The FHS is a formally maintained specification (currently version 3.0, published by
> the Linux Foundation), not just informal convention — which is why automation, packaging, and
> monitoring tools can all safely assume `/etc` means config and `/var/log` means logs across
> almost any distro.

## What is it?

The Filesystem Hierarchy Standard (FHS) is a formal specification, maintained by the Linux
Foundation, defining the directory structure and contents on Linux systems. [Linux Philosophy and
the FHS](../01-linux-basics/linux-philosophy-and-fhs.md) introduced the concept and the Unix
philosophy behind it; this page covers the standard itself as a maintained specification.

## Why does it exist?

Before FHS, different Unix-like systems and even different Linux distros scattered files
inconsistently, making packages, scripts, and documentation hard to write generically. FHS gives
package maintainers, distro builders, and automation authors one specification to target, so a
package built for one FHS-compliant distro places its files predictably on any other.

## Where is it used?

- Package managers (`apt`, `dnf`) rely on FHS paths to install software predictably
- Configuration management and provisioning tools (Ansible, Terraform provisioners) reference FHS
  paths directly in their modules
- Monitoring and log-shipping agents default to scanning `/var/log` because FHS guarantees it's
  there
- Every distro's own documentation and packaging guidelines are written assuming FHS compliance

## How it works

> 📊 Diagram: a version timeline showing FHS 2.3 → FHS 3.0, with a callout on FHS 3.0's most
> practically relevant change — merging `/bin`, `/sbin`, and `/lib` into `/usr` equivalents via
> symlinks on modern distros (the "unified /usr" layout mentioned briefly in Module 01).

FHS is versioned like any specification — the current version is 3.0. Distros aren't required to
be 100% compliant, and most take small, deliberate deviations (e.g. `/srv` usage varies,
`/opt` conventions vary by vendor) — but the core structure (`/etc`, `/var`, `/home`, `/usr`,
`/tmp`) is followed closely enough across virtually every mainstream distro that "closely FHS
compliant" is a safe assumption for automation.

**Compliance vs. convention**: some things that feel like FHS rules are actually just widespread
convention (e.g. exactly which subdirectory a specific application puts its config in under
`/etc`). FHS specifies the top-level structure; deeper conventions below that are often
distro-specific or package-specific, layered on top of, not mandated by, FHS itself.

## Real-world example

An Ansible playbook written to deploy Nginx assumes config lives under `/etc/nginx/` and logs
under `/var/log/nginx/` — because that's not a guess, it's a direct consequence of FHS being a
real, versioned specification that Nginx's packagers on every mainstream distro comply with. The
same playbook works against Ubuntu, Debian, RHEL, and Rocky targets with zero path changes.

## Commands

No command example on this page — this page is specification-level context. See
[Core Directories Explained](core-directories-explained.md) for the first hands-on directory
inspection in this module.

## Production example

Not applicable — see [Core Directories Explained](core-directories-explained.md) for the first
terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Assume FHS core structure (`/etc`, `/var`, `/home`) across mainstream distros | Assume 100% FHS compliance for every subdirectory detail |
| Write automation targeting FHS paths, not distro-specific guesses | Hardcode paths that only happen to work on one distro you tested |

## Common mistakes

- Assuming FHS mandates exact subdirectory conventions below the top level (e.g. exactly which
  folder under `/etc` a specific app uses) — that level of detail is package/distro convention
  layered on top of FHS, not FHS itself.
- Writing automation that hardcodes a path that happens to work on the one distro tested, without
  checking whether it's a genuine FHS-guaranteed location or a distro-specific quirk.
- Treating a minor distro deviation from FHS as a "broken" system rather than a normal, expected
  degree of variance.

## Best practices

- When writing automation or documentation meant to be portable, target genuinely FHS-guaranteed
  paths (`/etc`, `/var/log`, `/home`) rather than assumptions specific to one distro you happened
  to test on.
- When something isn't where you expect, check whether it's an FHS-guaranteed location or a
  package-specific convention before assuming the system is misconfigured.

## Exercises

1. Look up which FHS version is current and name one structural change from the previous version.
2. Explain in one sentence the difference between FHS compliance and distro-specific convention.
3. Name one automation tool (from this documentation's later modules) that depends on FHS paths
   being predictable.

## Quiz

**Q: Is the FHS a formal specification or just informal convention?**
<details><summary>Show answer</summary>
A formal, versioned specification maintained by the Linux Foundation — not just informal habit,
which is exactly why automation tools can safely depend on it.
</details>

**Q: Does FHS compliance mean every distro is identical below the top-level directories?**
<details><summary>Show answer</summary>
No — FHS specifies the core top-level structure; deeper subdirectory conventions often vary by
distro or package, layered on top of FHS rather than mandated by it.
</details>

## Interview questions

- Why does automation tooling depend on the FHS being a real specification rather than just
  convention? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- FHS is a formal, versioned specification (currently 3.0), maintained by the Linux Foundation.
- Core structure (`/etc`, `/var`, `/home`, `/usr`, `/tmp`) is followed closely across mainstream
  distros; deeper details vary by distro/package convention.
- This predictability is exactly what package managers and automation tools depend on.
- FHS compliance is a spectrum, not all-or-nothing — small deviations are normal.

## Further Reading

- [Filesystem Hierarchy Standard 3.0 specification](https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html)

## Related topics

- [Linux Philosophy and the FHS](../01-linux-basics/linux-philosophy-and-fhs.md)
- [Core Directories Explained](core-directories-explained.md)
