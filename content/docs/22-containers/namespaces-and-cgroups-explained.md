---
title: "Namespaces and cgroups Explained"
description: "The two kernel features that actually implement container isolation — namespaces controlling what a process can see, cgroups controlling what it can use — with no container-specific code involved."
module: "22-containers"
moduleTitle: "Containers"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["22-containers/containers-vs-vms"]
relatedTopics: ["containers-vs-vms", "container-runtimes-overview"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#namespaces-vs-cgroups"]
relatedCheatsheet: ""
furtherReading: [{"label": "namespaces(7) man page", "url": "https://man7.org/linux/man-pages/man7/namespaces.7.html"}, {"label": "cgroups(7) man page", "url": "https://man7.org/linux/man-pages/man7/cgroups.7.html"}]
nextTopic: "22-containers/container-runtimes-overview"
prevTopic: "22-containers/containers-vs-vms"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["linux namespaces explained", "cgroups explained", "pid namespace", "container isolation kernel mechanism"]
canonicalUrl: "/docs/containers/namespaces-and-cgroups-explained"
---

# Namespaces and cgroups Explained

🔴 Expert · Relevant for: DevOps · Cloud · Platform · Linux Administrator

> **TL;DR:** Namespaces make a process see only its own isolated view of processes, network,
> filesystem, and hostname. cgroups limit how much CPU/memory/I/O a process (or group of processes)
> can actually use. Together, applied to an ordinary process, they *are* what a container is —
> no separate "container" kernel object exists.

## What is it?

The two Linux kernel features that implement container isolation in practice — namespaces
(isolating what a process can *see*) and cgroups (limiting what a process can *use*) — introduced
conceptually on [What Is Containerization](what-is-containerization.md), given full mechanical
detail here.

## Why does it exist?

A container needs to look, from inside, like it has its own machine — its own process list, its
own network interfaces, its own filesystem root — while actually running on a kernel shared with
every other process on the host. Namespaces provide exactly this illusion of isolation; cgroups
separately ensure no single container can starve the others of CPU, memory, or I/O. Both existed
in the Linux kernel as general-purpose features before container tooling (Docker and others) built
user-friendly interfaces on top of them.

## Where is it used?

Every container runtime ([Container Runtimes Overview](container-runtimes-overview.md), next page)
uses namespaces and cgroups under the hood — this isn't unique to Docker, but a shared kernel
foundation every container technology builds on identically.

## How it works

> 📊 Diagram: a single process shown with six labeled namespace "windows" around it (PID, Network,
> Mount, UTS, IPC, User), each window showing that process seeing only its own isolated view
> through that window rather than the host's full state — with a separate cgroup "fence" drawn
> around the same process limiting its CPU/memory/I/O consumption regardless of what it can see.

**Namespaces — what a process can see:**

| Namespace | Isolates |
|---|---|
| PID | Process IDs — a containerized process can be PID 1 inside its own namespace while being a normal, high-numbered PID on the host |
| Network | Network interfaces, IP addresses, routing tables, ports |
| Mount | Filesystem mount points — a container can have an entirely different root filesystem view |
| UTS | Hostname and domain name |
| IPC | Inter-process communication resources (shared memory, semaphores) |
| User | User and group ID mappings — a process can be "root" inside its namespace while mapped to an unprivileged user on the host |

A container is created by placing a process into a new instance of each of these namespaces
simultaneously — it can only see its own processes, its own network configuration, its own
filesystem, and so on, with no visibility into the host's or other containers' equivalents.

**cgroups — what a process can use:**

cgroups (control groups) limit and account for resource usage — CPU shares, memory limits, block
I/O bandwidth — for a process or a group of processes, entirely independent of what those processes
can see via namespaces. A container's "resource limits" (e.g. "this container gets at most 512MB of
RAM") are cgroup limits applied to the namespaced process(es) that make up that container.

**The key insight:** a "container" isn't a special kernel object at all — it's an ordinary process,
placed into a specific set of namespaces and constrained by specific cgroup limits. Container
runtimes exist to make configuring these kernel mechanisms convenient; they don't invent new
kernel-level isolation primitives.

## Real-world example

An engineer debugging a container issue runs `ps aux` on the host and is confused to see the
containerized process listed with a completely different, high-numbered PID than the `PID 1` it
reports internally when the container is inspected from inside. Understanding PID namespaces
resolves the apparent contradiction immediately: the container process genuinely has two valid PIDs
simultaneously — one within its own PID namespace (where it can be 1) and one on the host's PID
namespace (an ordinary, unprivileged-looking PID) — exactly the isolation namespaces are designed
to provide.

## Syntax

No single syntax — namespaces and cgroups are kernel features configured via system calls and
`/sys/fs/cgroup`, typically never touched directly; container runtimes (next page) provide the
practical interface.

## Commands

No command example on this page — container runtimes and Docker (Module 23) provide the practical
interface to these kernel features; this documentation doesn't teach direct low-level namespace/
cgroup manipulation as a standalone skill.

## Production example

```
$ docker run -d --memory=512m --cpus=1 nginx
$ docker inspect --format='{{.State.Pid}}' <container_id>
48213

$ ps -p 48213 -o pid,cmd
  PID CMD
48213 nginx: master process
```

The container's process is visible on the host with an ordinary PID (48213), while `--memory` and
`--cpus` configured cgroup limits — Docker's convenient interface to the exact kernel mechanisms
this page describes.

## Do / Don't

| Do | Don't |
|---|---|
| Understand a container as a namespaced, cgroup-limited ordinary process | Think of "container" as a distinct kernel-level object type |
| Recognize that container tooling configures existing kernel features | Assume Docker/containerd invented a new isolation mechanism |
| Distinguish "what a process can see" (namespaces) from "what it can use" (cgroups) | Conflate isolation (namespaces) with resource limiting (cgroups) as the same thing |

## Common mistakes

- Believing "container" refers to a special kernel object, rather than understanding it's an
  ordinary process placed into specific namespaces with cgroup limits applied.
- Conflating namespaces (what a process can see) with cgroups (what it can use) as the same
  mechanism, when they solve genuinely different problems.
- Being confused by a containerized process having different PIDs inside versus outside its
  namespace, not recognizing this as expected, correct namespace behavior.

## Best practices

- Build the mental model that container tooling is a convenience layer over pre-existing kernel
  features, not a fundamentally new technology — this demystifies debugging container behavior
  significantly.
- Keep namespaces and cgroups conceptually separate: visibility versus resource limits.
- When troubleshooting container behavior that seems to violate expectations, consider whether a
  specific namespace (network, mount, PID) explains the observed behavior before assuming a bug.

## Exercises

1. List the six namespace types covered on this page and what each isolates.
2. Explain the difference between what namespaces control and what cgroups control.
3. Explain why a containerized process can have two different, valid PIDs simultaneously.

## Quiz

**Q: What do namespaces control, as distinct from cgroups?**
<details><summary>Show answer</summary>
Namespaces control what a process can see (its own process list, network, filesystem, etc.);
cgroups control what a process can use (CPU, memory, I/O limits) — different problems, both
required together to implement container isolation.
</details>

**Q: Is a container a distinct kernel object type?**
<details><summary>Show answer</summary>
No — a container is an ordinary Linux process placed into a specific set of namespaces and
constrained by cgroup limits; there's no separate "container" primitive in the kernel.
</details>

**Q: Why might a containerized process show different PIDs inside versus outside the container?**
<details><summary>Show answer</summary>
The PID namespace gives the process its own isolated PID numbering (it can be PID 1 inside), while
the host sees it under a separate, ordinary PID in the host's own PID namespace — both are
simultaneously valid.
</details>

## Interview questions

- Explain, at the kernel level, what actually makes a container isolated from the host system. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Namespaces isolate what a process can see (PID, network, mount, UTS, IPC, user); cgroups limit
  what it can use (CPU, memory, I/O).
- A container is an ordinary process placed into namespaces and constrained by cgroups — not a
  distinct kernel object.
- Container runtimes are a convenience layer over these pre-existing kernel features, not a new
  isolation mechanism.
- A containerized process legitimately has different PIDs inside and outside its PID namespace
  simultaneously.

## Related topics

- [Containers vs VMs](containers-vs-vms.md)
- [Container Runtimes Overview](container-runtimes-overview.md)
