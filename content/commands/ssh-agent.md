---
title: "ssh-agent — Hold Decrypted Keys in Memory for a Session"
description: "A background process that unlocks a passphrase-protected private key once and holds it in memory, so it isn't re-entered on every connection."
relatedConcepts: ["13-ssh/ssh-agent-and-agent-forwarding"]
relatedCommands: ["ssh-add", "ssh"]
careerRelevance: ["devops", "sre", "cybersecurity"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["ssh-agent explained", "eval ssh-agent", "ssh-agent -s", "ssh agent session"]
canonicalUrl: "/commands/ssh-agent"
---

# ssh-agent

🔴 Expert · Relevant for: DevOps · SRE · Cybersecurity

> **TL;DR:** `eval "$(ssh-agent -s)"` starts an agent for the current shell session. Then
> [`ssh-add`](ssh-add.md) loads a key into it, decrypted once, reused for every subsequent
> connection in that session.

## Purpose

`ssh-agent` runs in the background holding decrypted private keys in memory — see
[SSH Agent and Agent Forwarding](../docs/13-ssh/ssh-agent-and-agent-forwarding.md) for the full
concept, including agent forwarding's risk.

## Syntax

```
ssh-agent [OPTIONS] [COMMAND]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | Run a specific command with the agent available, then exit | No |

## Options

| Flag | Meaning |
|---|---|
| `-s` | Output Bourne-shell-compatible commands to set environment variables (for `eval`) |
| `-k` | Kill the currently running agent |

## Examples

```
$ eval "$(ssh-agent -s)"
```
Start an agent and export its environment variables into the current shell session.

```
$ ssh-agent bash
```
Start an agent that lasts only for the lifetime of a new `bash` subshell.

```
$ eval "$(ssh-agent -k)"
```
Kill the currently running agent.

## Expected Output

```
$ eval "$(ssh-agent -s)"
Agent pid 12345
```

## Exit Status

`0` on success starting (or killing) the agent, non-zero on failure.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Could not open a connection to your authentication agent` | No agent running, or environment variables not set in this shell | Run `eval "$(ssh-agent -s)"` in the current shell |
| Agent running but key not usable | Key was never loaded into this agent | Run [`ssh-add`](ssh-add.md) |
| A new terminal doesn't see the agent from another terminal | Agent environment variables are per-shell-session, not system-wide by default | Start (or re-attach to) an agent per session, or use a session-persistence tool |

## Security Considerations

An agent holds decrypted key material in memory for as long as it runs — treat a running agent
(and especially a forwarded one, see
[Agent Forwarding](../docs/13-ssh/ssh-agent-and-agent-forwarding.md)) as sensitive, live access,
not an inert file.

## Performance Considerations

Negligible — the benefit is avoiding repeated passphrase prompts, not raw performance.

## Production Usage

Most desktop Linux environments and modern terminal setups start an `ssh-agent` automatically per
login session — manually running `eval "$(ssh-agent -s)"` is mainly needed in a fresh shell,
script, or minimal environment where one isn't already running.

## Related Commands

- [`ssh-add`](ssh-add.md) — load a key into a running agent
- [`ssh`](ssh.md) — uses the agent automatically if one is running and has a matching key loaded

## Related Concepts

- [SSH Agent and Agent Forwarding](../docs/13-ssh/ssh-agent-and-agent-forwarding.md)
