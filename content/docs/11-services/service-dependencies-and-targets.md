---
title: "Service Dependencies and Targets"
description: "After=, Requires=, Wants= — the difference between ordering and actually requiring, and how targets replaced SysV runlevels."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["11-services/journalctl-basics"]
relatedTopics: ["init-systems-sysv-vs-systemd"]
relatedCommands: []
careerRelevance: ["linux-administrator", "sre", "devops"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#requires-vs-wants"]
relatedCheatsheet: "systemctl"
furtherReading: []
nextTopic: "11-services/init-systems-sysv-vs-systemd"
prevTopic: "11-services/journalctl-basics"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["systemd after vs requires", "systemd wants vs requires", "systemd targets explained", "multi-user target"]
canonicalUrl: "/docs/services/service-dependencies-and-targets"
---

# Service Dependencies and Targets

🔴 Expert · Relevant for: Linux Administrator · SRE · DevOps

> **TL;DR:** `After=` controls *ordering only* — it doesn't mean the other unit must succeed or
> even run. `Requires=`/`Wants=` control actual dependency — `Requires=` fails this unit if the
> dependency fails; `Wants=` doesn't. Targets (like `multi-user.target`) are systemd's replacement
> for SysV runlevels — a named synchronization point, not a script.

## What is it?

The directives that let a unit declare its relationship to other units: ordering (`After=`,
`Before=`) and actual dependency (`Requires=`, `Wants=`), plus **targets** — special units that
group other units together as a named milestone (like "the system has reached normal
multi-user operation").

## Why does it exist?

[Module 11's systemd overview](systemd-overview.md) explained that parallel startup depends on
units declaring real dependencies. This is the mechanism: without these directives, systemd has
no way to know that service B genuinely needs service A to be up first, versus B merely being
started after A by coincidence in the old SysV ordering.

## Where is it used?

Any unit file where startup order or dependency genuinely matters — a web application that needs
the network to be up first, a service that needs a database running before it starts, or a
custom service that should be considered part of normal system startup.

## How it works

> 📊 Diagram: two separate relationship types shown side by side — "Ordering only" (`After=`): two
> boxes with a simple sequence arrow, no failure propagation shown. "Real dependency"
> (`Requires=`): two boxes with a sequence arrow AND a red failure-propagation arrow, showing that
> if the required unit fails, the dependent unit fails too.

| Directive | Controls | If the referenced unit fails |
|---|---|---|
| `After=` | Ordering only — start after, nothing about success | No effect on this unit |
| `Before=` | Ordering only — start before another unit | No effect on this unit |
| `Requires=` | Real dependency — this unit needs that one | This unit fails/stops too |
| `Wants=` | Soft dependency — prefer that one to be running | This unit continues anyway |

**The critical distinction**: `After=` alone is a very common mistake — it only affects *order*,
not whether the referenced unit actually succeeded. A unit with only `After=network.target` will
still try to start even if networking never actually came up; `Requires=network-online.target`
(a stricter, "actually online" variant) is what's needed if the dependency must genuinely
succeed first.

**Targets**: `multi-user.target` (normal multi-user system, no GUI) and `graphical.target`
(adds a desktop environment) are the modern replacements for SysV's numbered runlevels (this
module's final topic covers that history). A unit's `[Install]` section `WantedBy=multi-user.target`
(seen in the previous topic's example) means "include this unit as part of normal system
startup."

## Real-world example

A custom application's unit file declares `After=postgresql.service` but not
`Requires=postgresql.service`. During a routine reboot, PostgreSQL happens to fail to start (a
disk issue) — the application still starts anyway, immediately fails to connect to its database,
and crash-loops, because `After=` only controlled ordering, not an actual dependency guarantee.
Changing to `Requires=postgresql.service` would have made the application unit fail cleanly
instead, with a clear dependency-failure message, rather than crash-looping against a database
that was never going to be there.

## Commands

No new command example on this page — `systemd-analyze` (for visualizing dependency chains and
boot time) is a forward reference; unit file syntax itself was covered in the previous topic.

## Production example

```ini
[Unit]
Description=My Application
After=postgresql.service
Requires=postgresql.service

[Service]
ExecStart=/opt/myapp/bin/myapp

[Install]
WantedBy=multi-user.target
```

`After=` and `Requires=` are commonly used together — `Requires=` alone doesn't guarantee
ordering, so both are typically declared when a real, ordered dependency exists.

## Do / Don't

| Do | Don't |
|---|---|
| Use `Requires=` (not just `After=`) when a dependency must actually succeed | Assume `After=` alone guarantees the other service is actually working |
| Declare both `After=` and `Requires=` together for a real dependency | Rely on `Requires=` alone without `After=` for ordering |
| Use `WantedBy=multi-user.target` for a normal, non-graphical service | Guess at which target to use without checking what similar services declare |

## Common mistakes

- Using only `After=` when a genuine dependency exists, leading to a service starting even when
  its actual dependency failed — the exact failure in the real-world example above.
- Assuming `Requires=` alone also controls ordering — it doesn't; `After=` is still needed
  alongside it for that.
- Not understanding targets replaced runlevels conceptually, and trying to map SysV runlevel
  numbers directly onto specific targets one-to-one (the mapping is similar but not perfectly
  identical).

## Best practices

- Use `Requires=` alongside `After=` (not `After=` alone) whenever a dependency genuinely must
  succeed first.
- Use `Wants=` for a soft, preferred-but-not-mandatory relationship.
- Check existing, similar unit files for which target they use before guessing.

## Exercises

1. Find a real unit file (e.g. `nginx.service`) and identify its `After=`/`Requires=`/`Wants=`
   directives, if any.
2. Explain in one sentence the practical difference between `After=` and `Requires=`.
3. Explain what `WantedBy=multi-user.target` in a unit's `[Install]` section actually
   accomplishes.

## Quiz

**Q: If Service B has `After=ServiceA` but not `Requires=ServiceA`, and ServiceA fails to start,
does ServiceB still try to start?**
<details><summary>Show answer</summary>
Yes — `After=` only controls ordering, not an actual dependency guarantee. ServiceB will still
attempt to start, just after ServiceA's start attempt, regardless of whether it succeeded.
</details>

**Q: What replaced SysV's numbered runlevels in systemd?**
<details><summary>Show answer</summary>
Targets — named synchronization points like `multi-user.target` and `graphical.target`, grouping
units together as milestones rather than numbered levels.
</details>

## Interview questions

- What's the difference between `After=` and `Requires=` in a systemd unit file? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `After=`/`Before=` control ordering only; `Requires=`/`Wants=` control actual dependency.
- A unit with only `After=` still starts even if its referenced unit failed — a real, common
  mistake.
- Targets (`multi-user.target`, `graphical.target`) replaced SysV runlevels as named milestones.
- Real dependencies typically need both `After=` and `Requires=` declared together.

## Related topics

- [init Systems: SysV vs systemd](init-systems-sysv-vs-systemd.md)
