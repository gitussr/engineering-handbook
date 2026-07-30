---
title: "What is a Process"
description: "A process is a program in motion — its own memory, its own PID, its own resources — and why that distinction from 'a program' matters."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["09-groups/group-based-permission-strategy"]
relatedTopics: ["process-lifecycle-and-states"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#process-vs-program"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "10-processes/process-lifecycle-and-states"
prevTopic: "09-groups/group-based-permission-strategy"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["what is a process linux", "process vs program", "process memory space"]
canonicalUrl: "/docs/processes/what-is-a-process"
---

# What is a Process

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator · Backend

> **TL;DR:** A program is a file on disk. A process is that program actually running — loaded
> into memory, assigned a PID, owned by a user, consuming CPU and RAM. One program can be many
> simultaneous processes.

## What is it?

A process is a running instance of a program: the program's code loaded into memory, plus its own
memory space, open files, and execution state, tracked by the kernel under a unique identifier
(the PID, covered next).

## Why does it exist?

The kernel needs to track and manage everything actually executing on the system — scheduling CPU
time, isolating memory between different running things, enforcing which user owns what. "Process"
is the unit the kernel manages for all of this; without it, there'd be no way to run more than one
thing, or to run the same program more than once, safely and independently.

## Where is it used?

Every single thing happening on a Linux system, at every moment, is one or more processes — from
the kernel's own init process (PID 1) down to a one-off `ls` command that exists for a fraction of
a second.

## How it works

> 📊 Diagram: one program file icon on disk labeled "nginx (binary)" with three arrows pointing to
> three separate process boxes, each labeled with a different PID — illustrating that one program
> can be running as multiple independent processes simultaneously.

- **Program**: a file on disk (an executable) — inert until run.
- **Process**: that program loaded into memory and actually executing, with its own PID, its own
  memory space (isolated from other processes), an owning user, and a set of resources (open
  files, network connections) it holds.
- One program can be **many simultaneous processes** — running `nginx` might mean one master
  process plus several worker processes, all from the same binary, each independently tracked.

## Real-world example

A web server binary (`/usr/sbin/nginx`) sits on disk as one file, but a running Nginx deployment
typically has one master process plus several worker processes — each a separate process with
its own PID, all originating from the exact same program file. Killing one worker process doesn't
touch the others; they're independent, even though they came from the same binary.

## Commands

No command example on this page — this page is conceptual context. See
[ps, top, htop](ps-top-htop.md) for the first hands-on process inspection in this module.

## Production example

Not applicable — see [PID, PPID, and the Process Tree](pid-ppid-and-process-tree.md) for the
first terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Distinguish "the program" from "a running instance of it" | Use "process" and "program" interchangeably when precision matters |
| Expect one program to potentially run as multiple independent processes | Assume killing one process from a program stops every instance of it |

## Common mistakes

- Using "process" and "program" interchangeably — imprecise in casual conversation, but a real
  source of confusion once multiple processes from the same program are involved.
- Assuming a program can only run as a single process at a time — many real services (web
  servers, database connection pools) run as multiple simultaneous processes by design.
- Not realizing each process has independent memory — one process crashing doesn't directly
  corrupt another's memory, by design (this isolation is a core OS guarantee).

## Best practices

- Use "process" specifically when referring to something actually running, and "program"/
  "binary" for the file on disk — the distinction matters in incident reports and technical
  writing.
- When investigating an issue with "the app," check whether it's actually running as multiple
  processes before assuming a single process's state tells the whole story.

## Exercises

1. Name a program on your system you'd expect to run as multiple simultaneous processes, and
   explain why.
2. Explain in one sentence the difference between a program and a process.
3. Explain why two processes running the exact same program don't share memory with each other.

## Quiz

**Q: Can one program be running as more than one process at the same time?**
<details><summary>Show answer</summary>
Yes — many real services (web servers, database systems) intentionally run as multiple
simultaneous processes from the same underlying program.
</details>

**Q: What does a process have that a program on disk doesn't?**
<details><summary>Show answer</summary>
A PID, its own memory space, an owning user, execution state, and held resources (open files,
connections) — none of which exist until the program is actually running.
</details>

## Interview questions

- What's the difference between a program and a process? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A program is a file on disk; a process is that program actually running, with its own PID and
  memory.
- One program can run as multiple independent, simultaneous processes.
- Process memory isolation is a core OS guarantee — one process's crash doesn't directly corrupt
  another's.

## Related topics

- [Process Lifecycle and States](process-lifecycle-and-states.md)
- [PID, PPID, and the Process Tree](pid-ppid-and-process-tree.md)
