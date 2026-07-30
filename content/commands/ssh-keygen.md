---
title: "ssh-keygen — Generate an SSH Key Pair"
description: "Create a new public/private key pair for SSH authentication."
relatedConcepts: ["13-ssh/ssh-key-generation-and-key-based-auth"]
relatedCommands: ["ssh-copy-id", "ssh"]
careerRelevance: ["devops", "cloud", "sre", "cybersecurity"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["ssh-keygen examples", "ssh-keygen ed25519", "generate ssh key pair", "ssh-keygen passphrase"]
canonicalUrl: "/commands/ssh-keygen"
---

# ssh-keygen

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Cybersecurity

> **TL;DR:** `ssh-keygen -t ed25519 -C "you@example.com"` generates a new key pair — a private
> key file and a matching `.pub` public key file — prompting for an optional passphrase.

## Purpose

`ssh-keygen` generates a new public/private key pair for SSH authentication — see
[SSH Key Generation and Key-Based Auth](../docs/13-ssh/ssh-key-generation-and-key-based-auth.md)
for the full concept.

## Syntax

```
ssh-keygen [OPTIONS]
```

## Arguments

None — entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `-t TYPE` | Key type (`ed25519` recommended; `rsa` for legacy compatibility) |
| `-C COMMENT` | A label (commonly an email) embedded in the public key for identification |
| `-f FILE` | Output file path/name instead of the default |
| `-b BITS` | Key size in bits (relevant for `rsa`; `ed25519` has a fixed, modern size) |

## Examples

```
$ ssh-keygen -t ed25519 -C "engineer@company.com"
```
Generate a new ed25519 key pair with an identifying comment.

```
$ ssh-keygen -t rsa -b 4096 -C "legacy-system-key"
```
Generate an RSA key pair for a system that requires it for compatibility.

```
$ ssh-keygen -f ~/.ssh/deploy_key -t ed25519
```
Generate a key pair with a specific filename, for a dedicated purpose (e.g. a deploy key).

## Expected Output

```
$ ssh-keygen -t ed25519 -C "engineer@company.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
```

## Exit Status

`0` on success, non-zero if the output path already exists and isn't overwritten, or on invalid
options.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Prompted to overwrite an existing key | A key already exists at the default/specified path | Choose a different filename with `-f` if you want to keep the existing key |
| Forgotten passphrase | No recovery mechanism exists for a private key's passphrase | Generate a new key pair and re-install the new public key everywhere the old one was used |

## Security Considerations

Always set a passphrase for an interactively-used key — an unencrypted private key file is
usable by anyone who obtains it, with nothing else required.

## Performance Considerations

Negligible — a one-time operation per key.

## Production Usage

Generating a dedicated key per purpose (personal login vs. an automation/deploy identity) rather
than reusing one key everywhere is standard practice — it lets any single key be revoked
independently without affecting others.

## Related Commands

- [`ssh-copy-id`](ssh-copy-id.md) — install the resulting public key on a server
- [`ssh`](ssh.md) — the tool that uses the generated key pair to authenticate

## Related Concepts

- [SSH Key Generation and Key-Based Auth](../docs/13-ssh/ssh-key-generation-and-key-based-auth.md)
