---
title: "Process Lifecycle and States"
description: "Running, sleeping, stopped, zombie — the states a process moves through, and why a process spends most of its life waiting, not running."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["10-processes/what-is-a-process"]
relatedTopics: ["pid-ppid-and-process-tree"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#process-states"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "10-processes/pid-ppid-and-process-tree"
prevTopic: "10-processes/what-is-a-process"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["linux process states", "running vs sleeping process", "process state D uninterruptible sleep"]
canonicalUrl: "/docs/processes/process-lifecycle-and-states"
---

# Process Lifecycle and States

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** A process spends most of its life **sleeping** (waiting for something — input, a
> timer, a disk read), not actually running on the CPU. "Running" (`R`) and "sleeping" (`S`) are
> normal; "stopped" (`T`) and "uninterruptible sleep" (`D`) are the states worth paying attention
> to when troubleshooting.

## What is it?

The set of states a process moves through during its life, tracked and reported by the kernel
(visible in `ps`/`top` output as a single-letter state code) — from creation, through running and
waiting, to termination.

## Why does it exist?

A CPU can only actually execute one thing per core at any instant, but a system runs far more
processes than it has cores — most processes are waiting for something (disk I/O, network data,
user input) rather than actively computing. Process states are how the kernel tracks and reports
what each process is actually doing (or waiting for) at any given moment.

## Where is it used?

Every `ps`/`top` output includes a state column — reading it correctly is a genuine
troubleshooting skill: a process stuck in an unusual state for a long time is often the actual
symptom worth investigating, not just background noise.

## How it works

> 📊 Diagram: a state transition diagram — Running (R) ⇄ Sleeping (S, interruptible) with arrows
> both ways (a process constantly moves between actually executing and waiting), a side branch to
> Stopped (T, via a signal), Uninterruptible Sleep (D, waiting on I/O that can't be interrupted),
> and finally Zombie (Z, terminated but not yet reaped) as a dead-end state before full removal.

| State | Code | Meaning |
|---|---|---|
| Running | `R` | Actually executing on a CPU, or ready to run |
| Sleeping (interruptible) | `S` | Waiting for something (input, a timer, a signal) — the most common state, entirely normal |
| Uninterruptible sleep | `D` | Waiting specifically on I/O (usually disk) in a way that can't be interrupted by a signal — normal briefly, a red flag if stuck here for a long time |
| Stopped | `T` | Paused (e.g. via `Ctrl+Z`, covered in [Module 04's Job Control](../04-bash/job-control.md), or a `SIGSTOP` signal) |
| Zombie | `Z` | Terminated, but its exit status hasn't yet been read by its parent — covered fully in [Zombie and Orphan Processes](zombie-and-orphan-processes.md) |

A process spending most of its observed lifetime in `S` is completely normal — it means the
kernel is efficiently not giving CPU time to something that isn't ready to use it. A process stuck
in `D` for an extended period, however, usually points to a real problem: often disk I/O that's
failing or severely degraded.

## Real-world example

An SRE investigating a slow server notices several processes stuck in `D` state (uninterruptible
sleep) for minutes at a time in `ps` output — a strong signal the disk subsystem itself is the
actual bottleneck, not the applications running on it. Processes in `D` state also famously can't
be killed with a normal `kill` (not even `SIGKILL`) precisely because they're waiting on I/O the
kernel won't interrupt — a genuinely different situation from a merely slow but responsive
process.

## Commands

No command example on this page — process state inspection commands (`ps`, `top`) are covered in
[ps, top, htop](ps-top-htop.md), two topics ahead.

## Production example

Not applicable — see [ps, top, htop](ps-top-htop.md) for state-column inspection in practice.

## Do / Don't

| Do | Don't |
|---|---|
| Treat `S` (sleeping) as normal, expected process behavior | Assume every non-`R` process is a problem |
| Investigate the disk subsystem when processes are stuck in `D` for a long time | Try to `kill` a `D`-state process expecting it to respond immediately |

## Common mistakes

- Assuming a process in `S` (sleeping) state is a problem — it's the most common, entirely normal
  state for the vast majority of processes at any given moment.
- Not recognizing `D` (uninterruptible sleep) as a meaningful signal — a process genuinely stuck
  there for a long time usually points to a disk I/O problem, not an application bug.
- Trying to kill a `D`-state process and being confused when even `SIGKILL` doesn't work
  immediately — full signal behavior covered in
  [kill, killall, pkill, and Signals](kill-killall-pkill-and-signals.md).

## Best practices

- Read the state column in `ps`/`top` output as actual diagnostic information, not something to
  skip past.
- When multiple processes are stuck in `D` simultaneously, investigate storage/disk health before
  assuming an application-level cause.

## Exercises

1. Run `ps aux` (full syntax in the next topic) and identify the state column for several
   processes.
2. Explain in one sentence why most processes spend most of their time in `S`, not `R`.
3. Explain why a `D`-state process resists being killed immediately.

## Quiz

**Q: Is it normal for most processes to be in "sleeping" state most of the time?**
<details><summary>Show answer</summary>
Yes — most processes spend the majority of their life waiting for something (input, I/O, a timer)
rather than actively executing on a CPU.
</details>

**Q: What does a process stuck in `D` state for a long time usually indicate?**
<details><summary>Show answer</summary>
A problem with the disk/I/O subsystem — the process is waiting on I/O in an uninterruptible way,
and prolonged `D` state is a real diagnostic signal, not routine behavior.
</details>

## Interview questions

- What does it mean if several processes are stuck in D state? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Processes move through states: Running, Sleeping, Uninterruptible Sleep, Stopped, Zombie.
- Sleeping (`S`) is the normal, dominant state — not a sign of a problem.
- Uninterruptible sleep (`D`) stuck for a long time is a real signal, usually disk I/O related.
- A `D`-state process resists even `SIGKILL` until its I/O wait resolves.

## Related topics

- [PID, PPID, and the Process Tree](pid-ppid-and-process-tree.md)
- [Zombie and Orphan Processes](zombie-and-orphan-processes.md)
