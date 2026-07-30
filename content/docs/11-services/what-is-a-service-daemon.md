---
title: "What is a Service / Daemon"
description: "A daemon is a process deliberately detached from any controlling terminal, running independently in the background for as long as the system is up."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["10-processes/systemd-process-management-preview"]
relatedTopics: ["systemd-overview"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#daemon-explained"]
relatedCheatsheet: "systemctl"
furtherReading: []
nextTopic: "11-services/systemd-overview"
prevTopic: "10-processes/systemd-process-management-preview"
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["daemon explained linux", "what is a service linux", "daemon naming convention"]
canonicalUrl: "/docs/services/what-is-a-service-daemon"
---

# What is a Service / Daemon

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** A daemon is a process with no controlling terminal, running independently in the
> background for as long as the system is up — `sshd`, `nginx`, `cron` are all daemons. Many
> (not all) daemon names conventionally end in "d."

## What is it?

A daemon (or "service") is a process specifically designed to run detached from any controlling
terminal ([Module 10](../10-processes/foreground-vs-background-processes.md)), typically starting
at boot and running continuously in the background, providing some ongoing function rather than
producing a one-time result and exiting.

## Why does it exist?

Many things a system needs to do — accept SSH connections, serve web requests, run scheduled
jobs — need to happen continuously, independent of whether any user is logged in or any terminal
is open. A daemon is a process built for exactly that: no controlling terminal to depend on, no
interactive session required, just a long-running, background function.

## Where is it used?

Virtually every meaningful background function on a Linux system: `sshd` (accepts SSH
connections), `nginx`/`httpd` (serves web requests), `cron`/`crond` (runs scheduled jobs, Module
17), `systemd` itself (PID 1, supervising everything else).

## How it works

> 📊 Diagram: a process shown explicitly detached from any terminal icon (no controlling terminal
> connection line, unlike the foreground/background processes diagrammed in Module 10), with a
> label "started at boot, runs continuously, no interactive session."

A daemon differs from an ordinary background process (Module 10's `&`) in a specific technical
way: it detaches fully from its controlling terminal (historically via a "double-fork" technique),
so it survives independent of any terminal session entirely — not just backgrounded within one,
but structurally disconnected from having one at all. In modern practice, systemd (Module 11's
next topic) handles this detachment automatically for services it manages, so you rarely need to
implement it yourself.

**Naming convention**: many daemon names end in "d" (`sshd`, `crond`, `systemd` itself) as a
loose, widely-followed convention signaling "this is a background daemon" — not a hard rule
(`nginx` and `cron` on some distros don't follow it), but useful as a quick visual cue.

## Real-world example

A user SSHs into a server, and their interactive shell session is a normal foreground process
tied to that SSH connection — closing the SSH session ends it. Meanwhile, `sshd` itself (the
daemon that accepted that connection in the first place) keeps running entirely independently,
ready to accept the next connection, regardless of what happens to any individual user's session.

## Commands

No command example on this page — this page is conceptual context. See
[systemctl: start, stop, enable, disable, status](systemctl-start-stop-enable-disable-status.md)
for the first hands-on service management in this module.

## Production example

Not applicable — see
[systemctl: start, stop, enable, disable, status](systemctl-start-stop-enable-disable-status.md)
for the first terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Recognize daemons as structurally detached from any terminal, not just backgrounded | Confuse a daemon with an ordinary `&`-backgrounded process from Module 10 |
| Use the "-d" naming convention as a helpful hint, not a strict rule | Assume every service name ends in "d" |

## Common mistakes

- Treating "backgrounded with `&`" (Module 10) and "a daemon" as the same thing — a daemon is
  structurally detached from any controlling terminal, not just running behind an active one.
- Assuming every daemon's name ends in "d" — it's a common, useful convention, not a guarantee.

## Best practices

- Let systemd handle the technical detachment of a custom long-running process (Module 11's next
  topics) rather than trying to implement daemonization manually — it's a solved, well-tested
  problem you don't need to reinvent.

## Exercises

1. Name three daemons running on any Linux system you have access to.
2. Explain in one sentence why a daemon needs to be detached from a controlling terminal, not
   just run in the background of one.
3. Give one example of a daemon whose name doesn't end in "d."

## Quiz

**Q: What makes a daemon different from an ordinary background process started with `&`?**
<details><summary>Show answer</summary>
A daemon is structurally detached from any controlling terminal entirely, while an `&`-backgrounded
process is still tied to the terminal session it was started from (Module 10).
</details>

## Interview questions

- What is a daemon, and how does it differ from a regular background process? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A daemon runs detached from any controlling terminal, continuously, typically from boot.
- This is structurally different from an ordinary `&`-backgrounded process (Module 10).
- Many daemon names end in "d" by convention, not by rule.
- systemd handles the technical detachment automatically for services it manages.

## Related topics

- [systemd Overview](systemd-overview.md)
- [Module 10: Foreground vs Background Processes](../10-processes/foreground-vs-background-processes.md)
