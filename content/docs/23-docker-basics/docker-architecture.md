---
title: "Docker Architecture"
description: "The client-daemon-registry model behind every docker command — what actually happens when you run docker run, and where containerd fits in underneath it."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["22-containers/why-containers-matter-for-devops-cloud-roles"]
relatedTopics: ["installing-docker", "images-vs-containers"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#docker-architecture"]
relatedCheatsheet: "docker-basics"
furtherReading: [{"label": "Docker Architecture Documentation", "url": "https://docs.docker.com/get-started/overview/#docker-architecture"}]
nextTopic: "23-docker-basics/installing-docker"
prevTopic: "22-containers/why-containers-matter-for-devops-cloud-roles"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["docker architecture explained", "docker client daemon", "dockerd explained", "docker registry explained"]
canonicalUrl: "/docs/docker-basics/docker-architecture"
---

# Docker Architecture

🟢 Must Know · Relevant for: DevOps · Cloud · Platform · Software Engineering

> **TL;DR:** The `docker` command is a client that talks to a background daemon (`dockerd`), which
> does the actual work — pulling images from a registry, and delegating to `containerd`
> ([Module 22](../22-containers/container-runtimes-overview.md)) to run containers.

## What is it?

The three-part model behind every Docker interaction: the client (the `docker` command you type),
the daemon (`dockerd`, running in the background, doing the actual work), and a registry (where
images are stored and fetched from).

## Why does it exist?

Separating "what you type" (the client) from "what does the work" (the daemon) allows the daemon to
keep running and managing containers independently of any specific terminal session, and allows
the client to potentially talk to a daemon running on a different machine entirely — the same
client-server separation pattern that shows up throughout this documentation (SSH's client/server
model from [Module 13](../13-ssh/what-is-ssh-and-how-it-works.md) is architecturally similar).

## Where is it used?

Every single Docker interaction, whether it's a developer's laptop running `docker run` locally or
a CI pipeline issuing Docker commands against a remote build server — the same three-part model
applies identically.

## How it works

> 📊 Diagram: three boxes left to right — "docker CLI" (client, what the user types) with an arrow
> labeled "REST API over a Unix socket" pointing to "dockerd" (daemon, the background process
> actually doing the work), which itself has two further arrows: one to "containerd"
> ([Module 22](../22-containers/container-runtimes-overview.md), actually running containers) and
> one to "registry" (e.g. Docker Hub, where images are pulled from and can be pushed to).

| Component | Role |
|---|---|
| `docker` CLI (client) | What the user types — translates commands into API requests |
| `dockerd` (daemon) | Background process that receives those requests and does the actual work |
| `containerd` | The runtime `dockerd` delegates actual container execution to (Module 22) |
| Registry | Where images are stored — Docker Hub by default, or a private registry |

**The request flow for `docker run nginx`:**

1. The `docker` CLI sends a request to `dockerd` over a local Unix socket (or, less commonly, a
   network socket).
2. `dockerd` checks whether the `nginx` image already exists locally; if not, it pulls it from the
   configured registry (Docker Hub by default).
3. `dockerd` delegates to `containerd` to actually create the namespaced, cgroup-limited process
   ([Module 22](../22-containers/namespaces-and-cgroups-explained.md)) that becomes the running
   container.

This layered flow is why `docker run` "just works" from the user's perspective while actually
involving several distinct components underneath.

## Real-world example

A developer runs `docker ps` and sees no output, then remembers Docker's daemon (`dockerd`) isn't
running — the `docker` CLI itself is just a thin client, and every command fails immediately if the
daemon it talks to isn't up, distinct from a typical standalone command that does its own work
directly. Starting the daemon (`sudo systemctl start docker`, using
[Module 11's](../11-services/index.md) service management) resolves it — a direct, practical
consequence of the client-daemon split this page explains.

## Syntax

No single syntax — this page is conceptual; the next-but-one page introduces installation, and
[Docker Run, ps, exec, logs](docker-run-ps-exec-logs.md) introduces the practical commands.

## Commands

No command example on this page — see [Installing Docker](installing-docker.md) for the setup
steps that make the architecture described here actually usable.

## Production example

```
$ docker version
Client:
 Version:           24.0.7
Server: Docker Engine - Community
 Engine:
  Version:          24.0.7
```

`docker version` reports both the client and server (daemon) versions separately — direct evidence
that they're distinct components, not one monolithic program.

## Do / Don't

| Do | Don't |
|---|---|
| Understand `docker` as a client talking to a separate daemon | Assume the `docker` command does all the work itself, standalone |
| Check whether `dockerd` is running when every `docker` command fails immediately | Assume a failing `docker` command is always a syntax error |
| Recognize `containerd` as the layer actually running containers underneath `dockerd` | Conflate `docker`, `dockerd`, and `containerd` as one single thing |

## Common mistakes

- Assuming the `docker` CLI itself does the container work, rather than recognizing it as a client
  sending requests to a separate background daemon.
- Not checking whether `dockerd` is running when every Docker command fails identically and
  immediately — a classic first troubleshooting step this architecture makes necessary.
- Confusing `docker`, `dockerd`, and `containerd` as interchangeable names for the same thing, when
  each is a distinct component with a distinct job.

## Best practices

- Check the daemon's status (`systemctl status docker`, Module 11) as the first troubleshooting
  step whenever every Docker command fails uniformly.
- Understand the request flow (client → daemon → containerd/registry) well enough to reason about
  where in that chain a specific failure is occurring.
- Keep the three components' distinct roles clear — it directly explains behavior that would
  otherwise seem mysterious (like `docker version` reporting two separate version numbers).

## Exercises

1. Run `docker version` and identify the client and server (daemon) version numbers separately.
2. Explain what happens, step by step, when `docker run nginx` is executed for the first time on a
   machine with no local `nginx` image.
3. Describe why checking `dockerd`'s status is a sensible first troubleshooting step when Docker
   commands fail.

## Quiz

**Q: What are the three main components of Docker's architecture?**
<details><summary>Show answer</summary>
The client (`docker` CLI), the daemon (`dockerd`), and a registry — with `dockerd` further
delegating actual container execution to `containerd`.
</details>

**Q: Why does docker version show two separate version numbers?**
<details><summary>Show answer</summary>
Because the client and the daemon (server) are genuinely separate components, each with its own
version — `docker version` reports both distinctly.
</details>

**Q: What's the first thing to check if every docker command fails immediately?**
<details><summary>Show answer</summary>
Whether the Docker daemon (`dockerd`) is actually running — since the `docker` CLI is just a
client, it can't function at all without a running daemon to talk to.
</details>

## Interview questions

- Walk through what happens internally when you run `docker run nginx` for the first time. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Docker's architecture separates the client (`docker` CLI), the daemon (`dockerd`), and a
  registry — with `containerd` doing the actual container execution underneath `dockerd`.
- This client-daemon split means every Docker command depends on a running daemon, independent of
  any specific terminal session.
- `docker version` reporting separate client/server versions is direct evidence of this
  architecture.
- Checking the daemon's status is the standard first troubleshooting step when Docker commands
  fail uniformly.

## Related topics

- [Installing Docker](installing-docker.md)
- [Images vs Containers](images-vs-containers.md)
