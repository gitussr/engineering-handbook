---
title: "SSH Key Generation and Key-Based Auth"
description: "Generating a public/private key pair and using it to authenticate instead of a password — how it works and why it's the recommended default."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["13-ssh/ssh-command-usage"]
relatedTopics: ["ssh-hardening-disable-root-login-key-only-auth"]
relatedCommands: ["ssh-keygen", "ssh-copy-id"]
careerRelevance: ["devops", "cloud", "sre", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ssh-key-based-auth"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "13-ssh/scp-sftp-rsync"
prevTopic: "13-ssh/ssh-command-usage"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["ssh key generation", "ssh-keygen explained", "ssh public private key", "ssh-copy-id explained"]
canonicalUrl: "/docs/ssh/ssh-key-generation-and-key-based-auth"
---

# SSH Key Generation and Key-Based Auth

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Cybersecurity

> **TL;DR:** A key pair has a private key (stays on your machine, never shared) and a public key
> (copied to servers you want to access). The server trusts anyone who can prove they hold the
> matching private key — no password ever crosses the network. `ssh-keygen` generates the pair;
> `ssh-copy-id` installs the public key on a server.

## What is it?

An authentication method using a cryptographic key pair instead of a password: a private key that
never leaves the client machine, and a public key that's safe to copy to any server you want to
access — the server uses it to verify you hold the matching private key, without the private key
itself ever being transmitted.

## Why does it exist?

Passwords can be guessed, brute-forced, phished, or reused across systems. A private key is
effectively impossible to guess (thousands of bits of entropy) and, unlike a password, is never
sent over the network even during a successful login — there's nothing for an attacker
intercepting the session to steal that would let them authenticate elsewhere.

## Where is it used?

The recommended default authentication method for essentially all production SSH access —
manual admin logins, automation tools (CI/CD pipelines, Ansible), and Git-over-SSH access to
repository hosts.

## How it works

> 📊 Diagram: a client holding a private key and a server holding the corresponding public key
> (already copied there in advance); during authentication, the server sends a challenge the
> client can only answer correctly by using the private key — the private key itself never
> travels over the network, only proof that the client possesses it.

1. **Generate a key pair** on the client: `ssh-keygen` produces a private key (e.g.
   `~/.ssh/id_ed25519`, kept secret, ideally passphrase-protected) and a matching public key
   (`~/.ssh/id_ed25519.pub`, safe to share).
2. **Install the public key** on any server you want to access: `ssh-copy-id` appends it to that
   server's `~/.ssh/authorized_keys` file for your account.
3. **Authenticate**: when connecting, the server issues a cryptographic challenge that only the
   holder of the matching private key can answer correctly — proving identity without the private
   key, or any password, ever crossing the network.

**Passphrase**: encrypting the private key file itself with a passphrase means a stolen key file
alone isn't enough to use it — this is a separate layer from the key-pair mechanism itself and is
strongly recommended for any key used interactively.

## Real-world example

A team migrates from password-based SSH access to key-based auth after a security review flags
password-guessing attempts in the logs (a topic revisited in
[Module 16: Logs](../16-logs/index.md)). Each engineer generates their own key pair, installs
their public key via `ssh-copy-id` on every server they need, and password authentication is then
disabled server-side entirely (this module's later hardening topic) — eliminating the entire
class of brute-force login attempts the logs had been showing.

## Commands

- [`ssh-keygen`](../../commands/ssh-keygen.md) — full syntax and examples
- [`ssh-copy-id`](../../commands/ssh-copy-id.md) — full syntax and examples

## Production example

```
$ ssh-keygen -t ed25519 -C "engineer@company.com"
Generating public/private ed25519 key pair.
Enter passphrase (empty for no passphrase):

$ ssh-copy-id deploy@web-01.internal
Number of key(s) added: 1

$ ssh deploy@web-01.internal
```

The final `ssh` connects using the key automatically — no password prompt, assuming
`ssh-copy-id` succeeded and the server allows key-based auth.

## Do / Don't

| Do | Don't |
|---|---|
| Protect a private key with a passphrase, especially for interactive use | Leave an unencrypted private key as the only thing standing between an attacker and access |
| Use `ssh-copy-id` to install a public key rather than manually editing `authorized_keys` | Hand-copy-paste keys into `authorized_keys`, risking a formatting mistake |
| Generate a separate key pair per person (or per automation identity) | Share one private key across multiple people or systems |

## Common mistakes

- Copying or emailing a *private* key instead of the public key — the private key must never
  leave the machine it was generated on.
- Leaving a private key without a passphrase on a machine that could be lost, stolen, or
  compromised, removing the one extra layer of protection a passphrase provides.
- Manually hand-editing `~/.ssh/authorized_keys` and introducing a formatting error (a key must be
  exactly one line) instead of using `ssh-copy-id`.

## Best practices

- Use `ssh-keygen -t ed25519` (a modern, fast, secure key type) for new keys unless a specific
  system requires RSA for compatibility.
- Always set a passphrase on an interactively-used private key.
- Use a separate key pair per person or per automated identity, so any single key can be revoked
  without affecting anyone else's access.

## Exercises

1. Generate a new SSH key pair with `ssh-keygen` and inspect both the private and public key
   files it creates.
2. Explain in one sentence why the private key never needs to be sent to the server.
3. Describe what a passphrase on a private key protects against that the key pair mechanism alone
   doesn't.

## Quiz

**Q: Does the private key ever get transmitted over the network during authentication?**
<details><summary>Show answer</summary>
No — the server issues a cryptographic challenge that only the private key can answer correctly;
the private key itself never leaves the client.
</details>

**Q: What does `ssh-copy-id` actually do?**
<details><summary>Show answer</summary>
It copies your public key to a remote server's `~/.ssh/authorized_keys` file for your account,
enabling key-based login for that account going forward.
</details>

## Interview questions

- Why is key-based SSH authentication considered more secure than password authentication? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A key pair has a private key (never shared) and a public key (safe to copy to servers).
- The server verifies identity via a cryptographic challenge — the private key never crosses the
  network.
- `ssh-keygen` generates the pair; `ssh-copy-id` installs the public key on a server.
- A passphrase adds a critical extra layer of protection if the private key file itself is ever
  stolen.

## Related topics

- [SSH Hardening: Disable Root Login, Key-Only Auth](ssh-hardening-disable-root-login-key-only-auth.md)
- [ssh Command Usage](ssh-command-usage.md)
