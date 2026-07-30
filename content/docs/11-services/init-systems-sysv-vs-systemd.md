---
title: "init Systems: SysV vs systemd"
description: "Why this history is practically relevant, not trivia — legacy /etc/init.d scripts, runlevels, and the service compatibility wrapper you'll still encounter."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["11-services/service-dependencies-and-targets"]
relatedTopics: []
relatedCommands: []
careerRelevance: ["linux-administrator", "devops"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#sysv-vs-systemd"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "12-networking/networking-fundamentals"
prevTopic: "11-services/service-dependencies-and-targets"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["sysv init explained", "etc init.d scripts", "systemd vs sysv runlevels", "service command compatibility"]
canonicalUrl: "/docs/services/init-systems-sysv-vs-systemd"
---

# init Systems: SysV vs systemd

🟡 Good to Know · Relevant for: Linux Administrator · DevOps

> **TL;DR:** SysV init started services via numbered, sequential shell scripts under
> `/etc/init.d/`, organized into "runlevels." systemd replaced it with declarative unit files and
> parallel, dependency-based startup. This isn't just history — the legacy `service` command
> still works as a compatibility wrapper, and some systems still run genuine SysV-style scripts
> alongside systemd.

## What is it?

A practical comparison between systemd and its predecessor, SysV init — covered here specifically
because it explains real, still-encountered legacy behavior, not as background trivia. SysV
compatibility layers are still genuinely relevant on systems you'll actually work on today.

## Why does it exist?

Understanding *why* systemd replaced SysV init — and what specifically changed — explains real
things you'll still encounter: the `service` command still works on systemd systems as a
compatibility wrapper, some older or specialized software still ships SysV-style init scripts,
and "runlevel" terminology still shows up in documentation and job interviews even on
systemd-only systems.

## Where is it used?

- Legacy software that hasn't been updated to ship a proper systemd unit file
- The `service` command, still commonly used out of habit even on systemd systems, as a
  compatibility shim
- Understanding older documentation, forum answers, and tutorials that predate systemd

## How it works

> 📊 Diagram: two startup models contrasted — SysV: a numbered sequence of scripts
> (`S10networking`, `S20sshd`, `S99local`) executed strictly in order, one at a time, all within
> one "runlevel" — systemd: a dependency graph of units, executed with maximum parallelism
> wherever no real dependency requires waiting, organized around targets instead of numbered
> levels.

| | SysV init | systemd |
|---|---|---|
| Service definition | Shell script under `/etc/init.d/` | Declarative unit file |
| Startup order | Fixed, sequential, by filename number prefix | Parallel, based on declared dependencies |
| Grouping concept | Numbered runlevels (0-6) | Named targets (`multi-user.target`, etc.) |
| Control command | `service name start/stop` (or the script directly) | `systemctl start/stop name` |
| Logging | Wherever the script/service chose (no central store) | Centralized in the journal (`journalctl`) |

**Runlevels → targets (approximate mapping)**: runlevel 3 (multi-user, no GUI) roughly
corresponds to `multi-user.target`; runlevel 5 (multi-user with GUI) roughly corresponds to
`graphical.target` — "roughly," because it's a conceptual mapping for orientation, not a strict
technical equivalence.

## Real-world example

An engineer joins a team maintaining a legacy application that still ships only a SysV-style
`/etc/init.d/legacyapp` script, never updated for systemd. On a modern systemd-based distro, this
still works via systemd's SysV compatibility layer, and `service legacyapp restart` (the old
command) functions as expected — but it doesn't get systemd's dependency management, centralized
journal logging, or automatic restart-on-failure the way a proper unit file would, which is
exactly the case for eventually migrating it to a real `.service` file (this module's earlier
topic).

## Commands

No new command example on this page — the legacy `service` command is referenced as a forward
reference; [`systemctl`](../../commands/systemctl.md), already covered, is the modern equivalent.

## Production example

```
$ service nginx restart      # legacy command, works via compatibility layer
$ systemctl restart nginx    # modern equivalent, preferred
```

Both work on a systemd system with a proper unit file; only `systemctl` gives full access to
systemd's dependency management and centralized logging.

## Do / Don't

| Do | Don't |
|---|---|
| Prefer `systemctl` over the legacy `service` command going forward | Assume `service` gives you systemd's full feature set |
| Recognize "runlevel" terminology in older docs as roughly mapping to targets | Expect an exact, one-to-one technical mapping between runlevels and targets |
| Migrate legacy SysV-only services to proper unit files when practical | Assume a SysV compatibility script gets automatic restart-on-failure the way a real unit does |

## Common mistakes

- Assuming the `service` command gives identical functionality to `systemctl` — it works as a
  compatibility shim but doesn't expose systemd's full dependency management or logging
  integration.
- Treating runlevel-to-target mapping as an exact technical equivalence rather than a rough
  conceptual bridge for people coming from SysV-era knowledge.
- Assuming all modern systems are purely systemd with zero SysV remnants — legacy scripts and
  habits persist longer than the underlying init system transition itself.

## Best practices

- Use `systemctl` directly rather than the legacy `service` command when you have the choice.
- When encountering a SysV-style `/etc/init.d/` script for custom or legacy software, consider
  migrating it to a proper unit file (this module's earlier topic) to gain systemd's full feature
  set.
- Recognize "runlevel" language in documentation or interviews as a conceptual bridge to targets,
  not something you need to configure directly on a modern system.

## Exercises

1. Check whether `/etc/init.d/` still exists and has any scripts on a system you have access to.
2. Run both `service name status` and `systemctl status name` against the same service and
   compare the output.
3. Explain in one sentence why understanding SysV init still matters on a systemd-only system.

## Quiz

**Q: Does the `service` command still work on a modern systemd-based system?**
<details><summary>Show answer</summary>
Yes — it works as a compatibility wrapper, but doesn't expose systemd's full dependency
management or centralized journal logging the way `systemctl` and a proper unit file do.
</details>

**Q: What roughly corresponds to SysV's runlevel 3 in systemd?**
<details><summary>Show answer</summary>
`multi-user.target` — a rough conceptual mapping, not an exact technical equivalence.
</details>

## Interview questions

- Why might you still encounter SysV-style init scripts on a modern Linux system? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- SysV init used sequential shell scripts and numbered runlevels; systemd uses declarative unit
  files and parallel, dependency-based startup.
- The `service` command still works as a compatibility wrapper on systemd systems.
- "Runlevel" terminology roughly maps to systemd targets, useful for reading older
  documentation.
- Legacy SysV scripts still appear in practice — migrating them to unit files gains systemd's
  full feature set.

## Related topics

- [Module 12: Networking](../12-networking/index.md)
