---
title: "Writing YAML Manifests"
description: "The declarative file format behind every kubectl apply — indentation rules that actually matter, and why apply differs fundamentally from an imperative kubectl create command."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/kubectl-basics"]
relatedTopics: ["kubectl-basics", "namespaces"]
relatedCommands: ["kubectl"]
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#kubectl-apply-vs-create"]
relatedCheatsheet: ""
furtherReading: [{"label": "Kubernetes Object Management", "url": "https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/"}]
nextTopic: "24-kubernetes-basics/namespaces"
prevTopic: "24-kubernetes-basics/kubectl-basics"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["kubernetes yaml manifest example", "kubectl apply vs create", "yaml indentation rules kubernetes", "declarative vs imperative kubernetes"]
canonicalUrl: "/docs/kubernetes-basics/writing-yaml-manifests"
---

# Writing YAML Manifests

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** A manifest is a YAML file declaring an object's desired state. `kubectl apply -f
> FILE` creates it if it doesn't exist or updates it to match if it does — the standard,
> declarative way to manage cluster configuration, version-controlled like any other code.

## What is it?

The YAML file format used to declare Kubernetes objects (the Pods, Deployments, and Services from
[earlier in this module](pods-deployments-services.md)) — and `kubectl apply`, the command that
turns a manifest file into actual cluster state.

## Why does it exist?

Typing out `kubectl create deployment ...` with every flag by hand isn't repeatable, reviewable, or
version-controllable the way a text file is. Manifests make cluster configuration declarative and
durable — exactly the same motivation behind
[Dockerfiles](../23-docker-basics/dockerfile-basics.md) replacing manually-typed build steps, and
[Docker Compose](../23-docker-basics/docker-compose-intro.md) replacing manually-chained `docker
run` commands.

## Where is it used?

Every real Kubernetes deployment — manifests are typically checked into version control alongside
application code, applied via CI/CD or a GitOps tool rather than run ad hoc, per
[kubectl Basics'](kubectl-basics.md) production-usage guidance.

## How it works

> 📊 Diagram: a YAML manifest file with its indentation-based nested structure shown explicitly
> (`spec:` containing indented `containers:`, containing an indented list), with an arrow labeled
> `kubectl apply -f` pointing into the cluster — resulting state shown matching the file's
> declaration exactly, whether that means creating something new or updating something that already
> existed to match.

**YAML indentation rules that actually matter:**

- Indentation is meaningful — it defines nesting, the same way Python's indentation defines code
  blocks. Two spaces per level is conventional (never tabs).
- A list item starts with `-`, at the same indentation level as its sibling items.
- Inconsistent indentation is one of the most common sources of manifest errors — a single
  misaligned line can silently change what's nested under what.

**`kubectl apply` vs. `kubectl create` — declarative vs. imperative:**

```bash
kubectl create deployment myapp --image=myapp:v1.0     # imperative: run this exact command now
kubectl apply -f deployment.yaml                        # declarative: make reality match this file
```

`create` is a one-time imperative action; `apply` is declarative and idempotent — running `apply`
again with an updated file updates the existing object to match, while running `create` again
would simply fail because the object already exists. This is why `apply` (not `create`) is the
standard tool for ongoing, file-based cluster management.

## Real-world example

A team manages their application's Deployment by editing a `deployment.yaml` file and running
`kubectl apply -f deployment.yaml` every time a change is needed — updating the replica count,
image version, or resource limits. Because the file is checked into version control, every change
has a reviewable history and can be rolled back by simply re-applying a previous version of the
file — a workflow that would be impossible to reproduce reliably with a sequence of manually-typed
imperative `kubectl create`/`kubectl edit` commands.

## Syntax

```yaml
apiVersion: API_VERSION
kind: OBJECT_KIND
metadata:
  name: NAME
spec:
  # object-specific fields
```

## Commands

See [`kubectl`](../../commands/kubectl.md) — extended below with `apply` coverage.

## Production example

```
$ kubectl apply -f deployment.yaml
deployment.apps/myapp created

$ kubectl apply -f deployment.yaml
deployment.apps/myapp unchanged

$ vim deployment.yaml   # bump replicas: 3 to replicas: 5
$ kubectl apply -f deployment.yaml
deployment.apps/myapp configured
```

`apply` reports `created`, `unchanged`, or `configured` depending on whether the file's declared
state already matches the cluster — confirming its idempotent, declarative behavior directly.

## Do / Don't

| Do | Don't |
|---|---|
| Manage cluster objects through version-controlled YAML manifests | Rely on one-off imperative `kubectl create`/`edit` commands for ongoing management |
| Use two-space, consistent indentation | Mix tabs and spaces, or use inconsistent indentation levels |
| Use `kubectl apply` for repeatable, idempotent changes | Assume `kubectl create` can be safely re-run to update an existing object |

## Common mistakes

- Mixing tabs and spaces, or inconsistent indentation levels, silently changing what's nested
  under what without an obvious syntax error.
- Using `kubectl create` for ongoing management, then being confused when re-running it fails
  because the object already exists.
- Not version-controlling manifests, losing the reviewable-history and rollback benefits that are
  the entire point of the declarative approach.

## Best practices

- Always version-control manifests alongside application code, the same discipline as
  Dockerfiles and Compose files.
- Use a consistent, linted YAML style (two-space indentation, no tabs) to avoid an entire class of
  subtle structural errors.
- Default to `kubectl apply` for any object that will be managed on an ongoing basis — reserve
  `create` for genuine one-time, throwaway objects.

## Exercises

1. Write a minimal Deployment manifest and apply it (or describe the expected `kubectl apply`
   output) twice in a row.
2. Explain the difference between `kubectl apply` and `kubectl create` in your own words.
3. Describe a YAML indentation mistake that could silently misconfigure a manifest without
   producing an obvious error.

## Quiz

**Q: What's the key difference between kubectl apply and kubectl create?**
<details><summary>Show answer</summary>
`create` is a one-time imperative action that fails if the object already exists; `apply` is
declarative and idempotent, creating the object if it doesn't exist or updating it to match if it
does.
</details>

**Q: Why does YAML indentation matter so much in a manifest?**
<details><summary>Show answer</summary>
Indentation defines nesting structure — an inconsistent or misaligned indentation level can
silently change what's nested under what, without necessarily producing an obvious syntax error.
</details>

**Q: Why is version-controlling manifests considered a best practice?**
<details><summary>Show answer</summary>
It provides a reviewable change history and makes rollback as simple as re-applying a previous
version of the file — benefits that don't exist with one-off imperative commands.
</details>

## Interview questions

- Explain the difference between kubectl apply and kubectl create, and when you'd use each. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A manifest is a YAML file declaring an object's desired state; `kubectl apply -f` is the
  standard way to turn it into cluster reality.
- `apply` is declarative and idempotent; `create` is imperative and one-time only.
- YAML indentation is structurally meaningful — inconsistency is a common, subtle source of errors.
- Version-controlling manifests provides reviewable history and simple rollback.

## Related topics

- [kubectl Basics](kubectl-basics.md)
- [Namespaces](namespaces.md)
