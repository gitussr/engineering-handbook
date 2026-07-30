---
title: "Processes"
description: "Module 10 of the Linux roadmap, opening the Intermediate stage — what a process is, PIDs and the process tree, viewing and controlling processes, signals, priority, and zombies."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
type: "module-index"
nextTopic: "10-processes/what-is-a-process"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/processes"
---

# Processes

Module 10 of 34 · Stage: Intermediate · Previous: [09 Groups](../09-groups/index.md)

This is the first Intermediate-stage module and the concept the brief specifically calls out as
the model for this documentation: teach the concept first, then the commands. "Foreground vs
Background Processes" here builds on the shell job-control mechanics already covered in
[Module 04](../04-bash/job-control.md) rather than repeating them — this module explains what's
actually happening at the process level.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [What is a Process](what-is-a-process.md) | 🟢 Must Know |
| [Process Lifecycle and States](process-lifecycle-and-states.md) | 🟡 Good to Know |
| [PID, PPID, and the Process Tree](pid-ppid-and-process-tree.md) | 🟢 Must Know |
| [ps, top, htop](ps-top-htop.md) | 🟢 Must Know |
| [kill, killall, pkill, and Signals](kill-killall-pkill-and-signals.md) | 🟢 Must Know |
| [Foreground vs Background Processes](foreground-vs-background-processes.md) | 🟢 Must Know |
| [nice and renice](nice-and-renice.md) | 🟡 Good to Know |
| [Zombie and Orphan Processes](zombie-and-orphan-processes.md) | 🔴 Expert |
| [How systemd Manages Processes](systemd-process-management-preview.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain what a process is and how it differs from a program.
- Read a process tree and explain the PID/PPID relationship.
- Inspect running processes with `ps`, `top`, and `htop`, and know when to reach for each.
- Send the right signal to a process with `kill`/`killall`/`pkill`, and explain the difference
  between `SIGTERM` and `SIGKILL`.
- Explain what actually happens at the process level when something runs in the foreground versus
  background.
- Adjust process priority with `nice`/`renice`.
- Recognize a zombie versus an orphan process and explain why each occurs.
- Explain, at a preview level, how systemd supervises processes — full depth in Module 11.

## Known, intentional gaps in this module

- Canonical command pages exist for `ps`, `top`, `kill`, `killall`, `pkill`, `nice`, and
  `renice` — every process-inspection and process-control command this module centers on.
- `htop` is covered in full within its topic page but doesn't get a separate canonical command
  page — it's a third-party tool with a rich interactive UI rather than a flag-driven CLI surface
  well suited to this documentation's command-page format.
- `pstree` is referenced but not yet given a canonical page.

**Previous module:** [09 Groups](../09-groups/index.md)
**Next module:** [11 Services →](../11-services/index.md)
