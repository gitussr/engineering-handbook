---
title: "Zombie and Orphan Processes"
description: "A zombie is dead but not yet buried — waiting for its parent to acknowledge it. An orphan is alive with no parent left — and gets adopted by init automatically."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["10-processes/nice-and-renice"]
relatedTopics: ["systemd-process-management-preview"]
relatedCommands: []
careerRelevance: ["sre", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#zombie-process-explained"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "10-processes/systemd-process-management-preview"
prevTopic: "10-processes/nice-and-renice"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["zombie process explained", "orphan process linux", "defunct process ps", "zombie process cant kill"]
canonicalUrl: "/docs/processes/zombie-and-orphan-processes"
---

# Zombie and Orphan Processes

🔴 Expert · Relevant for: SRE · Linux Administrator · Software Engineering

> **TL;DR:** A **zombie** has finished running but still occupies a PID slot because its parent
> hasn't read its exit status yet — `kill` can't remove it, since it's already dead; the fix is
> making the parent reap it. An **orphan** is a running process whose parent exited first — the
> kernel automatically reparents it to init/systemd, which does know how to reap it.

## What is it?

Two distinct, often-confused edge cases in the parent/child relationship from
[PID, PPID, and the Process Tree](pid-ppid-and-process-tree.md): a **zombie** process has already
terminated but still has an entry in the process table, waiting for its parent to collect its
exit status. An **orphan** process is still actively running, but its original parent has already
exited.

## Why does it exist?

When a process exits, the kernel keeps a small record (exit status, resource usage) so the parent
can retrieve it via a system call — this is intentional, not a bug: the parent needs to know
*how* its child exited. A **zombie** is that brief, normal waiting state stretched out
indefinitely because the parent never calls the function that reads it ("reaping"). An
**orphan**, meanwhile, is what happens to a still-running child if its parent exits first — the
kernel needs *some* process responsible for eventually reaping it, so it reassigns orphans to
init/systemd (PID 1) automatically.

## Where is it used?

- Recognizing a zombie in `ps`/`top` output (state `Z`, or listed as "defunct")
- Understanding why `kill`ing a zombie does nothing (it's already dead — there's nothing left to
  signal)
- Recognizing that orphaned processes aren't inherently a problem — automatic reparenting to
  init/systemd is a normal, working part of the process lifecycle, not a failure state

## How it works

> 📊 Diagram: two separate small sequences — "Zombie": Child exits → Exit status held by kernel →
> Parent never calls wait() → Zombie persists in process table (dead, but visible). "Orphan":
> Parent exits while child still running → Kernel reparents the child to PID 1 → Child continues
> running normally, now a child of init/systemd.

| | Zombie | Orphan |
|---|---|---|
| Actually running? | No — already terminated | Yes — still actively running |
| The problem | Parent hasn't reaped its exit status | Original parent exited first |
| Kernel's response | None automatic — stays until the parent reaps it or the parent itself exits | Automatically reparented to PID 1 |
| Can you `kill` it? | No effect — it's already dead, nothing to signal | Not specifically relevant — it's a normal running process |
| Is it a problem? | Yes, if accumulating — indicates a bug in the parent | No — a normal, handled part of the process lifecycle |

**A large, growing number of zombies** is a real, diagnosable bug — almost always in the parent
process's code, which is failing to call `wait()`/`waitpid()` on its children. The fix is in the
parent's code, not something `kill` or any other tool can address directly on the zombie itself.

## Real-world example

A custom application server spawns worker processes for each incoming request but has a bug
where it never properly collects their exit status after they finish. Over days, `ps aux` shows
an accumulating list of `Z` (zombie) entries — each individually harmless (a zombie uses almost no
resources), but eventually exhausting the system's maximum process table entries, at which point
the application can no longer spawn new workers at all. The fix is in the application's own
code — no amount of `kill` on the zombies themselves would help, since they're already dead.

## Commands

No new command example on this page — `ps`/`top` (already covered) are how zombies and orphans
are actually observed; no new command is introduced here.

## Production example

```
$ ps aux | grep Z
appuser   5821  0.0  0.0      0     0 ?        Z    09:15   0:00 [worker] <defunct>
```

The `Z` state and `<defunct>` label both confirm this is a zombie — already terminated,
waiting to be reaped.

## Do / Don't

| Do | Don't |
|---|---|
| Investigate the parent process's code when zombies accumulate | Try to `kill` a zombie expecting it to disappear |
| Treat orphan reparenting to init as normal, expected behavior | Assume every orphaned process indicates something wrong |
| Restart the parent process as a last resort to clear its zombies | Ignore a growing zombie count assuming it's harmless indefinitely |

## Common mistakes

- Trying to `kill` a zombie process to "clean it up" — it's already dead; there's no process left
  to receive a signal, and `kill` will have no effect.
- Treating orphaned processes as inherently broken or dangerous — automatic reparenting to init
  is normal, expected kernel behavior, not a failure.
- Not recognizing that a growing zombie count points to a bug in the *parent's* code, and instead
  looking for a fix at the zombie process itself.

## Best practices

- When zombies accumulate, look at the parent process's code for missing `wait()`/`waitpid()`
  calls — that's where the actual bug lives.
- If the parent can't be fixed immediately, restarting it will cause the kernel to reparent
  (and then reap) any of its zombies as a workaround, though it doesn't address the root cause.
- Don't panic over occasional, individual zombies — a zombie briefly existing between a child's
  exit and its parent reaping it is completely normal; it's an accumulating, growing count that's
  the real signal.

## Exercises

1. Explain in one sentence why a zombie process can't simply be killed.
2. Explain what happens to a still-running process when its parent exits unexpectedly.
3. Explain where the actual fix belongs when zombie processes are accumulating.

## Quiz

**Q: Why doesn't `kill` work on a zombie process?**
<details><summary>Show answer</summary>
The zombie has already terminated — there's no running process left to receive a signal. It's
only an entry in the process table waiting to be reaped by its parent.
</details>

**Q: What happens to a running process when its parent exits before it does?**
<details><summary>Show answer</summary>
The kernel automatically reparents it to PID 1 (init/systemd), which will properly reap it when
it eventually exits — this is normal, expected behavior, not a failure state.
</details>

## Interview questions

- Why can't you kill a zombie process, and what's the actual fix when zombies accumulate? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A zombie has already terminated and is waiting for its parent to reap its exit status.
- An orphan is still running, with its original parent already exited — automatically reparented
  to init.
- `kill` has no effect on a zombie; the fix for accumulating zombies is in the parent's code.
- Orphan reparenting is normal; a growing zombie count is the real signal worth investigating.

## Related topics

- [PID, PPID, and the Process Tree](pid-ppid-and-process-tree.md)
- [How systemd Manages Processes](systemd-process-management-preview.md)
