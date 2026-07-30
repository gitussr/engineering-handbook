---
title: "kubectl Basics"
description: "The command-line client for talking to a cluster's API server — getting, describing, and deleting objects, and reading logs from inside a Pod's containers."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/pods-deployments-services"]
relatedTopics: ["pods-deployments-services", "writing-yaml-manifests"]
relatedCommands: ["kubectl"]
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#kubectl-get-vs-describe"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "24-kubernetes-basics/writing-yaml-manifests"
prevTopic: "24-kubernetes-basics/pods-deployments-services"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["kubectl get pods", "kubectl describe", "kubectl logs", "kubectl basics examples"]
canonicalUrl: "/docs/kubernetes-basics/kubectl-basics"
---

# kubectl Basics

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** `kubectl get` lists objects, `kubectl describe` shows full detail (including recent
> events — the first place to look when something's wrong), `kubectl logs` reads a Pod's
> container output, and `kubectl delete` removes an object — always talking to the control plane's
> API server, never directly to a node.

## What is it?

The command-line client for interacting with a Kubernetes cluster — sending every request through
the control plane's API server, as established in
[K8s Architecture](k8s-architecture-control-plane-and-nodes.md).

## Why does it exist?

Every interaction with a cluster — checking what's running, investigating a problem, making a
change — needs a consistent interface to the API server. `kubectl` is that interface, the
practical tool this entire module has been building toward.

## Where is it used?

Every day-to-day Kubernetes task: checking cluster state, investigating a failing Pod, reading
logs, and applying configuration changes ([Writing YAML Manifests](writing-yaml-manifests.md),
next page).

## How it works

> 📊 Diagram: `kubectl` sending a request to the API server (never directly to a node or
> `kubelet`), with four labeled command flows branching out — `get` (list), `describe` (detail +
> events), `logs` (container output via the node's `kubelet`), `delete` (remove) — each ultimately
> resolving through the same control plane path from
> [K8s Architecture](k8s-architecture-control-plane-and-nodes.md).

**`kubectl get`** — list objects:

```bash
kubectl get pods
kubectl get deployments
kubectl get pods -o wide     # more detail, including which node each Pod is on
```

**`kubectl describe`** — full detail on a specific object, including recent events — the first
place to look when something isn't behaving as expected:

```bash
kubectl describe pod myapp-7d9f8c6b5d-x2k9p
```

The **Events** section at the bottom of `describe`'s output is often the single most useful part —
it shows recent scheduling decisions, pull failures, restart reasons, and other operational history
that `get` alone never surfaces.

**`kubectl logs`** — read a Pod's container output, the same investigative instinct from
[Module 16](../16-logs/index.md) and [Docker's `logs`](../23-docker-basics/docker-run-ps-exec-logs.md),
applied at the Kubernetes level:

```bash
kubectl logs myapp-7d9f8c6b5d-x2k9p
kubectl logs -f myapp-7d9f8c6b5d-x2k9p    # follow continuously
```

**`kubectl delete`** — remove an object (a Deployment will typically recreate deleted Pods
automatically, since that's exactly the reconciliation loop from
[K8s Architecture](k8s-architecture-control-plane-and-nodes.md) doing its job):

```bash
kubectl delete pod myapp-7d9f8c6b5d-x2k9p
```

## Real-world example

A Pod is stuck in a `Pending` state, never actually starting. `kubectl get pods` shows the status
but not *why*; `kubectl describe pod` reveals, in its Events section, that the scheduler couldn't
find a node with enough available memory to place it — information invisible from `get` alone.
This escalation (`get` to notice something's wrong, `describe` to find out why) mirrors the same
"confirm, then investigate" discipline from [Module 21's](../21-performance/performance-tuning-overview.md)
measure-first approach, applied to Kubernetes troubleshooting specifically.

## Syntax

```
kubectl get OBJECT_TYPE [NAME]
kubectl describe OBJECT_TYPE NAME
kubectl logs [-f] POD_NAME
kubectl delete OBJECT_TYPE NAME
```

## Commands

See [`kubectl`](../../commands/kubectl.md) for the full canonical reference.

## Production example

```
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-7d9f8c6b5d-x2k9p   0/1     Pending   0          2m

$ kubectl describe pod myapp-7d9f8c6b5d-x2k9p
...
Events:
  Type     Reason            Message
  ----     ------            -------
  Warning  FailedScheduling  0/3 nodes are available: insufficient memory
```

`get` shows the symptom (`Pending`); `describe`'s Events section reveals the actual cause —
exactly the escalation pattern this page teaches.

## Do / Don't

| Do | Don't |
|---|---|
| Escalate from `get` to `describe` when something looks wrong | Stop investigating at `get`'s summary status alone |
| Check `describe`'s Events section first when troubleshooting | Overlook Events, the section most likely to explain a problem |
| Use `kubectl logs -f` for live investigation | Assume `kubectl logs` without `-f` shows live, ongoing output |

## Common mistakes

- Stopping at `kubectl get`'s brief status output without escalating to `describe` when something
  needs actual investigation.
- Overlooking the Events section in `describe`'s output, which is often exactly where the real
  explanation lives.
- Deleting a Pod expecting it to be gone permanently, without realizing a Deployment will simply
  recreate it — a related object, not an accident, needs to be modified for a lasting change.

## Best practices

- Reach for `describe` immediately once `get` shows something isn't in the expected state — it's
  almost always the next step, not an optional deep-dive.
- Read the Events section of `describe`'s output first — it's frequently the fastest path to root
  cause.
- Remember that deleting a Pod managed by a Deployment triggers automatic recreation — to make a
  lasting change, modify the Deployment, not just its current Pods.

## Exercises

1. Run `kubectl get pods` and `kubectl describe pod` on the same Pod (or describe what you'd
   expect) and compare the information each provides.
2. Explain why the Events section of `describe`'s output is often the most useful part.
3. Describe what happens if you delete a Pod that's managed by a Deployment.

## Quiz

**Q: What's the standard escalation when kubectl get shows something isn't behaving as expected?**
<details><summary>Show answer</summary>
Run `kubectl describe` on that object — it shows full detail including the Events section, which
`get`'s brief summary never surfaces.
</details>

**Q: What happens if you delete a Pod that a Deployment manages?**
<details><summary>Show answer</summary>
The Deployment's reconciliation loop detects the missing replica and automatically creates a
replacement — the deletion doesn't produce a lasting change unless the Deployment itself is
modified.
</details>

**Q: Where in kubectl describe's output would you look first to explain unexpected Pod behavior?**
<details><summary>Show answer</summary>
The Events section — it shows recent scheduling decisions, failures, and restart reasons that
directly explain most unexpected states.
</details>

## Interview questions

- A Pod is stuck in Pending — walk through how you'd investigate why. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `kubectl get` lists objects briefly; `kubectl describe` gives full detail, including the
  often-critical Events section.
- `kubectl logs [-f]` reads container output, the same investigative instinct as `docker logs` or
  `journalctl`.
- Deleting a Deployment-managed Pod triggers automatic recreation — modify the Deployment for a
  lasting change.
- All `kubectl` commands go through the control plane's API server, never directly to a node.

## Related topics

- [Pods, Deployments, Services](pods-deployments-services.md)
- [Writing YAML Manifests](writing-yaml-manifests.md)
