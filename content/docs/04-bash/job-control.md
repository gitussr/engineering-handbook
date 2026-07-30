---
title: "Job Control"
description: "Running commands in the background, bringing them back to the foreground, and keeping them alive after you disconnect — bg, fg, jobs, &, and nohup."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/command-substitution"]
relatedTopics: ["bash-history-tricks"]
relatedCommands: ["jobs"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#job-control"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/bash-history-tricks"
prevTopic: "04-bash/command-substitution"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["bash job control", "bg fg jobs command", "nohup", "background process linux"]
canonicalUrl: "/docs/bash/job-control"
---

# Job Control

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `&` starts a command in the background so your shell stays free; `jobs` lists your
> shell's background/stopped jobs; `bg`/`fg` move a job between background and foreground.
> `nohup` goes further — it protects a command from being killed when your shell session itself
> ends, which `&` alone doesn't do.

## What is it?

Job control is the shell's mechanism for running, tracking, and managing multiple commands within
a single terminal session — moving tasks between running in the foreground (blocking your
prompt) and the background (freeing your prompt while the task keeps running).

## Why does it exist?

Without job control, running a long command means your terminal is unusable until it finishes.
Job control lets you start something, free up your prompt to keep working, and check back on it
later — all within one session, without needing a separate terminal window or a multiplexer
(covered in [Module 03](../03-terminal/terminal-multiplexers.md)) for simpler cases.

## Where is it used?

- Starting a long-running command without blocking your terminal
- Checking on and managing multiple background tasks in one session
- Protecting a command from being killed if your terminal or SSH session unexpectedly closes
  (`nohup`) — related to, but distinct from, the persistence a multiplexer provides

## How it works

> 📊 Diagram: a simple state diagram — "Foreground" ⇄ "Background" via `fg`/`bg`, with a third
> state "Stopped" (via `Ctrl+Z`) that can move to either, and a separate note that `nohup`
> additionally detaches a command from the terminal's hangup signal entirely.

| Tool | Effect |
|---|---|
| `command &` | Start a command in the background immediately |
| `Ctrl+Z` | Suspend (stop) the current foreground command |
| `jobs` | List background/stopped jobs in the current shell session |
| `bg` | Resume a stopped job, running it in the background |
| `fg` | Bring a background/stopped job back to the foreground |
| `nohup command &` | Run a command immune to the "hangup" signal sent when your terminal/SSH session closes |

**Important distinction**: a plain `&` background job is still tied to your shell session — if the
shell exits (e.g. an SSH disconnect), the job is killed too, unless it was started with `nohup` or
run inside a multiplexer session (Module 03).

## Real-world example

An engineer starts a long-running data export with `command &`, keeping their terminal free to
run other commands while it processes. Later, they close their laptop lid, which drops the SSH
connection — and the export dies with it, because `&` alone doesn't survive a hangup. The
corrected version, `nohup command &`, would have survived exactly that scenario.

## Commands

- [`jobs`](../../commands/jobs.md) — list background/stopped jobs in the current shell

## Production example

```
$ long-task &
[1] 24831
$ jobs
[1]+  Running                 long-task &
$ fg %1
long-task
^Z
[1]+  Stopped                 long-task
$ bg %1
[1]+ long-task &
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `nohup` for anything that must survive a disconnect | Assume `&` alone protects a job from a dropped SSH session |
| Use `jobs` to check what's running before closing a session | Close a terminal without checking for active background jobs |
| Use a multiplexer (Module 03) for anything long and interactive | Rely on plain job control for tasks needing to persist across days |

## Common mistakes

- Assuming `&` alone protects a background command from a dropped connection — it doesn't;
  `nohup` or a multiplexer session is required for that.
- Closing a terminal without checking `jobs` first, unintentionally killing background work.
- Confusing job control (single-session background/foreground management) with a multiplexer
  (session-level persistence across disconnects) — they solve related but distinct problems.

## Best practices

- Use `nohup command &` (or better, a multiplexer session) for anything that must survive a
  disconnect.
- Check `jobs` before closing a terminal session with active background work.
- For anything long-running and important, prefer a multiplexer session (Module 03) over plain
  job control — it's more robust and lets you fully reattach, not just avoid killing the process.

## Exercises

1. Start a long-running command in the background with `&`, then check it with `jobs`.
2. Suspend a foreground command with `Ctrl+Z`, then resume it in the background with `bg`.
3. Explain in one sentence why `nohup command &` behaves differently from just `command &` when
   an SSH session drops.

## Quiz

**Q: Does `command &` protect a job from being killed if your SSH session disconnects?**
<details><summary>Show answer</summary>
No — a plain background job is still tied to the shell session. `nohup` (or running inside a
multiplexer) is needed to survive a disconnect.
</details>

**Q: What does `Ctrl+Z` do to a running foreground command?**
<details><summary>Show answer</summary>
Suspends (stops) it, moving it to a stopped state that can then be resumed in the background with
`bg` or brought back to the foreground with `fg`.
</details>

## Interview questions

- What's the difference between running a command with `&` and running it with `nohup`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `&` backgrounds a command but doesn't protect it from a dropped session — `nohup` does.
- `jobs`, `bg`, and `fg` manage tasks within a single shell session.
- A multiplexer (Module 03) is the more robust choice for anything long-running and important.
- Always check `jobs` before closing a terminal with active background work.

## Related topics

- [Module 03: Terminal Multiplexers](../03-terminal/terminal-multiplexers.md)
- [Module 10: Processes](../10-processes/index.md)
