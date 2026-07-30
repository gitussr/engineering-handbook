---
title: "kubectl — Control a Kubernetes Cluster"
description: "The command-line client for a cluster's API server — listing, inspecting, and deleting objects, and reading logs from Pod containers."
relatedConcepts: ["24-kubernetes-basics/kubectl-basics", "24-kubernetes-basics/pods-deployments-services", "24-kubernetes-basics/k8s-architecture-control-plane-and-nodes", "24-kubernetes-basics/writing-yaml-manifests", "24-kubernetes-basics/namespaces", "24-kubernetes-basics/configmaps-and-secrets-intro"]
relatedCommands: ["docker"]
careerRelevance: ["devops", "cloud", "platform"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["kubectl command", "kubectl get pods", "kubectl describe", "kubectl logs", "kubectl delete", "kubectl apply -f", "kubectl -n namespace", "kubectl create secret"]
canonicalUrl: "/commands/kubectl"
---

# kubectl

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** `kubectl get pods` lists Pods; `kubectl describe pod NAME` shows full detail
> including recent events; `kubectl logs [-f] NAME` reads container output; `kubectl delete`
> removes an object — every command talks to the control plane's API server, never directly to a
> node.

## Purpose

`kubectl` is the CLI for interacting with a Kubernetes cluster's API server — see
[kubectl Basics](../docs/24-kubernetes-basics/kubectl-basics.md) for the full concept.

## Syntax

```
kubectl COMMAND [OBJECT_TYPE] [NAME] [OPTIONS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `OBJECT_TYPE` | The kind of object (`pod`, `deployment`, `service`, `configmap`, `secret`, etc.) | Yes, for most commands |
| `NAME` | A specific object's name | Yes, for `describe`/`logs`/`delete`; optional for `get` (omit to list all) |

## Options

| Command | Flag | Meaning |
|---|---|---|
| `get` | `-o wide` | Show additional detail (e.g. which node a Pod is on) |
| `logs` | `-f` | Follow output continuously, like `tail -f` |
| `apply` | `-f FILE` | Create or update objects declaratively from a YAML manifest — idempotent, unlike `create` |
| `create` | `-f FILE` | Create objects from a manifest — fails if they already exist |
| any | `-n NAMESPACE` | Scope the command to a specific Namespace instead of the default |
| `get` | `--all-namespaces` | List across every Namespace at once |
| `create` | `namespace NAME` | Create a new Namespace |
| `create` | `configmap NAME --from-literal=KEY=VALUE` | Create a ConfigMap from a literal key-value pair |
| `create` | `secret generic NAME --from-literal=KEY=VALUE` | Create a Secret from a literal key-value pair |
| `get` | `secret NAME -o jsonpath='{.data.KEY}'` | Read a Secret's stored (base64-encoded) value |

## Examples

```
$ kubectl get pods
```
List Pods in the current namespace.

```
$ kubectl describe pod myapp-7d9f8c6b5d-x2k9p
```
Show full detail, including the Events section, for a specific Pod.

```
$ kubectl logs -f myapp-7d9f8c6b5d-x2k9p
```
Follow a Pod's container output continuously.

```
$ kubectl delete pod myapp-7d9f8c6b5d-x2k9p
```
Delete a specific Pod (a Deployment-managed Pod will typically be recreated automatically).

```
$ kubectl apply -f deployment.yaml
```
Create or update objects declaratively from a manifest file — see
[Writing YAML Manifests](../docs/24-kubernetes-basics/writing-yaml-manifests.md).

```
$ kubectl get pods -n staging
```
List Pods scoped to a specific Namespace — see
[Namespaces](../docs/24-kubernetes-basics/namespaces.md).

```
$ kubectl create secret generic db-creds --from-literal=password=hunter2
$ kubectl get secret db-creds -o jsonpath='{.data.password}' | base64 -d
```
Create a Secret, then decode its stored value — demonstrating it's only base64-encoded, not
encrypted, by default. See
[ConfigMaps and Secrets Intro](../docs/24-kubernetes-basics/configmaps-and-secrets-intro.md).

## Expected Output

```
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-7d9f8c6b5d-x2k9p   1/1     Running   0          2d
```

## Exit Status

`0` on success, non-zero if the API server is unreachable, the object doesn't exist, or the
manifest/command syntax is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `The connection to the server ... was refused` | `kubectl` can't reach the cluster's API server (wrong context, cluster unreachable) | Confirm `kubectl config current-context` points to the intended cluster |
| `Error from server (NotFound)` | Wrong object name, or looking in the wrong Namespace | Confirm the name with `kubectl get`, and add `-n NAMESPACE` if needed |
| Pod stuck in `Pending` | Often insufficient cluster resources for scheduling | Check `kubectl describe pod`'s Events section for the scheduler's specific reason |

## Security Considerations

`kubectl`'s access is governed by the cluster's RBAC (role-based access control) configuration —
treat `kubectl` credentials/contexts with the same care as any other privileged access, since a
misconfigured or overly broad role can grant far more cluster access than intended. Restrict
`get secret` access tightly — a Secret's stored value is only base64-encoded by default, not
encrypted, and trivially decodable by anyone who can read it (see
[ConfigMaps and Secrets Intro](../docs/24-kubernetes-basics/configmaps-and-secrets-intro.md)).

## Performance Considerations

`kubectl get`/`describe`/`logs` are lightweight read operations; `kubectl logs -f` on a
high-output container can generate significant traffic if left running unattended, the same
consideration as `docker logs -f`.

## Production Usage

Production teams typically avoid running ad hoc, mutating `kubectl` commands directly against a
live cluster — configuration changes are usually version-controlled and applied through a CI/CD
pipeline or a GitOps tool, with direct `kubectl` use reserved for inspection (`get`, `describe`,
`logs`) and deliberate, controlled manual intervention.

## Related Commands

- [`docker`](docker.md) — the container-level equivalent (`docker ps`/`logs`/`exec`); `kubectl`
  operates one layer up, at the Pod/cluster level

## Related Concepts

- [kubectl Basics](../docs/24-kubernetes-basics/kubectl-basics.md)
- [Pods, Deployments, Services](../docs/24-kubernetes-basics/pods-deployments-services.md)
- [K8s Architecture: Control Plane and Nodes](../docs/24-kubernetes-basics/k8s-architecture-control-plane-and-nodes.md)
- [Writing YAML Manifests](../docs/24-kubernetes-basics/writing-yaml-manifests.md)
- [Namespaces](../docs/24-kubernetes-basics/namespaces.md)
- [ConfigMaps and Secrets Intro](../docs/24-kubernetes-basics/configmaps-and-secrets-intro.md)
