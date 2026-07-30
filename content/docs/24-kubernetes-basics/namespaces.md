---
title: "Namespaces (Kubernetes)"
description: "A logical partition within a single cluster for organizing objects by team or environment — a different concept from the Linux kernel namespaces covered in Module 22, despite the shared name."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/writing-yaml-manifests"]
relatedTopics: ["writing-yaml-manifests", "configmaps-and-secrets-intro"]
relatedCommands: ["kubectl"]
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#k8s-namespace-vs-linux-namespace"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "24-kubernetes-basics/configmaps-and-secrets-intro"
prevTopic: "24-kubernetes-basics/writing-yaml-manifests"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["kubernetes namespace explained", "kubernetes namespace vs linux namespace", "kubectl -n flag", "kubernetes default namespace"]
canonicalUrl: "/docs/kubernetes-basics/namespaces"
---

# Namespaces (Kubernetes)

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** A Kubernetes Namespace is a logical partition *within one cluster* — for organizing
> objects by team or environment (`staging`, `production`). This is a completely different concept
> from the Linux kernel namespaces from [Module 22](../22-containers/namespaces-and-cgroups-explained.md),
> despite sharing the same name.

## What is it?

A way to divide a single Kubernetes cluster into multiple logical partitions, each with its own
set of objects (Pods, Deployments, Services) that can share the same names without colliding across
partitions.

## Why does it exist?

A single cluster is often shared across multiple teams or environments (`staging`, `production`,
a specific team's workloads) — without some way to partition it logically, every object name would
need to be globally unique across the entire cluster, and access control couldn't be scoped to just
one team's or environment's resources.

## Where is it used?

Multi-team clusters (giving each team its own Namespace to avoid naming collisions and scope
access control), and separating environments (`staging` vs. `production`) within the same
physical cluster rather than requiring entirely separate clusters for each.

## How it works

> 📊 Diagram: one physical cluster shown divided into several logical Namespace partitions
> (`default`, `staging`, `production`), each containing its own independently-named Pods/
> Deployments/Services — with a clearly separate, smaller diagram alongside it showing Module 22's
> Linux kernel namespaces (PID, network, mount) isolating a single process's view of the host —
> explicitly labeled as two unrelated concepts that happen to share an English word.

**⚠️ Namespace name collision — this is genuinely confusing, so it's worth stating directly:**
Kubernetes "Namespaces" and Linux kernel "namespaces"
([Module 22](../22-containers/namespaces-and-cgroups-explained.md)) share a name but are otherwise
unrelated:

| | Kubernetes Namespace | Linux namespace |
|---|---|---|
| Scope | Logical partition within a cluster | Kernel-level isolation for a single process |
| Purpose | Organize objects, scope access control | Isolate what a process can see (PID, network, mount, etc.) |
| Level | Kubernetes API concept | Kernel feature, underlying container isolation itself |

A Kubernetes Namespace doesn't provide process isolation at all — that isolation is what Pods
already get from the Linux namespaces underlying their containers, entirely independent of which
Kubernetes Namespace they're organized under.

**Working with Namespaces:**

```bash
kubectl get namespaces
kubectl create namespace staging
kubectl get pods -n staging          # scope a command to a specific namespace
kubectl get pods --all-namespaces    # across every namespace
```

Every cluster has a `default` Namespace; objects created without specifying one land there
automatically.

## Real-world example

A platform team gives each of three product teams their own Namespace (`team-a`, `team-b`,
`team-c`) within one shared cluster — each team can name their Deployments and Services however
they like (`web`, `api`, `db`) without any risk of colliding with another team's identically-named
objects, since Namespace membership scopes the name uniqueness requirement. Access control
(RBAC) is also scoped per Namespace, so `team-a`'s engineers can't accidentally (or deliberately)
modify `team-b`'s resources.

## Syntax

```
kubectl get namespaces
kubectl create namespace NAME
kubectl get OBJECT_TYPE -n NAMESPACE
```

## Commands

See [`kubectl`](../../commands/kubectl.md) — extended below with namespace-scoping coverage.

## Production example

```
$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   90d
kube-system       Active   90d
staging           Active   45d
production        Active   45d

$ kubectl get pods -n production
NAME                     READY   STATUS
myapp-7d9f8c6b5d-x2k9p   1/1     Running
```

Multiple Namespaces coexisting in one cluster, each scoped independently — `kube-system` (used by
Kubernetes itself) alongside two application environments.

## Do / Don't

| Do | Don't |
|---|---|
| Use Namespaces to organize a shared cluster by team or environment | Assume a Kubernetes Namespace provides process-level isolation |
| Always specify `-n` (or check the current context's default) when working across multiple Namespaces | Assume `kubectl get pods` without `-n` shows every Namespace's Pods |
| Keep "Kubernetes Namespace" and "Linux namespace" mentally distinct | Conflate the two concepts just because they share a name |

## Common mistakes

- Assuming a Kubernetes Namespace provides the same process-isolation guarantees as a Linux kernel
  namespace, when it's purely an organizational/access-control boundary at the API level.
- Forgetting `-n NAMESPACE` and being confused why an object "doesn't exist," when it actually
  exists in a different Namespace than the one being checked by default.
- Conflating the two "namespace" concepts in conversation or documentation, causing genuine
  confusion for someone trying to learn either one.

## Best practices

- Use Namespaces deliberately to organize a shared cluster — by team, by environment, or both.
- Always be explicit about which Namespace a command targets, either via `-n` or by confirming the
  current context's default Namespace.
- When explaining this concept to someone new, state the Linux-namespace naming collision
  explicitly, the way this page does — it's a common, avoidable source of confusion otherwise.

## Exercises

1. List the Namespaces in a cluster you have access to (or describe what you'd expect to see,
   including `kube-system`).
2. Explain, in one sentence, why Kubernetes Namespaces and Linux namespaces are unrelated concepts
   despite the shared name.
3. Create a Namespace and a Pod within it, then confirm the Pod isn't visible without specifying
   that Namespace.

## Quiz

**Q: Does a Kubernetes Namespace provide the same isolation as a Linux kernel namespace?**
<details><summary>Show answer</summary>
No — a Kubernetes Namespace is purely an organizational and access-control partition within the
API; process-level isolation comes from Linux kernel namespaces (Module 22), entirely independent
of which Kubernetes Namespace a Pod is organized under.
</details>

**Q: What happens to an object created without specifying a Namespace?**
<details><summary>Show answer</summary>
It's created in the `default` Namespace, which every cluster has automatically.
</details>

**Q: Why might kubectl get pods appear to show no results even though a Pod exists?**
<details><summary>Show answer</summary>
The Pod may exist in a different Namespace than the one being checked by default — `-n
NAMESPACE` or `--all-namespaces` is needed to see objects outside the current default.
</details>

## Interview questions

- Explain the difference between a Kubernetes Namespace and a Linux namespace. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A Kubernetes Namespace is a logical partition within one cluster, for organizing objects and
  scoping access control by team or environment.
- This is unrelated to Linux kernel namespaces (Module 22), despite sharing the same name — a
  common source of confusion worth stating explicitly.
- Every cluster has a `default` Namespace; objects land there unless another is specified.
- Always be explicit about which Namespace a command targets.

## Related topics

- [Writing YAML Manifests](writing-yaml-manifests.md)
- [ConfigMaps and Secrets Intro](configmaps-and-secrets-intro.md)
