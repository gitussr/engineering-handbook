---
title: "Kernel Tuning: sysctl"
description: "Reading and changing kernel runtime parameters with sysctl — common performance-relevant settings, and the difference between a runtime change and a persistent one."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["21-performance/network-performance-iperf-sar"]
relatedTopics: ["network-performance-iperf-sar", "benchmarking-tools"]
relatedCommands: ["sysctl"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#sysctl-persistence"]
relatedCheatsheet: ""
furtherReading: [{"label": "sysctl(8) man page", "url": "https://man7.org/linux/man-pages/man8/sysctl.8.html"}]
nextTopic: "21-performance/benchmarking-tools"
prevTopic: "21-performance/network-performance-iperf-sar"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["sysctl command examples", "sysctl.conf persistent", "vm.swappiness", "net.core.somaxconn tuning"]
canonicalUrl: "/docs/performance/kernel-tuning-sysctl"
---

# Kernel Tuning: sysctl

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `sysctl` reads and changes kernel runtime parameters. Changes via `sysctl -w` are
> runtime-only and lost on reboot — persist them in `/etc/sysctl.conf` or `/etc/sysctl.d/`. Tune
> deliberately, one parameter at a time, based on a confirmed bottleneck, not by copying a list of
> "recommended" settings.

## What is it?

`sysctl` reads and modifies kernel runtime parameters that control a wide range of behavior —
networking, virtual memory, filesystem limits — without requiring a reboot or recompiling the
kernel.

## Why does it exist?

Many kernel behaviors that affect performance (how aggressively the system swaps, how many
connections a socket can queue) have sensible general-purpose defaults that aren't necessarily
optimal for every workload. `sysctl` exists to adjust these parameters at runtime, informed by the
specific bottleneck a workload actually experiences — closing the loop after this module's earlier
pages confirm what that bottleneck is.

## Where is it used?

Tuning a database server's virtual memory behavior, adjusting a web server's network connection
queue limits under high concurrent load, and any performance investigation where the earlier pages
in this module have confirmed a specific, tunable kernel-level constraint.

## How it works

> 📊 Diagram: a running kernel with a settings table inside it, `sysctl` shown reading and writing
> entries in that table directly (runtime, immediate, lost on reboot) — beside a separate
> `/etc/sysctl.conf` file shown being read only at boot time to repopulate that same table,
> illustrating why a `sysctl -w` change and a persisted config file change are two separate steps.

**Reading and writing parameters:**

```bash
sysctl vm.swappiness                    # read a specific parameter
sysctl -a                               # list all current parameters
sudo sysctl -w vm.swappiness=10         # change at runtime (temporary)
```

**Persisting a change — a separate, deliberate step:**

```bash
# /etc/sysctl.d/99-custom.conf
vm.swappiness = 10
net.core.somaxconn = 4096
```

```bash
sudo sysctl -p /etc/sysctl.d/99-custom.conf   # apply a config file's settings immediately
```

A `sysctl -w` change alone is runtime-only and silently reverts on the next reboot — persisting it
in `/etc/sysctl.d/` (or `/etc/sysctl.conf`) is a distinct, easily-forgotten step.

**Common performance-relevant parameters:**

| Parameter | Controls |
|---|---|
| `vm.swappiness` | How aggressively the kernel swaps (0-100; lower favors keeping data in RAM) |
| `net.core.somaxconn` | Maximum queued connections for a listening socket — relevant under high concurrent connection load |
| `fs.file-max` | System-wide maximum open file descriptors |
| `net.ipv4.tcp_fin_timeout` | How long a closed TCP connection stays in `TIME_WAIT` |

## Real-world example

A database server swaps more aggressively than the team wants, even with ample free memory,
because the default `vm.swappiness` (favoring some swap usage even when memory isn't tight)
doesn't suit a workload that strongly prefers keeping data in RAM. After confirming via
[`vmstat`](../../commands/vmstat.md) (earlier in this module) that swapping is indeed occurring
unnecessarily, lowering `vm.swappiness` and persisting it in `/etc/sysctl.d/` resolves the issue —
but only after the earlier diagnostic pages confirmed swapping was actually happening, not as a
first guess.

## Syntax

```
sysctl PARAMETER
sysctl -w PARAMETER=VALUE
sysctl -p [FILE]
```

## Commands

See [`sysctl`](../../commands/sysctl.md) for the full reference.

## Production example

```
$ sysctl vm.swappiness
vm.swappiness = 60

$ sudo sysctl -w vm.swappiness=10
vm.swappiness = 10

$ echo "vm.swappiness = 10" | sudo tee /etc/sysctl.d/99-custom.conf
$ sudo sysctl -p /etc/sysctl.d/99-custom.conf
vm.swappiness = 10
```

Change applied at runtime, then explicitly persisted — both steps required for the change to
survive a reboot.

## Do / Don't

| Do | Don't |
|---|---|
| Persist a tuning change in `/etc/sysctl.d/`, not just runtime | Assume a `sysctl -w` change survives a reboot |
| Tune one parameter at a time, based on a confirmed bottleneck | Apply a list of "recommended" tuning settings without understanding what each does |
| Document why a non-default value was set | Leave a tuned parameter unexplained for the next engineer to find |

## Common mistakes

- Making a runtime-only change with `sysctl -w` and assuming it's permanent, only to have it
  silently revert on the next reboot.
- Copying a generic list of "performance tuning" `sysctl` settings from the internet without
  understanding what each one actually does for this specific workload.
- Tuning multiple parameters simultaneously, making it impossible to know which change actually
  caused an observed effect.

## Best practices

- Always persist a confirmed-useful change in `/etc/sysctl.d/`, treating the runtime change as a
  test step, not the final action.
- Change and evaluate one parameter at a time, based on a bottleneck this module's earlier pages
  already confirmed — never as a first guess.
- Comment persisted changes with the reason they were made, so the next engineer understands the
  intent rather than just seeing an unexplained non-default value.

## Exercises

1. Read the current value of `vm.swappiness` on a system you have access to.
2. Explain the difference between `sysctl -w` and persisting a value in `/etc/sysctl.d/`.
3. Describe why tuning parameters one at a time matters for understanding cause and effect.

## Quiz

**Q: Does a change made with sysctl -w persist across a reboot?**
<details><summary>Show answer</summary>
No — it's a runtime-only change that's lost on reboot unless separately persisted in
`/etc/sysctl.conf` or a file under `/etc/sysctl.d/`.
</details>

**Q: Why is tuning one parameter at a time recommended over changing several at once?**
<details><summary>Show answer</summary>
Changing multiple parameters simultaneously makes it impossible to isolate which specific change
caused an observed effect, undermining the measure-first discipline from this module's opening
page.
</details>

**Q: What does vm.swappiness control?**
<details><summary>Show answer</summary>
How aggressively the kernel swaps memory to disk — a lower value favors keeping data in RAM even
when some memory pressure exists.
</details>

## Interview questions

- How would you persist a sysctl tuning change so it survives a reboot? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `sysctl` reads and changes kernel runtime parameters without requiring a reboot.
- `sysctl -w` is runtime-only — persisting a change requires a separate step in
  `/etc/sysctl.d/`.
- Tune one confirmed-relevant parameter at a time, never a copied list applied blindly.
- This page closes the loop on the module's measure-first discipline — tuning follows diagnosis,
  not the other way around.

## Related topics

- [Network Performance: iperf, sar](network-performance-iperf-sar.md)
- [Benchmarking Tools](benchmarking-tools.md)
