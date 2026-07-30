---
title: "What Is Kubernetes and Why It Exists"
description: "The orchestration problem Docker alone doesn't solve — running many containers across many machines reliably, and why Kubernetes became the standard answer."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/docker-best-practices"]
relatedTopics: ["k8s-architecture-control-plane-and-nodes", "which-linux-skills-k8s-depends-on"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#why-kubernetes-exists"]
relatedCheatsheet: ""
furtherReading: [{"label": "Kubernetes Documentation", "url": "https://kubernetes.io/docs/concepts/overview/"}]
nextTopic: "24-kubernetes-basics/k8s-architecture-control-plane-and-nodes"
prevTopic: "23-docker-basics/docker-best-practices"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["what is kubernetes", "why kubernetes exists", "kubernetes vs docker compose", "container orchestration explained"]
canonicalUrl: "/docs/kubernetes-basics/what-is-kubernetes-and-why-it-exists"
---

# What Is Kubernetes and Why It Exists

🟢 Must Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** Kubernetes automates running containers across a fleet of machines — restarting
> failed containers, scheduling them onto available capacity, and scaling them up or down —
> problems [Docker Compose](../23-docker-basics/docker-compose-intro.md) alone doesn't solve once
> an application outgrows a single host.

## What is it?

A container orchestration platform: software that manages running containers across many
machines, handling scheduling, scaling, failure recovery, and networking automatically rather than
requiring manual intervention per container per host.

## Why does it exist?

Docker ([Module 23](../23-docker-basics/index.md)) and Compose solve running containers on a
single host well, but a production application often needs to run across many machines, survive
individual machine failures, and scale up or down based on load — problems that get unmanageable
to handle manually once more than a handful of containers and hosts are involved. Kubernetes exists
specifically to automate that fleet-wide management.

## Where is it used?

Any application that has outgrown a single host, or that needs automated failure recovery and
scaling — most large-scale production deployments across cloud providers and on-premises data
centers run on Kubernetes or a managed variant of it.

## How it works

> 📊 Diagram: a single-host Compose setup shown scaling smoothly (a few containers, one machine)
> versus the same application at fleet scale — many containers, many machines — with a labeled
> "orchestration" layer (Kubernetes) sitting above the fleet, automatically deciding which
> container runs on which machine, restarting failed ones, and redistributing load, none of which
> Compose is designed to do across multiple hosts.

**What Kubernetes automates that Compose doesn't:**

| Problem | Compose | Kubernetes |
|---|---|---|
| Scope | Single host | Many hosts (a cluster) |
| Failure recovery | Manual restart | Automatic — detects and replaces failed containers |
| Scaling | Manual, single-host | Automated, across the cluster, based on demand |
| Scheduling | N/A (one host) | Automatically places containers onto available capacity |
| Rolling updates | Manual | Built-in, automated, with rollback support |

**The core promise:** describe the *desired state* ("I want 3 copies of this application running,
always") and Kubernetes continuously works to make reality match that description — restarting
failed instances, rescheduling them elsewhere if a machine dies, automatically, without a human
issuing individual recovery commands.

## Real-world example

An application running as a single container on a single server crashes at 3 AM when that server's
disk fills up — nothing restarts it, and it stays down until someone notices and manually
intervenes. The same application, running under Kubernetes with a declared desired state of "3
replicas always running," would have Kubernetes automatically detect the failed instance,
reschedule a replacement onto a healthy machine, and restore the desired state without waking
anyone up — precisely the automated failure recovery this page identifies as Kubernetes' core
value proposition.

## Syntax

No single syntax — this page is conceptual; [kubectl Basics](kubectl-basics.md) introduces the
practical command-line interface.

## Commands

No command example on this page — see [kubectl Basics](kubectl-basics.md) for the first ones.

## Production example

```
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-7d9f8c6b5d-x2k9p   1/1     Running   0          2d
myapp-7d9f8c6b5d-m4n7q   1/1     Running   0          2d
myapp-7d9f8c6b5d-p8j2r   1/1     Running   0          2d
```

Three running instances of the same application, distributed and managed automatically — the
practical result of declaring "3 replicas" as the desired state, fully explained starting in
[kubectl Basics](kubectl-basics.md).

## Do / Don't

| Do | Don't |
|---|---|
| Reach for Kubernetes when an application genuinely needs multi-host orchestration | Adopt Kubernetes for a workload simple enough for Compose alone |
| Think in terms of declared desired state, not manual step-by-step commands | Expect to manually restart every failed container across a fleet |
| Understand Kubernetes as solving fleet-scale problems, not single-host convenience | Treat Kubernetes as simply "Docker but with more commands" |

## Common mistakes

- Adopting Kubernetes for a workload simple enough that Compose alone would suffice, taking on
  unnecessary operational complexity.
- Thinking of Kubernetes as just a more complicated way to run `docker run`, missing that its real
  value is automated fleet-wide management, not single-container convenience.
- Assuming Kubernetes eliminates the need for the Docker/container fundamentals from Module 23,
  when it actually builds directly on top of them.

## Best practices

- Match tooling to actual scale: Compose for single-host/simple setups, Kubernetes when genuine
  multi-host orchestration and automated recovery are actually needed.
- Think in terms of desired state ("I want N replicas running") rather than manual, imperative
  step-by-step container management.
- Build Kubernetes knowledge on top of solid Module 23 container fundamentals — Kubernetes doesn't
  replace that knowledge, it orchestrates containers built the same way.

## Exercises

1. List two problems Kubernetes solves that Docker Compose alone does not.
2. Explain the "desired state" concept in your own words.
3. Describe a scenario where Compose would be sufficient and Kubernetes would be unnecessary
   complexity.

## Quiz

**Q: What is the core problem Kubernetes solves that Docker Compose doesn't?**
<details><summary>Show answer</summary>
Orchestrating containers across many machines — scheduling, automated failure recovery, and
scaling at fleet scale — rather than managing containers on a single host.
</details>

**Q: What does "desired state" mean in the Kubernetes model?**
<details><summary>Show answer</summary>
A declared description of what should be running (e.g. "3 replicas of this application") that
Kubernetes continuously works to maintain automatically, rather than requiring manual commands to
restore after a failure.
</details>

**Q: Does Kubernetes replace the need to understand Docker/container fundamentals?**
<details><summary>Show answer</summary>
No — Kubernetes orchestrates containers built the same way as in Module 23; it adds fleet-scale
management on top, not a replacement for container fundamentals.
</details>

## Interview questions

- What problem does Kubernetes solve that Docker alone doesn't? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Kubernetes automates container orchestration across many machines — scheduling, failure
  recovery, and scaling.
- The core model is declared desired state, continuously reconciled automatically.
- Kubernetes builds on top of container fundamentals (Module 23), not a replacement for them.
- Match tooling to actual scale — Compose for simple setups, Kubernetes for genuine fleet-scale
  needs.

## Related topics

- [K8s Architecture: Control Plane and Nodes](k8s-architecture-control-plane-and-nodes.md)
- [Which Linux Skills K8s Actually Depends On](which-linux-skills-k8s-depends-on.md)
