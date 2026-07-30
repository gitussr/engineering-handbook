---
title: "tmux — Terminal Multiplexer"
description: "Run a persistent, detachable terminal session that survives SSH disconnection, and split it into multiple panes — the standard way to protect long-running work from a dropped connection."
relatedConcepts: ["03-terminal/terminal-multiplexers"]
relatedCommands: []
careerRelevance: ["linux-administrator", "devops", "sre"]
difficulty: "must-know"
compatibility: [{"context": "Most Linux distros", "note": "installed via package manager if not already present; not installed by default on minimal server images"}]
updatedAt: "2026-07-30"
keywords: ["tmux command", "tmux cheat sheet", "tmux detach attach", "tmux vs screen"]
canonicalUrl: "/commands/tmux"
---

# tmux

🟢 Must Know · Relevant for: Linux Administrator · DevOps · SRE

> **TL;DR:** `tmux new -s {name}` starts a named session; detach with `Ctrl+B` then `D`; reattach
> from anywhere with `tmux attach -t {name}` — the session and everything running inside it
> survives a dropped SSH connection.

## Purpose

`tmux` is a terminal multiplexer: it runs a persistent session on the server itself, independent
of any one SSH connection, and lets you detach from it and reattach later — from the same
connection or a different one — with everything inside still running exactly as you left it. See
[Terminal Multiplexers: tmux, screen](../docs/03-terminal/terminal-multiplexers.md) for why this
matters for anything long-running over SSH.

## Syntax

```
tmux [COMMAND] [OPTIONS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `-s {name}` | Session name, used with `new`/`attach` | No — tmux assigns a numeric name if omitted, but a real name is much easier to reattach to later |
| `-t {name}` | Target session name, used with `attach`/`kill-session` | Required for `attach`/`kill-session` when more than one session exists |

## Options

| Command | Meaning |
|---|---|
| `new -s {name}` | Start a new named session |
| `attach -t {name}` | Reattach to an existing session |
| `ls` | List all running sessions |
| `kill-session -t {name}` | Terminate a specific session and everything running inside it |
| `Ctrl+B` then `D` | Detach from the current session (keyboard shortcut, not a shell command) |

## Examples

```
$ tmux new -s deploy
```
Start a new named session called "deploy" — do this before starting any long-running task over SSH.

```
$ tmux attach -t deploy
```
Reattach to that session after a dropped connection, or from a different terminal entirely — the
task keeps running exactly where you left it.

```
$ tmux ls
```
List all currently running sessions on the server, useful for confirming a session is still alive
before trying to reattach.

## Expected Output

```
$ tmux ls
deploy: 1 windows (created Wed Jul 30 09:00:00 2026)
build:  1 windows (created Wed Jul 30 08:15:00 2026)
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Success |
| Non-zero | The named session doesn't exist (for `attach`), or `tmux` isn't installed |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `no server running on ...` | No `tmux` session currently exists on this server | Start one with `tmux new -s {name}` first |
| `can't find session {name}` | Typo in the session name, or the session already ended | Run `tmux ls` to see actual running session names |
| Accidentally closing the terminal window instead of detaching | Confusing "close the window" with "detach" — they are not the same | Always detach deliberately with `Ctrl+B` then `D`, not by closing the terminal |

## Security Considerations

A detached `tmux` session keeps running and remains accessible to anyone who can reattach as the
same user — on a shared server, be aware that a forgotten, still-running session can expose
whatever was left on screen (including command history) to another user with the same account
access.

## Performance Considerations

Negligible overhead — `tmux` itself uses minimal CPU/memory; the resource cost is whatever's
actually running inside the session, identical to running it without `tmux` at all.

## Compatibility Notes

Not installed by default on many minimal server images — install via the distro's package manager
(`apt install tmux` / `dnf install tmux`) if it's missing. `screen` is the older, still-common
alternative with a similar (but not identical) keybinding scheme.

## Production Usage

Starting a named `tmux` session before any long-running operation over SSH — a deploy, a large
data migration, a build — is standard practice specifically because a dropped SSH connection
(network blip, laptop sleeping, VPN hiccup) would otherwise kill the task outright. Naming
sessions meaningfully (`deploy`, not the default numeric name) matters once more than one session
is running at a time.

## Related Commands

No closely related canonical command page — `screen` serves the same purpose with different
keybindings, covered alongside `tmux` on
[Terminal Multiplexers](../docs/03-terminal/terminal-multiplexers.md).

## Related Concepts

- [Terminal Multiplexers: tmux, screen](../docs/03-terminal/terminal-multiplexers.md)
