---
title: "kill — Send a Signal to a Process"
description: "Send SIGTERM by default, or a specific signal by number/name, to a process ID."
relatedConcepts: ["10-processes/kill-killall-pkill-and-signals"]
relatedCommands: ["killall", "pkill", "ps"]
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["kill command linux", "kill -9", "kill -15", "sigterm sigkill kill command"]
canonicalUrl: "/commands/kill"
---

# kill

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `kill PID` sends SIGTERM (ask nicely). `kill -9 PID` sends SIGKILL (immediate, no
> cleanup). Try the default first; escalate only if needed.

## Purpose

`kill` sends a signal to a process by PID — see
[kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md) for
full signal coverage.

## Syntax

```
kill [-SIGNAL] PID...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PID` | One or more process IDs to signal | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-SIGNAL` or `-N` | Signal name (e.g. `-TERM`, `-KILL`) or number (e.g. `-15`, `-9`) — defaults to `-TERM`/`-15` if omitted |
| `-l` | List all available signal names |

## Examples

```
$ kill 1234
```
Send SIGTERM (default) to PID 1234.

```
$ kill -9 1234
```
Send SIGKILL — immediate termination, no cleanup.

```
$ kill -HUP 1234
```
Send SIGHUP — often used for a graceful config reload.

```
$ kill -l
```
List all available signal names.

## Expected Output

`kill` produces no output on success.

## Exit Status

`0` on success, `1` if the PID doesn't exist or the signal couldn't be delivered (e.g. insufficient
privileges to signal another user's process).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `kill: (PID) - No such process` | The PID doesn't exist (may have already exited) | Verify with `ps -p PID` first |
| `kill: (PID) - Operation not permitted` | You don't own the process and aren't root | Use `sudo` if appropriate, or confirm you have the right PID |
| Process doesn't stop after `kill` | The process may be catching/ignoring SIGTERM, or stuck in uninterruptible sleep (`D` state) | Escalate to `kill -9` if appropriate, or investigate the I/O wait |

## Security Considerations

You can only signal processes you own, unless running as root — this is a deliberate permission
boundary preventing arbitrary users from disrupting each other's processes.

## Performance Considerations

Not applicable.

## Production Usage

Deployment and process-supervision tooling (including systemd, Module 11) relies on SIGTERM as
the standard "please shut down" signal, with a configurable timeout before escalating to SIGKILL
automatically — the same SIGTERM-then-SIGKILL pattern taught here, just automated.

## Related Commands

- [`killall`](killall.md) — signal by exact process name
- [`pkill`](pkill.md) — signal by name pattern
- [`ps`](ps.md) — find the PID to signal

## Related Concepts

- [kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md)
