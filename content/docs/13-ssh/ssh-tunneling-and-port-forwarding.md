---
title: "SSH Tunneling and Port Forwarding"
description: "Using an existing SSH connection to carry traffic for a completely different service — reaching something not directly exposed, through a connection that already is."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["13-ssh/ssh-config-file"]
relatedTopics: ["ssh-agent-and-agent-forwarding"]
relatedCommands: ["ssh"]
careerRelevance: ["devops", "sre", "cybersecurity", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ssh-tunneling-local-vs-remote"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "13-ssh/ssh-hardening-disable-root-login-key-only-auth"
prevTopic: "13-ssh/ssh-config-file"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["ssh -l port forwarding", "ssh -r remote forwarding", "ssh tunnel explained", "ssh -d socks proxy"]
canonicalUrl: "/docs/ssh/ssh-tunneling-and-port-forwarding"
---

# SSH Tunneling and Port Forwarding

🔴 Expert · Relevant for: DevOps · SRE · Cybersecurity · Cloud

> **TL;DR:** Local forwarding (`-L`) makes a remote service reachable as if it were local. Remote
> forwarding (`-R`) does the reverse — makes a local service reachable from the remote side. Both
> carry traffic for a completely different service through an existing SSH connection.

## What is it?

A technique for routing traffic for some other service (a database, a web dashboard, anything
with its own port) through an already-established, encrypted SSH connection — reaching something
that isn't directly network-accessible, through a connection that already is.

## Why does it exist?

A database or internal admin dashboard is often deliberately not exposed directly to the internet
— it only accepts connections from `localhost` on its own server, or is firewalled off from
outside access entirely (Module 12's firewall topic). SSH tunneling exists to reach that service
anyway, securely, by riding on top of an SSH connection that *is* allowed through, without
opening the target service's port to the outside world at all.

## Where is it used?

Accessing a database or internal web dashboard that only listens on `localhost` on a remote
server, reaching an internal service across a security boundary without weakening the firewall,
and — for remote forwarding — exposing a local development service temporarily to a remote
machine.

## How it works

> 📊 Diagram: two contrasting flows — Local forwarding: a local port on your machine shown
> connecting through the SSH tunnel to a service listening on the remote server's `localhost`.
> Remote forwarding: the reverse, a port opened on the remote server shown tunneling back to a
> service running on your local machine.

| Flag | Direction | Use case |
|---|---|---|
| `-L local_port:target_host:target_port` | Local forwarding | Reach a remote-side-only service as if it were on your own machine |
| `-R remote_port:target_host:target_port` | Remote forwarding | Expose a local (or local-network) service to the remote side |
| `-D local_port` | Dynamic forwarding (SOCKS proxy) | Route arbitrary traffic through the tunnel, browser-proxy style |

**Local forwarding example**: a database on a remote server only accepts connections from
`localhost` on that same server. `ssh -L 5432:localhost:5432 user@dbserver` makes
`localhost:5432` on *your own machine* transparently forward to `localhost:5432` on the remote
server — your local database client just connects to `localhost:5432` normally, with SSH quietly
carrying that traffic to the actual remote database underneath.

## Real-world example

A production database is correctly firewalled to only accept connections from `localhost` on its
own server — a deliberate security control. An engineer needs to run an ad hoc query using their
own local GUI database client. Rather than weakening the firewall rule (a real security
regression) or copying data out insecurely, they run
`ssh -L 5432:localhost:5432 user@dbserver` and point their local client at `localhost:5432` — the
database's actual access boundary is never touched, and the connection is fully encrypted for the
whole trip via SSH.

## Commands

No new command — tunneling is a set of flags (`-L`/`-R`/`-D`) on
[`ssh`](../../commands/ssh.md), already covered earlier in this module, not a separate tool.

## Production example

```
$ ssh -L 5432:localhost:5432 user@dbserver
```

Leave this connection open, then in another terminal (or a GUI client):

```
$ psql -h localhost -p 5432 -U appuser mydb
```

The client believes it's talking to a local database; SSH is transparently forwarding that traffic
to the real remote instance.

## Do / Don't

| Do | Don't |
|---|---|
| Use tunneling to reach a firewalled service without weakening the firewall itself | Open a firewall rule just to avoid setting up a tunnel |
| Close a tunnel (end the SSH session) once the ad hoc task is done | Leave a long-forgotten tunnel open indefinitely |
| Use local forwarding (`-L`) to reach a remote-only service | Confuse local and remote forwarding direction under pressure |

## Common mistakes

- Weakening a firewall rule to directly expose a service, when a tunnel would have reached it
  just as effectively without touching the security boundary at all.
- Confusing `-L` and `-R` direction under pressure — `-L` reaches something on the remote side
  from your local machine; `-R` does the reverse.
- Leaving ad hoc tunnels open far longer than needed, an easy-to-forget, unnecessary standing
  connection.

## Best practices

- Prefer a tunnel over loosening a firewall rule whenever the need is temporary or one-off.
- Close tunnels once their purpose is served, rather than leaving them open indefinitely.
- Combine tunneling with [`~/.ssh/config`](ssh-config-file.md) `LocalForward` directives for
  tunnels used routinely, instead of retyping the full flag every time.

## Exercises

1. Set up a local port forward to a service only listening on `localhost` on a remote machine you
   have access to.
2. Explain in one sentence the directional difference between `-L` and `-R`.
3. Explain why tunneling is preferable to opening a firewall rule for a temporary, ad hoc need.

## Quiz

**Q: What does `ssh -L 5432:localhost:5432 user@dbserver` accomplish?**
<details><summary>Show answer</summary>
It forwards `localhost:5432` on your own machine to `localhost:5432` on the remote server,
letting a local client reach a remote-only-accessible service as if it were local.
</details>

**Q: What's the direction difference between local (`-L`) and remote (`-R`) forwarding?**
<details><summary>Show answer</summary>
Local forwarding (`-L`) lets you reach a remote-side service from your local machine. Remote
forwarding (`-R`) does the reverse — it exposes a local (or local-network) service to the remote
side.
</details>

## Interview questions

- How would you securely access a database that only accepts connections from `localhost` on a
  remote server? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- SSH tunneling carries traffic for another service through an existing, encrypted SSH
  connection.
- Local forwarding (`-L`) reaches a remote-side service; remote forwarding (`-R`) exposes a local
  service to the remote side.
- Tunneling reaches a firewalled service without weakening the firewall itself.
- Close ad hoc tunnels once their purpose is done rather than leaving them open indefinitely.

## Related topics

- [SSH Agent and Agent Forwarding](ssh-agent-and-agent-forwarding.md)
- [SSH Config File](ssh-config-file.md)
