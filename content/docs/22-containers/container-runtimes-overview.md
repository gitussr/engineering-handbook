---
title: "Container Runtimes Overview: containerd, CRI-O"
description: "What actually runs a container day-to-day beneath Docker's user-facing CLI — containerd and CRI-O, and why Kubernetes needed a standard interface to talk to either one."
module: "22-containers"
moduleTitle: "Containers"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["22-containers/namespaces-and-cgroups-explained"]
relatedTopics: ["namespaces-and-cgroups-explained", "why-containers-matter-for-devops-cloud-roles"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#docker-vs-containerd-vs-cri-o"]
relatedCheatsheet: ""
furtherReading: [{"label": "containerd Documentation", "url": "https://containerd.io/docs/"}]
nextTopic: "22-containers/why-containers-matter-for-devops-cloud-roles"
prevTopic: "22-containers/namespaces-and-cgroups-explained"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["containerd vs docker", "cri-o explained", "container runtime interface CRI", "what is containerd"]
canonicalUrl: "/docs/containers/container-runtimes-overview"
---

# Container Runtimes Overview: containerd, CRI-O

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** Docker's CLI is a user-facing tool; underneath it, `containerd` is the runtime that
> actually manages containers day-to-day. CRI-O is a leaner alternative built specifically for
> Kubernetes, implementing the same standard interface (CRI) rather than Docker's broader tooling.

## What is it?

The layer of software that actually creates and manages containers using the namespaces/cgroups
mechanisms from the [previous page](namespaces-and-cgroups-explained.md) — distinct from Docker's
user-facing CLI, which is a tool built on top of a runtime, not the runtime itself.

## Why does it exist?

Docker popularized containers with a convenient, all-in-one CLI, but "actually running a
container" is a distinct, smaller job than everything Docker's tooling does (image building,
networking, CLI ergonomics). Separating that core runtime job into its own component
(`containerd`) let it be reused by other tools — most importantly Kubernetes, which needed a
standard way to talk to *any* compliant runtime, not just Docker specifically.

## Where is it used?

Every Docker installation actually uses `containerd` underneath, whether or not the user ever
interacts with it directly. Kubernetes clusters explicitly choose a runtime (commonly `containerd`
or `CRI-O`) as a cluster-level configuration decision, since Kubernetes itself doesn't run
containers directly — it delegates to whichever compliant runtime is configured.

## How it works

> 📊 Diagram: a layered stack — at the top, user-facing tools (`docker` CLI, `kubectl`); in the
> middle, the Container Runtime Interface (CRI), a standard API; at the bottom, interchangeable
> runtime implementations (`containerd`, `CRI-O`) that actually create namespaced, cgroup-limited
> processes — showing that the top layer talks to the standard interface, not directly to a
> specific runtime implementation.

**`containerd`** — the runtime Docker itself uses underneath its CLI; also usable directly (or via
Kubernetes) without Docker's additional tooling layered on top. Handles the actual work of pulling
images, managing container lifecycle, and configuring namespaces/cgroups.

**CRI-O** — a runtime built specifically to implement the Container Runtime Interface (CRI, see
below) for Kubernetes, deliberately leaner than `containerd` since it doesn't need to support
Docker's broader non-Kubernetes use cases.

**The Container Runtime Interface (CRI)** — a standard API Kubernetes uses to talk to *any*
compliant runtime, rather than being hard-coded to Docker specifically. This is why Kubernetes
could shift its default away from Docker's full stack toward `containerd`/`CRI-O` directly,
without changing how Kubernetes itself works — both runtimes implement the same standard interface
Kubernetes expects.

**Why this separation matters practically:** it means "Docker" and "containers" aren't
synonymous — Docker is one popular *user experience* around containers, but the actual container
runtime layer is shared, standardized infrastructure that multiple tools (Docker, Kubernetes) can
use interchangeably.

## Real-world example

A team migrating their Kubernetes cluster away from Docker as the underlying runtime (a real
industry-wide shift, since Kubernetes deprecated direct Docker support in favor of CRI-compliant
runtimes) discovers their workloads continue running completely unchanged under `containerd`
directly — because Kubernetes was always talking to a CRI-compliant runtime through the standard
interface, not to Docker-specific behavior. The migration is a configuration change at the
infrastructure level, invisible to the applications themselves.

## Syntax

No single syntax — this page is conceptual; [Module 23](../23-docker-basics/index.md) introduces
the practical `docker` CLI, which is what most engineers interact with directly regardless of
which runtime operates underneath it.

## Commands

No canonical command pages exist for `containerd`'s or `CRI-O`'s own low-level CLIs (`ctr`,
`crictl`) — these are debugging tools rarely used directly outside specialized infrastructure work;
this documentation teaches the `docker` CLI (Module 23) as the practical, everyday interface (see
this module's [intentional gaps](index.md)).

## Production example

```
$ docker info | grep "Server Version\|Runtimes\|Default Runtime"
 Server Version: 24.0.7
 Runtimes: runc
 Default Runtime: runc
```

`docker info` reveals the runtime configuration underneath Docker's own CLI — confirming this
layered architecture is real and inspectable, not just theoretical.

## Do / Don't

| Do | Don't |
|---|---|
| Understand Docker's CLI as a user experience layered on top of a runtime | Treat "Docker" and "containers" as synonymous |
| Recognize CRI as the standard interface enabling runtime interchangeability | Assume Kubernetes is permanently tied to Docker specifically |
| Know that `containerd`/`CRI-O` do the actual container-creation work | Assume Docker's CLI itself directly manipulates namespaces/cgroups |

## Common mistakes

- Treating "Docker" and "containers" as interchangeable terms, missing that Docker is one tool
  built on top of a separate, standardized runtime layer.
- Assuming Kubernetes requires Docker specifically, unaware of the CRI standard that lets it use
  any compliant runtime interchangeably.
- Not realizing `containerd` already runs underneath every Docker installation, even when the user
  never interacts with it directly.

## Best practices

- Understand the layered architecture (CLI → CRI → runtime) well enough to reason about
  infrastructure changes like a Kubernetes cluster's runtime migration.
- Don't assume application behavior depends on which specific CRI-compliant runtime is
  configured underneath — that's precisely the abstraction CRI is designed to provide.
- Recognize `docker info`-style commands as a way to inspect the runtime layer directly when
  troubleshooting.

## Exercises

1. Explain the relationship between Docker's CLI, `containerd`, and the kernel features from the
   previous page.
2. Describe what the Container Runtime Interface (CRI) standardizes, and why that mattered for
   Kubernetes specifically.
3. Run `docker info` (if available) and identify which runtime is configured underneath.

## Quiz

**Q: Is Docker's CLI the same thing as the container runtime?**
<details><summary>Show answer</summary>
No — Docker's CLI is a user-facing tool that itself relies on `containerd` as the actual runtime
doing the underlying container-management work.
</details>

**Q: What does the Container Runtime Interface (CRI) provide?**
<details><summary>Show answer</summary>
A standard API that lets Kubernetes talk to any compliant container runtime (`containerd`,
`CRI-O`, etc.) interchangeably, rather than being hard-coded to one specific runtime.
</details>

**Q: Why might a team migrate a Kubernetes cluster's runtime without changing their applications?**
<details><summary>Show answer</summary>
Because Kubernetes interacts with the runtime through the standardized CRI interface — swapping
the underlying CRI-compliant runtime is an infrastructure-level change invisible to the
applications themselves.
</details>

## Interview questions

- Explain the relationship between Docker, containerd, and Kubernetes' Container Runtime
  Interface. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Docker's CLI is a user experience layer; `containerd` is the runtime actually doing the work
  underneath it.
- CRI-O is a leaner runtime built specifically for Kubernetes via the CRI standard.
- The Container Runtime Interface (CRI) standardizes how Kubernetes talks to any compliant runtime,
  enabling runtime interchangeability without application-level impact.
- "Docker" and "containers" are not synonymous — Docker is one tool among several built on shared,
  standardized container infrastructure.

## Related topics

- [Namespaces and cgroups Explained](namespaces-and-cgroups-explained.md)
- [Why Containers Matter for DevOps/Cloud Roles](why-containers-matter-for-devops-cloud-roles.md)
