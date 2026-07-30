---
title: "Terminal Multiplexers: tmux, screen"
description: "Why an SSH session that survives disconnection matters, and how tmux and screen give you persistent, splittable terminal sessions."
module: "03-terminal"
moduleTitle: "Terminal"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["03-terminal/keyboard-shortcuts-and-history"]
relatedTopics: ["customizing-shell-prompt"]
relatedCommands: ["tmux"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#tmux-vs-screen"]
relatedCheatsheet: ""
furtherReading: [{"label": "tmux GitHub wiki", "url": "https://github.com/tmux/tmux/wiki"}]
nextTopic: "03-terminal/customizing-shell-prompt"
prevTopic: "03-terminal/keyboard-shortcuts-and-history"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["tmux", "gnu screen", "persistent ssh session", "terminal multiplexer"]
canonicalUrl: "/docs/terminal/terminal-multiplexers"
---

# Terminal Multiplexers: tmux, screen

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** A terminal multiplexer (tmux, or the older screen) keeps your session — and
> anything running in it — alive on the server even if your SSH connection drops. It also lets
> you split one terminal window into multiple panes.

## What is it?

A terminal multiplexer runs on the remote server itself and manages one or more terminal sessions
inside it. You connect to it over SSH; if the SSH connection drops, the multiplexer session — and
anything running inside it — keeps running on the server, ready to reattach to.

## Why does it exist?

A plain SSH session dies the moment your connection drops (a flaky network, closing your laptop
lid, a VPN hiccup) — and anything running in that session dies with it. A long-running task
(a large file transfer, a multi-hour build, a database migration) started in a plain SSH session
is destroyed by a dropped connection. A multiplexer decouples "your terminal session" from "your
network connection" entirely.

## Where is it used?

- Running long tasks on a remote server that must survive a disconnect
- Splitting one SSH session into multiple panes (logs in one pane, a shell in another) without
  opening multiple SSH connections
- On-call/incident response, where a stable, reattachable session matters under pressure

## How it works

> 📊 Diagram: two states of the same tmux session — "Attached" (a terminal window connected to
> it, live) and "Detached" (the session still running on the server, nothing connected) — with an
> arrow showing you can detach and reattach without anything inside the session stopping.

You start a multiplexer session on the server (`tmux new -s work`), run whatever you need inside
it, and can **detach** from it (the session keeps running) or simply lose your connection — either
way, reconnecting with SSH and running `tmux attach -t work` puts you right back where you left
off, with everything still running.

## Real-world example

An engineer starts a multi-hour database migration script over SSH without a multiplexer. Their
laptop goes to sleep an hour in, the SSH connection drops, and the migration is killed halfway
through — a real, costly mistake. The same task started inside a `tmux` session survives the
laptop sleeping, the network dropping, or even switching to a different machine to reconnect
later.

## Commands

- [`tmux`](../../commands/tmux.md) — the modern, most commonly used multiplexer (full command
  reference forward-referenced; see the module's known gaps note)

## Production example

```
$ tmux new -s deploy
# ... run a long-running task ...
# connection drops, or you detach with Ctrl+B then D
$ ssh user@server
$ tmux attach -t deploy
# back exactly where you left off, task still running
```

## Do / Don't

| Do | Don't |
|---|---|
| Start a multiplexer session before any long-running remote task | Run a multi-hour task in a plain SSH session with no multiplexer |
| Name your sessions meaningfully (`tmux new -s deploy`) | Leave unnamed, unidentifiable sessions running indefinitely |

## Common mistakes

- Running long tasks directly in a plain SSH session and losing them entirely to a dropped
  connection — the exact problem multiplexers solve.
- Forgetting a session name and later being unable to tell which of several running sessions is
  which.
- Confusing "detaching" (safe, session keeps running) with closing the terminal window without
  detaching first (can still kill the session depending on configuration) — always detach
  deliberately when possible.

## Best practices

- Default to starting a named tmux session for anything that will run longer than a couple of
  minutes on a remote server.
- Learn the detach shortcut (`Ctrl+B` then `D` in tmux) as a deliberate action, not something you
  rely on an accidental disconnect to trigger.
- Use split panes to watch logs and run commands side by side instead of juggling multiple SSH
  windows.

## Exercises

1. Start a named tmux session on a remote server, run a simple long-lived command
   (e.g. `sleep 300`), detach, disconnect, reconnect, and reattach.
2. Explain in one sentence why a dropped SSH connection doesn't kill a task running inside tmux.
3. List a task from your own work (or a hypothetical one) that should always run inside a
   multiplexer.

## Quiz

**Q: What happens to a task running inside tmux if your SSH connection drops?**
<details><summary>Show answer</summary>
It keeps running — the tmux session lives on the server independently of your network connection,
and you can reattach to it once reconnected.
</details>

**Q: Is tmux or screen "better"?**
<details><summary>Show answer</summary>
Both solve the same core problem; tmux is more actively developed and more commonly used in
modern environments, but screen is still found on many older or minimal systems.
</details>

## Interview questions

- Why would you use tmux before starting a long-running task over SSH? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Terminal multiplexers decouple your session from your network connection — a dropped SSH
  connection no longer kills what's running.
- tmux is the modern default; screen is the older equivalent still found on many systems.
- Detach deliberately (don't rely on an accidental disconnect) and name sessions meaningfully.
- Any long-running remote task belongs inside a multiplexer session, as a default habit.

## Further Reading

- [tmux GitHub wiki](https://github.com/tmux/tmux/wiki)

## Related topics

- [Module 13: SSH](../13-ssh/index.md)
