---
title: "SSH Hardening: Disable Root Login, Key-Only Auth"
description: "The handful of sshd_config changes that eliminate the most common SSH attack surface, and the order to apply them in so you don't lock yourself out."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["13-ssh/ssh-tunneling-and-port-forwarding"]
relatedTopics: ["ssh-key-generation-and-key-based-auth"]
relatedCommands: []
careerRelevance: ["devops", "cybersecurity", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ssh-hardening-checklist"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "13-ssh/ssh-agent-and-agent-forwarding"
prevTopic: "13-ssh/ssh-tunneling-and-port-forwarding"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["sshd_config hardening", "disable root login ssh", "permitrootlogin no", "passwordauthentication no"]
canonicalUrl: "/docs/ssh/ssh-hardening-disable-root-login-key-only-auth"
---

# SSH Hardening: Disable Root Login, Key-Only Auth

🟡 Good to Know · Relevant for: DevOps · Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `PermitRootLogin no` and `PasswordAuthentication no` in `/etc/ssh/sshd_config`
> eliminate the two most commonly attacked SSH weaknesses — direct root login attempts and
> password brute-forcing. Verify key-based access works *before* disabling passwords, or you can
> lock yourself out entirely.

## What is it?

A small set of `/etc/ssh/sshd_config` directives that remove the most commonly exploited SSH
attack surface: allowing direct root login, and allowing password authentication at all once
key-based auth ([this module's earlier topic](ssh-key-generation-and-key-based-auth.md)) is set
up.

## Why does it exist?

Automated attacks against internet-facing SSH servers overwhelmingly target two things: guessing
the root password directly, and brute-forcing passwords for any account. Disabling both — direct
root login and password authentication entirely — removes essentially the entire class of
automated credential-guessing attacks in one move, since there's no password to guess and no
direct root target to guess it against.

## Where is it used?

Any internet-facing (or otherwise not-fully-trusted-network) SSH server, which in practice means
nearly every production Linux server — this is considered baseline hardening, not an advanced or
optional measure.

## How it works

> 📊 Diagram: `/etc/ssh/sshd_config` shown with two directives highlighted —
> `PermitRootLogin no` and `PasswordAuthentication no` — with an arrow showing the correct order
> of operations: verify key-based login works for a non-root sudo-capable user *first*, only then
> apply these changes and restart `sshd`.

| Directive | Effect |
|---|---|
| `PermitRootLogin no` | Disallows logging in directly as `root` over SSH at all — an admin must log in as themselves and `sudo` |
| `PasswordAuthentication no` | Disallows password-based login entirely — only key-based auth is accepted |

**The critical ordering**: these changes must be applied only *after* confirming key-based login
already works for a non-root account with `sudo` access. Applying `PasswordAuthentication no`
before key-based auth is confirmed working can lock every user out of the server simultaneously,
with no password fallback left.

## Real-world example

A team hardens a new fleet of servers by setting both directives and restarting `sshd`
immediately, without first testing that their own key-based login actually worked. One engineer's
public key was never successfully installed due to an earlier typo — after the restart, that
engineer is locked out entirely, with no password fallback and no working key. The fix (console
access via the cloud provider, or another already-logged-in admin's session) is avoidable
entirely by testing key-based login first, every time, before disabling passwords.

## Commands

No new command — this topic is about `/etc/ssh/sshd_config` directives and restarting the
`sshd` service via [`systemctl`](../../commands/systemctl.md), already covered in
[Module 11](../11-services/systemctl-start-stop-enable-disable-status.md).

## Production example

```
$ sudo vim /etc/ssh/sshd_config
# PermitRootLogin no
# PasswordAuthentication no

$ sudo sshd -t
$ sudo systemctl restart sshd
```

`sshd -t` tests the configuration file's syntax *before* restarting — catching a typo before it
takes down SSH access entirely.

## Do / Don't

| Do | Don't |
|---|---|
| Verify key-based login works for a sudo-capable non-root user first | Disable password auth before confirming key-based login actually works |
| Run `sshd -t` to validate config syntax before restarting | Restart `sshd` with an untested config change |
| Keep a console/out-of-band access method available during the change | Make this change with SSH as your only possible access path |

## Common mistakes

- Disabling password authentication before confirming key-based login genuinely works, risking a
  full lockout — the exact scenario in the real-world example above.
- Skipping `sshd -t` before restarting, and only discovering a config typo after `sshd` fails to
  restart.
- Making this change with no out-of-band access method (cloud console, physical access) available
  as a fallback.

## Best practices

- Always verify key-based login works, with `sudo` access confirmed, before disabling password
  authentication.
- Run `sshd -t` before every `sshd` restart to catch configuration errors safely.
- Keep an out-of-band access method (cloud provider console, etc.) available whenever changing
  SSH access configuration, as a safety net.

## Exercises

1. Locate `/etc/ssh/sshd_config` on a system you have access to and identify the current values
   of `PermitRootLogin` and `PasswordAuthentication`.
2. Explain in one sentence why these two changes should be applied only after key-based login is
   confirmed working.
3. Explain what `sshd -t` protects against.

## Quiz

**Q: What must be verified before setting `PasswordAuthentication no`?**
<details><summary>Show answer</summary>
That key-based login already works for a non-root, sudo-capable account — otherwise disabling
passwords can lock every user out with no fallback.
</details>

**Q: What does `sshd -t` do, and why run it before restarting `sshd`?**
<details><summary>Show answer</summary>
It validates the `sshd_config` file's syntax without applying it, catching a configuration error
before a restart could take down SSH access entirely.
</details>

## Interview questions

- What's the correct order of operations when hardening SSH to disable password authentication? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `PermitRootLogin no` and `PasswordAuthentication no` eliminate the most common SSH attack
  surface.
- Key-based login must be verified working before disabling password authentication, to avoid a
  full lockout.
- `sshd -t` validates configuration syntax before a restart.
- Keep an out-of-band access method available whenever changing SSH access configuration.

## Related topics

- [SSH Key Generation and Key-Based Auth](ssh-key-generation-and-key-based-auth.md)
- [SSH Agent and Agent Forwarding](ssh-agent-and-agent-forwarding.md)
