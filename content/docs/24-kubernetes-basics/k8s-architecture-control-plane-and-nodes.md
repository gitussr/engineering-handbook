---
title: "K8s Architecture: Control Plane and Nodes"
description: "The two-tier architecture behind every cluster — the control plane deciding what should run, and worker nodes actually running it via the container runtimes from Module 22."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/what-is-kubernetes-and-why-it-exists"]
relatedTopics: ["what-is-kubernetes-and-why-it-exists", "pods-deployments-services"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#control-plane-vs-node"]
relatedCheatsheet: ""
furtherReading: [{"label": "Kubernetes Components", "url": "https://kubernetes.io/docs/concepts/overview/components/"}]
nextTopic: "24-kubernetes-basics/pods-deployments-services"
prevTopic: "24-kubernetes-basics/what-is-kubernetes-and-why-it-exists"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["kubernetes control plane explained", "kubernetes worker node", "kube-apiserver etcd scheduler", "kubelet explained"]
canonicalUrl: "/docs/kubernetes-basics/k8s-architecture-control-plane-and-nodes"
---

# K8s Architecture: Control Plane and Nodes

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** The control plane decides what *should* run and where; worker nodes actually run it,
> using the container runtimes ([Module 22](../22-containers/container-runtimes-overview.md))
> already covered. `kubelet` on each node is the agent that makes the node's reality match the
> control plane's decisions.

## What is it?

The two-tier architecture behind every Kubernetes cluster: a control plane (the decision-making
layer) and worker nodes (the machines that actually run containers), communicating continuously to
keep reality matching the declared desired state from the
[previous page](what-is-kubernetes-and-why-it-exists.md).

## Why does it exist?

Someone (or something) has to decide *which* machine runs *which* container, track the current
state of the entire cluster, and detect when reality drifts from what was declared — that's the
control plane's job, kept deliberately separate from the nodes that do the actual container
execution, so the decision-making layer can manage many nodes without being tied to any single
one's lifecycle.

## Where is it used?

Every Kubernetes cluster, whether self-managed or a cloud provider's managed offering (which
typically manages the control plane on the user's behalf, exposing only the worker nodes and the
`kubectl` interface).

## How it works

> 📊 Diagram: a control plane box (containing `kube-apiserver`, `etcd`, `kube-scheduler`,
> `kube-controller-manager`) with arrows out to multiple worker node boxes, each containing
> `kubelet` and a container runtime ([Module 22](../22-containers/container-runtimes-overview.md))
> — `kubectl` shown talking only to the control plane's API server, never directly to individual
> nodes.

**Control plane components:**

| Component | Role |
|---|---|
| `kube-apiserver` | The front door — every request (including from `kubectl`) goes through this API |
| `etcd` | The cluster's source of truth — a distributed key-value store holding all cluster state |
| `kube-scheduler` | Decides which node a new container should run on, based on available resources |
| `kube-controller-manager` | Continuously watches actual state vs. desired state and takes corrective action |

**Worker node components:**

| Component | Role |
|---|---|
| `kubelet` | The agent on each node that receives instructions from the control plane and ensures containers are actually running as declared |
| Container runtime | `containerd`/CRI-O ([Module 22](../22-containers/container-runtimes-overview.md)) — actually runs the containers `kubelet` tells it to |
| `kube-proxy` | Handles network routing so traffic reaches the right container on the right node |

**The reconciliation loop:** the control plane continuously compares actual cluster state (from
`etcd`) against desired state, and `kube-controller-manager` takes corrective action whenever they
diverge — this loop is what actually implements the "desired state" promise from the
[previous page](what-is-kubernetes-and-why-it-exists.md), running constantly rather than only when
a human notices a problem.

## Real-world example

A worker node crashes unexpectedly, taking down every container it was running. The control
plane's continuous reconciliation loop detects (via `etcd`'s recorded desired state no longer
matching actual reality) that the required number of replicas is no longer running, and
`kube-scheduler` automatically places replacement containers on other healthy nodes — all without
any human intervention, purely because the architecture separates "what should be running"
(control plane, durably recorded in `etcd`) from "what's actually running on this specific machine"
(a worker node, which can fail without losing the cluster's overall state).

## Syntax

No single syntax — this page is conceptual; [kubectl Basics](kubectl-basics.md) (next-but-one
page) introduces the practical interface to this architecture.

## Commands

No command example on this page — see [kubectl Basics](kubectl-basics.md) for the first ones.

## Production example

```
$ kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
node-1     Ready    control-plane   30d   v1.29.0
node-2     Ready    <none>          30d   v1.29.0
node-3     Ready    <none>          30d   v1.29.0
```

One control-plane node and two worker nodes, visible directly through `kubectl` — a concrete view
of the architecture this page describes.

## Do / Don't

| Do | Don't |
|---|---|
| Understand `kubectl`/API requests as always going through the control plane, never directly to a node | Assume `kubectl` talks to worker nodes directly |
| Recognize `etcd` as the durable source of truth for cluster state | Assume cluster state lives only on whichever node happens to be running a container |
| Understand worker node failure as recoverable via the control plane's reconciliation loop | Assume a failed node means permanent loss of what it was running |

## Common mistakes

- Assuming `kubectl` commands talk directly to worker nodes, rather than always going through the
  control plane's API server.
- Not understanding that a worker node failing doesn't mean the cluster's state is lost — that
  state lives durably in `etcd`, on the control plane, independent of any specific node.
- Confusing `kubelet` (the per-node agent) with `kubectl` (the user-facing CLI) — similarly named
  but entirely different components with different audiences.

## Best practices

- Understand the request flow (client → API server → scheduler/controller-manager → kubelet →
  runtime) well enough to reason about where a specific failure or delay is occurring.
- Rely on managed control planes (offered by most cloud providers) where operational simplicity
  matters more than full self-management of `etcd`/`kube-apiserver` availability.
- Keep `kubelet` and `kubectl` clearly distinct in your mental model — one is a node agent, the
  other is the user-facing CLI.

## Exercises

1. Run `kubectl get nodes` (or describe what you'd expect to see) and identify which node(s) are
   control-plane versus worker roles.
2. List the four core control-plane components and their roles.
3. Explain what happens to a cluster's recorded state when a single worker node crashes.

## Quiz

**Q: What is etcd's role in a Kubernetes cluster?**
<details><summary>Show answer</summary>
It's the distributed key-value store holding the cluster's entire state — the durable source of
truth the control plane uses to know what should be running.
</details>

**Q: What's the difference between kubelet and kubectl?**
<details><summary>Show answer</summary>
`kubelet` is the per-node agent that ensures containers actually run as the control plane
instructs; `kubectl` is the user-facing command-line tool for interacting with the cluster's API —
different components with different audiences, despite the similar name.
</details>

**Q: Does a worker node's failure destroy the cluster's overall desired-state record?**
<details><summary>Show answer</summary>
No — that state lives durably in `etcd` on the control plane, independent of any individual
worker node, which is exactly why the control plane can reschedule lost work onto other nodes
automatically.
</details>

## Interview questions

- Walk through what happens at the architecture level when a worker node fails in a Kubernetes
  cluster. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- The control plane (`kube-apiserver`, `etcd`, `kube-scheduler`, `kube-controller-manager`) decides
  what should run and where; worker nodes (`kubelet`, container runtime, `kube-proxy`) run it.
- `etcd` is the durable source of truth for cluster state, independent of any specific node.
- A continuous reconciliation loop compares actual state to desired state and corrects
  automatically.
- `kubelet` (node agent) and `kubectl` (user CLI) are distinct components despite the similar name.

## Related topics

- [What Is Kubernetes and Why It Exists](what-is-kubernetes-and-why-it-exists.md)
- [Pods, Deployments, Services](pods-deployments-services.md)
