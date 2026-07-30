---
title: "What is SSH and How It Works"
description: "SSH encrypts and authenticates a remote login session end to end — the replacement for older, unencrypted remote access protocols like telnet."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/ports-and-sockets-explained"]
relatedTopics: ["ssh-command-usage"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ssh-explained"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "13-ssh/ssh-command-usage"
prevTopic: "12-networking/ports-and-sockets-explained"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["what is ssh", "how ssh works", "ssh encryption explained", "ssh vs telnet"]
canonicalUrl: "/docs/ssh/what-is-ssh-and-how-it-works"
---

# What is SSH and How It Works

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** SSH (Secure Shell) is an encrypted, authenticated protocol for remote login and
> command execution over a network — port 22 by default. It replaced older protocols like telnet
> that sent everything, including passwords, in plain text.

## What is it?

SSH is a network protocol that provides an encrypted, authenticated channel between a client and
a server, most commonly used to log into a remote machine's shell and run commands as if sitting
in front of it directly.

## Why does it exist?

Older remote access protocols (telnet, rlogin) sent everything — including login credentials —
over the network in plain text, readable by anyone able to intercept the traffic. SSH exists to
close that gap entirely: every byte of an SSH session, including the password or key used to
authenticate, is encrypted before it ever leaves the client.

## Where is it used?

The default way to administer almost every remote Linux server: logging in to run commands,
copying files (this module's `scp`/`sftp`/`rsync` topic), and as the transport underneath many
higher-level tools (Git over SSH, Ansible's default connection method, VS Code Remote-SSH).

## How it works

> 📊 Diagram: a client and server exchanging an encrypted SSH handshake — key exchange to
> establish a shared session key, server authentication (the server proves its identity via its
> host key), then client authentication (password or key-based) — contrasted with a plain-text
> telnet session where a password is shown traveling over the wire unencrypted and readable.

SSH connections happen in two authentication stages, and it's important to understand these are
about two different identities being verified:

1. **Server authentication**: the client verifies the server is who it claims to be, using the
   server's host key. This is what triggers the "authenticity of host can't be established"
   prompt the very first time you connect to a new server.
2. **Client authentication**: the server verifies the client is allowed to log in — either by
   password, or by key-based authentication (this module's next-but-one topic), which is
   significantly more secure.

By default, SSH listens on TCP port 22 ([Ports and Sockets](../12-networking/ports-and-sockets-explained.md),
previous module) — though this is configurable and commonly changed as a hardening measure (this
module's later topic).

## Real-world example

An engineer connects to a brand-new server for the first time and sees a prompt: "The
authenticity of host 'server (203.0.113.5)' can't be established... Are you sure you want to
continue connecting?" This is SSH's server-authentication step surfacing to a human because it
has no prior record of this server's host key — accepting it (and verifying the fingerprint out
of band, ideally) records that key for all future connections, so any later mismatch would
indicate the server's identity has genuinely changed, a potential red flag.

## Commands

No command example on this page — this page is conceptual context. See
[`ssh` Command Usage](ssh-command-usage.md) for the first hands-on commands in this module.

## Production example

Not applicable — see [`ssh` Command Usage](ssh-command-usage.md) for the first terminal session
in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Verify a new host's key fingerprint out of band when possible, especially for sensitive systems | Blindly accept every "authenticity of host" prompt without a second thought |
| Understand SSH authenticates both server and client, in that order | Assume SSH only checks who *you* are, not who the server is |
| Treat any host key change on a known server as worth investigating | Dismiss a host key mismatch warning as routine |

## Common mistakes

- Treating the "authenticity of host can't be established" prompt as pure friction to click
  through, rather than SSH's server-authentication step doing its job.
- Assuming SSH only authenticates the client — server authentication happens first and is just as
  important, especially against a compromised or spoofed server.
- Not understanding that a host key *mismatch* on a previously-known server is a legitimate
  security warning, not a routine glitch.

## Best practices

- Verify a new server's host key fingerprint through a trusted side channel when the stakes
  justify it (e.g. production infrastructure, not a disposable test VM).
- Take a host key mismatch warning seriously — investigate before dismissing it, since it can
  indicate a genuinely different (possibly malicious) server.
- Prefer key-based authentication (this module's next-but-one topic) over passwords wherever
  possible.

## Exercises

1. Connect via SSH to a new host for the first time and read the host key prompt carefully before
   accepting it.
2. Explain in one sentence the difference between server authentication and client authentication
   in an SSH session.
3. Explain why a host key mismatch warning on a previously-known server should not be dismissed
   automatically.

## Quiz

**Q: What does the "authenticity of host can't be established" prompt actually verify?**
<details><summary>Show answer</summary>
It's SSH's server-authentication step — the client has no prior record of this server's host key
and is asking the user to confirm before trusting it.
</details>

**Q: What problem with older protocols like telnet did SSH specifically solve?**
<details><summary>Show answer</summary>
telnet sent everything, including login credentials, in plain text over the network. SSH
encrypts the entire session.
</details>

## Interview questions

- What's the difference between server authentication and client authentication in SSH? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- SSH provides an encrypted, authenticated channel for remote login, replacing plain-text
  protocols like telnet.
- SSH connections authenticate the server first, then the client.
- The "authenticity of host" prompt is server authentication happening for the first time against
  a new host.
- SSH defaults to TCP port 22, though this is commonly changed for hardening.

## Related topics

- [`ssh` Command Usage](ssh-command-usage.md)
- [Module 12: Ports and Sockets Explained](../12-networking/ports-and-sockets-explained.md)
