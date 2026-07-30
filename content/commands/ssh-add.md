---
title: "ssh-add — Load a Private Key Into a Running Agent"
description: "Decrypt a private key (prompting for its passphrase once) and load it into the currently running ssh-agent."
relatedConcepts: ["13-ssh/ssh-agent-and-agent-forwarding"]
relatedCommands: ["ssh-agent", "ssh-keygen"]
careerRelevance: ["devops", "sre", "cybersecurity"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["ssh-add examples", "ssh-add -l", "ssh-add list loaded keys", "ssh-add specific key"]
canonicalUrl: "/commands/ssh-add"
---

# ssh-add

🔴 Expert · Relevant for: DevOps · SRE · Cybersecurity

> **TL;DR:** `ssh-add ~/.ssh/id_ed25519` prompts for the key's passphrase once and loads the
> decrypted key into the running agent. `ssh-add -l` lists which keys are currently loaded.

## Purpose

`ssh-add` loads a private key into a running [`ssh-agent`](ssh-agent.md) — see
[SSH Agent and Agent Forwarding](../docs/13-ssh/ssh-agent-and-agent-forwarding.md) for the full
concept.

## Syntax

```
ssh-add [OPTIONS] [KEYFILE]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `KEYFILE` | Path to the private key to load | No — defaults to standard key file names (`~/.ssh/id_ed25519`, etc.) if omitted |

## Options

| Flag | Meaning |
|---|---|
| `-l` | List fingerprints of currently loaded keys |
| `-D` | Remove all keys from the running agent |
| `-t SECONDS` | Automatically remove the key from the agent after a time limit |

## Examples

```
$ ssh-add
```
Load the default key file(s), prompting for a passphrase if set.

```
$ ssh-add ~/.ssh/deploy_key
```
Load a specific, non-default key file.

```
$ ssh-add -l
```
List fingerprints of keys currently loaded in the agent.

```
$ ssh-add -t 3600 ~/.ssh/id_ed25519
```
Load a key that automatically expires from the agent after one hour.

## Expected Output

```
$ ssh-add ~/.ssh/id_ed25519
Enter passphrase for /home/user/.ssh/id_ed25519:
Identity added: /home/user/.ssh/id_ed25519 (engineer@company.com)

$ ssh-add -l
256 SHA256:AbCdEf... engineer@company.com (ED25519)
```

## Exit Status

`0` on success, non-zero if no agent is running, the passphrase is wrong, or the key file doesn't
exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Could not open a connection to your authentication agent` | No [`ssh-agent`](ssh-agent.md) running in this session | Start one with `eval "$(ssh-agent -s)"` first |
| `The agent has no identities` (from `ssh-add -l`) | No keys loaded yet | Run `ssh-add` with the key file |
| Wrong passphrase rejected repeatedly | Typo, or the wrong passphrase for that specific key | Re-verify which key and passphrase pair you're using |

## Security Considerations

Use `-t` to auto-expire a loaded key after a time limit when working on a shared or less-trusted
machine, reducing the window a compromised session could misuse the loaded key.

## Performance Considerations

Negligible — a one-time decrypt-and-load operation per key per agent session.

## Production Usage

`ssh-add -l` is the standard quick check to confirm which identity will actually be used before
an important connection — especially useful when multiple keys exist for different environments.

## Related Commands

- [`ssh-agent`](ssh-agent.md) — the background process keys are loaded into
- [`ssh-keygen`](ssh-keygen.md) — generates the key files `ssh-add` loads

## Related Concepts

- [SSH Agent and Agent Forwarding](../docs/13-ssh/ssh-agent-and-agent-forwarding.md)
