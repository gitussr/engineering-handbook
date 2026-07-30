---
title: "Foreground vs Background Processes"
description: "What actually happens at the process level when something runs in the foreground versus background — the mechanism behind Module 04's bg/fg/jobs commands."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["10-processes/kill-killall-pkill-and-signals"]
relatedTopics: ["nice-and-renice"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#sighup-nohup"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "10-processes/nice-and-renice"
prevTopic: "10-processes/kill-killall-pkill-and-signals"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["controlling terminal linux", "sighup explained", "foreground process group"]
canonicalUrl: "/docs/processes/foreground-vs-background-processes"
---

# Foreground vs Background Processes

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** [Module 04](../04-bash/job-control.md) already taught the commands (`&`, `bg`, `fg`,
> `jobs`, `nohup`) — this page explains the mechanism underneath them: a process is tied to its
> **controlling terminal**, and closing that terminal sends `SIGHUP` to every process still
> attached to it. `nohup` works by making a process ignore that specific signal.

## What is it?

The process-level explanation of what Module 04's job control commands actually manipulate: every
process launched from a terminal session is associated with that session's **controlling
terminal**, and whether it's "foreground" or "background" determines whether it receives
terminal-generated signals like `SIGINT` (`Ctrl+C`) directly.

## Why does it exist?

[Job Control](../04-bash/job-control.md) already covered *how* to move a process between
foreground and background — this page exists because *why* a background process survives
`Ctrl+C` but dies when you close the terminal (unless protected) only makes sense once you
understand the controlling terminal and signal delivery covered in this module's previous topic.

## Where is it used?

Every terminal session, every SSH connection — and specifically, understanding this is what
explains the exact `nohup`/multiplexer distinction Module 04 flagged but didn't fully explain
the mechanism behind.

## How it works

> 📊 Diagram: a terminal session box containing a foreground process (directly connected to
> keyboard input and receiving `Ctrl+C` as `SIGINT`) and a background process (still attached to
> the same controlling terminal, but not receiving keyboard-generated signals directly) — with a
> dotted line from the terminal itself to both processes, labeled "SIGHUP sent to both if the
> terminal closes."

- **Controlling terminal**: the terminal session a process was launched from. A foreground process
  receives keyboard-generated signals (`Ctrl+C` → `SIGINT`) directly; a background process
  doesn't, but is still attached to the same controlling terminal.
- **`SIGHUP`** ("hang up," historically from modem/serial line disconnects): sent by the kernel to
  every process still attached to a controlling terminal when that terminal closes — including
  background processes, which is exactly why a background job dies when you close the terminal
  window, unless something prevents it.
- **`nohup`** (Module 04) works by making a process explicitly ignore `SIGHUP` — this page is
  what makes that mechanism make sense rather than being a command to memorize without
  understanding.
- A terminal **multiplexer** (Module 03) solves the same problem differently: the session's
  controlling terminal never actually closes (it keeps running on the server), so `SIGHUP` is
  never sent at all, regardless of your local connection dropping.

## Real-world example

An engineer runs a long export script with `&` (background) and closes their terminal, assuming
the background flag alone was enough — the script dies immediately, because backgrounding a
process changes whether it receives keyboard signals directly, but does nothing about `SIGHUP`
when the controlling terminal itself closes. Understanding this is exactly why Module 04
recommended `nohup` or a multiplexer for anything that needs to survive a disconnect — this page
is the "why" behind that recommendation.

## Commands

No new command example on this page — the commands (`&`, `bg`, `fg`, `jobs`, `nohup`) were fully
covered in [Module 04: Job Control](../04-bash/job-control.md); this page explains the mechanism
they rely on, using [`kill`](../../commands/kill.md) to demonstrate SIGHUP directly.

## Production example

```
$ sleep 300 &
[1] 24831
$ kill -HUP 24831
[1]+  Hangup                  sleep 300
```

Manually sending SIGHUP to a background process demonstrates exactly what happens automatically
when its controlling terminal closes — and why `nohup` (which makes a process ignore this exact
signal) is the fix.

## Do / Don't

| Do | Don't |
|---|---|
| Understand `&` alone doesn't protect against `SIGHUP` on terminal close | Assume backgrounding a process is sufficient for surviving a disconnect |
| Use `nohup` or a multiplexer for anything that must survive a closed terminal | Rely on `&` alone for long-running remote work |

## Common mistakes

- Assuming `&` (backgrounding) alone protects a process from a closed terminal — it changes
  signal delivery for keyboard input, not `SIGHUP` on terminal close.
- Not connecting `nohup`'s behavior to the specific signal it's ignoring — treating it as magic
  rather than "makes the process ignore SIGHUP specifically."
- Assuming a multiplexer and `nohup` work by the same mechanism — a multiplexer prevents `SIGHUP`
  from being sent at all (the controlling terminal doesn't close); `nohup` lets the signal arrive
  but makes the process ignore it.

## Best practices

- When choosing between `nohup` and a multiplexer for a long-running task, understand they solve
  the same problem via different mechanisms — a multiplexer is generally more robust since it
  also lets you reattach and interact with the session later.
- Use this page's mental model (controlling terminal, SIGHUP) to reason about new, unfamiliar
  "why did my background process die" situations, not just memorize the `nohup` fix.

## Exercises

1. Start a background process and manually send it `SIGHUP` with `kill -HUP`, observing what
   happens.
2. Repeat, but start the process with `nohup` first, and observe the difference.
3. Explain in one sentence why a terminal multiplexer avoids the SIGHUP problem entirely, rather
   than just handling it like `nohup` does.

## Quiz

**Q: What signal does closing a terminal send to processes still attached to it?**
<details><summary>Show answer</summary>
`SIGHUP` — sent to every process still associated with that controlling terminal, including
background ones.
</details>

**Q: Does running a process with `&` alone protect it from `SIGHUP` when the terminal closes?**
<details><summary>Show answer</summary>
No — `&` only affects whether the process receives keyboard-generated signals directly; `nohup`
or a multiplexer is needed to actually survive the terminal closing.
</details>

## Interview questions

- Why does a background process sometimes die when you close your terminal, and how would you
  prevent that? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Every process is tied to a controlling terminal; foreground vs. background affects direct
  keyboard signal delivery, not survival of terminal closure.
- Closing a terminal sends `SIGHUP` to every attached process, including background ones.
- `nohup` works specifically by making a process ignore `SIGHUP`.
- A multiplexer avoids the problem entirely, since its controlling terminal never actually closes.

## Related topics

- [Module 04: Job Control](../04-bash/job-control.md)
- [Module 03: Terminal Multiplexers](../03-terminal/terminal-multiplexers.md)
