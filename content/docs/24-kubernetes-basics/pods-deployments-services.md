---
title: "Pods, Deployments, Services"
description: "The three objects that make up nearly every Kubernetes application — a Pod as the smallest deployable unit, a Deployment managing replicas of it, and a Service giving it a stable address."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/k8s-architecture-control-plane-and-nodes"]
relatedTopics: ["k8s-architecture-control-plane-and-nodes", "kubectl-basics"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#pod-vs-deployment-vs-service"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "24-kubernetes-basics/kubectl-basics"
prevTopic: "24-kubernetes-basics/k8s-architecture-control-plane-and-nodes"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["kubernetes pod explained", "kubernetes deployment explained", "kubernetes service explained", "pod vs deployment vs service"]
canonicalUrl: "/docs/kubernetes-basics/pods-deployments-services"
---

# Pods, Deployments, Services

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** A Pod wraps one or more containers as the smallest deployable unit. A Deployment
> manages a set of identical Pod replicas, keeping the declared count running. A Service gives that
> ever-changing set of Pods one stable network address, since individual Pods come and go.

## What is it?

The three foundational Kubernetes objects that together implement the desired-state model from
[What Is Kubernetes](what-is-kubernetes-and-why-it-exists.md): a Pod (what actually runs), a
Deployment (how many, and keeping that count correct), and a Service (a stable way to reach them).

## Why does it exist?

A single running container isn't durable — it can die, and something needs to replace it. A fixed
set of individually-managed containers isn't stable — their IP addresses change every time they're
recreated. These three objects layer on top of each other specifically to solve durability (via
Deployments managing Pods) and stable addressing (via Services) — problems that don't exist for a
single-host [Docker](../23-docker-basics/docker-run-ps-exec-logs.md) container the same way.

## Where is it used?

Nearly every application running on Kubernetes uses this same three-object pattern — a Deployment
managing replica Pods of the application, fronted by a Service that gives other parts of the
system (or external traffic) one stable way to reach whichever Pod is currently healthy.

## How it works

> 📊 Diagram: three nested layers — a Service (a stable, fixed network address) at the top, routing
> traffic down to a Deployment's set of Pod replicas (shown as several identical boxes, any of
> which might be replaced/rescheduled over time), each Pod containing one or more containers (the
> Docker-level unit from Module 23) — with an arrow showing a Pod being destroyed and replaced while
> the Service's address never changes.

**Pod** — the smallest deployable unit; wraps one or more tightly-coupled containers that share
network and storage:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
    - name: myapp
      image: myapp:v1.0
```

Pods are rarely created directly in practice — almost always managed indirectly through a
Deployment, which handles replica count and replacement automatically.

**Deployment** — manages a set of identical Pod replicas, keeping the declared count running:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: myapp:v1.0
```

`replicas: 3` is the desired-state declaration from earlier pages, made concrete — the Deployment
continuously ensures exactly 3 matching Pods exist, replacing any that fail.

**Service** — gives a Deployment's ever-changing set of Pods one stable network address:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080
```

The `selector` matches Pods by label (`app: myapp`), routing traffic to whichever of them currently
exist and are healthy — without a Service, other parts of the system would have no stable way to
reach a Deployment's Pods, since individual Pod IPs change every time one is replaced.

## Real-world example

A Deployment declares 3 replicas of a web application. One Pod crashes and is automatically
replaced with a new one carrying a brand-new internal IP address — without a Service in front, every
other part of the system that needed to reach the application would need to somehow discover that
new IP immediately, which doesn't scale. The Service's stable address and label-based routing mean
nothing downstream ever needs to know or care that a specific Pod was replaced — traffic simply
continues reaching whichever healthy Pods currently match the label selector.

## Syntax

```yaml
kind: Pod | Deployment | Service
apiVersion: v1 | apps/v1
metadata:
  name: NAME
spec: { ... }
```

## Commands

No command example on this page — see [kubectl Basics](kubectl-basics.md) (next page) for the
commands that create and manage these objects.

## Production example

```
$ kubectl get pods,deployments,services
NAME                         READY   STATUS    RESTARTS
pod/myapp-7d9f8c6b5d-x2k9p   1/1     Running   0

NAME                     READY   UP-TO-DATE   AVAILABLE
deployment.apps/myapp    3/3     3            3

NAME              TYPE        CLUSTER-IP     PORT(S)
service/myapp     ClusterIP   10.96.45.12    80/TCP
```

All three object types visible together — the Deployment maintaining 3/3 ready Pods, and a Service
providing one stable `CLUSTER-IP` regardless of which specific Pods currently back it.

## Do / Don't

| Do | Don't |
|---|---|
| Manage Pods indirectly through a Deployment | Create standalone Pods directly for anything beyond quick testing |
| Always front a Deployment with a Service for stable addressing | Expect other parts of the system to track individual Pod IPs directly |
| Use label selectors to connect Services to the right Pods | Hardcode a Service's routing to a specific Pod name or IP |

## Common mistakes

- Creating standalone Pods directly for real workloads, losing the automatic replacement/durability
  a Deployment provides.
- Forgetting to create a Service in front of a Deployment, leaving no stable way for other parts of
  the system to reach it as Pods are replaced.
- Misconfiguring a Service's label selector so it doesn't actually match the Deployment's Pods,
  resulting in a Service that routes to nothing.

## Best practices

- Almost always manage Pods through a Deployment, not directly — direct Pod creation is mainly for
  quick, disposable testing.
- Always pair a Deployment with a Service unless the workload genuinely has no need to be reached
  by anything else.
- Double-check that a Service's `selector` labels actually match the Deployment's Pod template
  labels — a mismatch here is a common, hard-to-spot misconfiguration.

## Exercises

1. Write a minimal Deployment manifest for a hypothetical application with 2 replicas.
2. Write a Service manifest that would correctly route to that Deployment's Pods.
3. Explain why a Service is necessary even though a Deployment already ensures the right number of
   Pods are running.

## Quiz

**Q: What is the smallest deployable unit in Kubernetes?**
<details><summary>Show answer</summary>
A Pod — it wraps one or more tightly-coupled containers that share network and storage.
</details>

**Q: Why is a Deployment used instead of creating Pods directly?**
<details><summary>Show answer</summary>
A Deployment continuously ensures the declared number of replica Pods exist, automatically
replacing any that fail — a standalone Pod created directly has no such automatic replacement.
</details>

**Q: Why does a Service exist even though a Deployment already manages Pod replicas?**
<details><summary>Show answer</summary>
Individual Pod IP addresses change every time a Pod is replaced — a Service provides one stable
address and routes to whichever matching Pods currently exist, so nothing downstream needs to
track individual Pod IPs.
</details>

## Interview questions

- Explain the relationship between a Pod, a Deployment, and a Service. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A Pod is the smallest deployable unit, wrapping one or more containers.
- A Deployment manages a set of identical Pod replicas, keeping the declared count running
  automatically.
- A Service gives that changing set of Pods one stable network address via label selectors.
- Together, these three objects implement Kubernetes' desired-state and stable-addressing
  promises.

## Related topics

- [K8s Architecture: Control Plane and Nodes](k8s-architecture-control-plane-and-nodes.md)
- [kubectl Basics](kubectl-basics.md)
