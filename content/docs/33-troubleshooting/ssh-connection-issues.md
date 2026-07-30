---
title: "SSH Connection Issues"
description: "How to tell apart the different ways an SSH connection can fail — timeout, connection refused, and authentication failure each point to a completely different cause."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["13-ssh/ssh-command-usage"]
relatedTopics: ["33-troubleshooting/network-connectivity-issues", "33-troubleshooting/permission-denied-issues"]
relatedCommands: ["ssh"]
careerRelevance: ["linux-administrator", "devops", "cybersecurity"]
relatedLabs: ["beginner/set-up-ssh-key-access-to-a-new-server"]
relatedInterviewQuestions: ["scenario-based#locked-out-of-ssh-access"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "33-troubleshooting/boot-issues-and-rescue-mode"
prevTopic: "33-troubleshooting/network-connectivity-issues"
estimatedReadingTime: 7
updatedAt: "2026-07-30"
keywords: ["ssh connection refused", "ssh timeout troubleshooting", "ssh permission denied publickey", "cant ssh into server"]
canonicalUrl: "/docs/troubleshooting/ssh-connection-issues"
---

# SSH Connection Issues

🟢 Must Know · Relevant for: Linux Administrator · DevOps · Cybersecurity

> **TL;DR:** "Can't SSH in" has at least three genuinely different failure modes — timeout,
> connection refused, and authentication failure — and each one points to a completely different
> part of the stack. Read which one you're actually seeing before troubleshooting.

## What is it?

A breakdown of the distinct ways an SSH connection attempt can fail, and what each one actually
means: a timeout (no response at all), a connection refused (something responded, but not SSH), and
an authentication failure (SSH is there and responding, but your credentials aren't accepted).

## Why does it exist?

All three failure modes get reported by users the same way ("I can't SSH in"), but they point to
completely different problem areas — a timeout points to network/firewall, a connection refused
points to the SSH daemon itself, and an auth failure points to keys/permissions. Troubleshooting
the wrong one wastes real time, especially under the pressure of being locked out of a server you
need.

## Where is it used?

Any "can't connect" report involving SSH — from a new server that was never fully configured to a
previously-working connection that suddenly stops working after a config or key change.

## How it works

> 📊 Diagram: three parallel branches from "ssh command fails" — "Hangs, then times out" →
> network/firewall layer, check [Network Connectivity Issues](network-connectivity-issues.md);
> "Connection refused immediately" → SSH daemon not running or not listening on that port, check
> the server directly (console access); "Permission denied (publickey)" → key/`authorized_keys`/
> permission problem, check client key, server-side `authorized_keys`, and file permissions.

1. **Read exactly which failure you're seeing** — the three modes look and sound different and
   point to different causes:
   - **Times out / hangs** with no response: almost always a network/firewall problem — see
     [Network Connectivity Issues](network-connectivity-issues.md).
   - **"Connection refused" immediately**: something is reachable, but nothing is listening for
     SSH on that port — the `sshd` service may be down, or misconfigured to a different port.
   - **"Permission denied (publickey)"**: the network path and SSH daemon are both fine — the
     specific credential offered wasn't accepted.
2. **For a timeout**, work through the network layers as usual (firewall rules, security groups
   on a cloud instance, routing).
3. **For connection refused**, get onto the machine some other way (console/serial access) and
   check `systemctl status sshd` and whether it's listening on the expected port
   (`ss -tlnp | grep sshd`).
4. **For a publickey rejection**, verify: the correct private key is being offered
   (`ssh -v` shows which keys were tried), the matching public key is actually present in the
   server's `~/.ssh/authorized_keys`, and the permissions on `~/.ssh` (700) and `authorized_keys`
   (600) are correct — SSH silently refuses keys if these are too loose.
5. **Never lock yourself out while testing a fix** — test any SSH config change in a second
   session while keeping the original open, per the same discipline covered in
   [SSH Hardening](../13-ssh/ssh-hardening-disable-root-login-key-only-auth.md).

## Real-world example

An engineer reports "SSH is broken" after a server was hardened. The actual symptom is
`Permission denied (publickey)`, not a timeout — meaning the network path and SSH daemon are both
fine, and the cause is almost certainly that password auth was disabled as part of the hardening
and the engineer's key was never added to the new configuration. Troubleshooting firewall rules
(as the vague report might suggest) would have found nothing, because the actual failure was
several layers further along than that.

## Commands

See [`ssh`](../../commands/ssh.md) `-v` for verbose connection diagnostics showing exactly which
authentication methods and keys were attempted.

## Production example

```
$ ssh -v user@server-ip
...
debug1: Offering public key: /home/user/.ssh/id_ed25519
debug1: Authentications that can continue: publickey
debug1: No more authentication methods to try.
user@server-ip: Permission denied (publickey).
```

The verbose output confirms the key was offered and the server rejected it — the next check is
whether that exact public key is present in the server's `authorized_keys`, and whether the
permissions on `~/.ssh` and `authorized_keys` are correct.

## Do / Don't

| Do | Don't |
|---|---|
| Identify which of the three failure modes you're seeing before troubleshooting | Troubleshoot firewall rules for what's actually an auth rejection |
| Use `ssh -v` to see exactly which key was offered and why it was rejected | Guess at which key is being used |
| Test SSH config changes in a second session before closing the original | Edit `sshd_config` and restart without a fallback session open |
| Check `~/.ssh` (700) and `authorized_keys` (600) permissions | Assume a correctly-placed public key will work regardless of file permissions |

## Common mistakes

- Treating every "can't SSH in" report as the same problem instead of identifying timeout vs.
  refused vs. auth-failure first.
- Not using `ssh -v` to see exactly what happened during the attempt.
- Forgetting that SSH silently ignores keys if `~/.ssh` or `authorized_keys` permissions are too
  loose — a correctly-placed key can still fail for this reason alone.
- Testing a hardening change (disabling password auth, disabling root login) by closing the
  original session first, with no fallback if the change was wrong.

## Best practices

- Ask which specific failure mode is occurring before troubleshooting — it changes where you look
  entirely.
- Use `ssh -v` (or `-vvv` for more detail) as a first diagnostic step for any auth-related failure.
- Always keep a working session open while testing SSH config changes in a second session.
- Keep console/serial access as a documented fallback for "connection refused" scenarios where SSH
  itself isn't the way in.

## Exercises

1. Deliberately misconfigure a key (wrong permissions on `authorized_keys`) and practice
   diagnosing it with `ssh -v`.
2. Explain in one sentence the difference between "connection timed out" and "connection refused"
   in terms of what each tells you about where the problem is.
3. Write the two permission values (`~/.ssh` and `authorized_keys`) that SSH requires to accept a key.

## Quiz

**Q: An SSH attempt returns "Connection refused" immediately. What does that tell you, as opposed to a timeout?**
<details><summary>Show answer</summary>
Something is reachable and actively responding that there's no SSH service listening — as opposed
to a timeout, which means nothing responded at all, usually pointing to network/firewall instead.
</details>

**Q: Why might a correctly-placed public key still fail authentication?**
<details><summary>Show answer</summary>
SSH silently refuses to use a key if `~/.ssh` or `authorized_keys` on the server have permissions
looser than expected (typically 700 and 600 respectively) — the key being "there" isn't sufficient.
</details>

## Interview questions

- You've been locked out of SSH access to a production server — key auth is failing and there's
  no other active session. What are your options? →
  [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#locked-out-of-ssh-access)

## Key Takeaways

- "Can't SSH in" has at least three distinct failure modes: timeout, connection refused, and
  authentication failure — each points to a different layer.
- `ssh -v` shows exactly which keys were tried and why authentication was rejected.
- SSH silently ignores keys/files with overly permissive file modes.
- Always keep a working session open while testing any SSH configuration change.

## Related topics

- [Network Connectivity Issues](network-connectivity-issues.md)
- [Boot Issues and Rescue Mode](boot-issues-and-rescue-mode.md)
- [SSH](../13-ssh/index.md)
