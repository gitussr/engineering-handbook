---
title: "Kubernetes Basics"
description: "Module 24 of the Linux roadmap — what Kubernetes is and why it exists, cluster architecture, Pods/Deployments/Services, kubectl, YAML manifests, Namespaces, ConfigMaps/Secrets, and the Linux fundamentals it all depends on."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
type: "module-index"
nextTopic: "24-kubernetes-basics/what-is-kubernetes-and-why-it-exists"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/kubernetes-basics"
---

# Kubernetes Basics

Module 24 of 34 · Stage: Professional · Previous: [23 Docker Basics](../23-docker-basics/index.md)

Module 23 covered running containers on a single host. This module covers orchestrating them
across a fleet: why Kubernetes exists, its control-plane/node architecture, the Pod/Deployment/
Service objects nearly every application uses, `kubectl` and YAML manifests for managing them,
Namespaces for organizing a shared cluster, ConfigMaps/Secrets for configuration — closing by
mapping every one of these concepts back to the Linux fundamentals this entire roadmap has built.
This module closes the Professional stage.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [What Is Kubernetes and Why It Exists](what-is-kubernetes-and-why-it-exists.md) | 🟢 Must Know |
| [K8s Architecture: Control Plane and Nodes](k8s-architecture-control-plane-and-nodes.md) | 🟡 Good to Know |
| [Pods, Deployments, Services](pods-deployments-services.md) | 🟡 Good to Know |
| [kubectl Basics](kubectl-basics.md) | 🟡 Good to Know |
| [Writing YAML Manifests](writing-yaml-manifests.md) | 🟡 Good to Know |
| [Namespaces (Kubernetes)](namespaces.md) | 🟡 Good to Know |
| [ConfigMaps and Secrets Intro](configmaps-and-secrets-intro.md) | 🔴 Expert |
| [Which Linux Skills K8s Actually Depends On](which-linux-skills-k8s-depends-on.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain what problem Kubernetes solves that Docker/Compose alone don't, and its core
  desired-state model.
- Describe the control-plane/node architecture and where `etcd`, the scheduler, and `kubelet` each
  fit.
- Explain how Pods, Deployments, and Services work together, and why each exists.
- Use `kubectl get`/`describe`/`logs`/`apply`/`delete` confidently, escalating to `describe`'s
  Events section when troubleshooting.
- Write a basic YAML manifest and explain `apply` vs. `create`.
- Use Namespaces to organize a cluster, and explain why they're unrelated to Linux kernel
  namespaces despite the shared name.
- Explain ConfigMaps vs. Secrets, and why a Secret isn't encrypted by default.
- Map Kubernetes failure modes and concepts back to the specific Linux fundamentals underneath
  them.

## Known, intentional gaps in this module

- A canonical command page exists for [`kubectl`](../../commands/kubectl.md), created during
  [kubectl Basics](kubectl-basics.md) and extended across this module's later topics (`apply`,
  Namespace scoping, `create configmap`/`create secret`) — the same one-command-one-page pattern
  used for `docker` in Module 23.
- No separate command pages exist for `kube-apiserver`, `etcd`, `kube-scheduler`,
  `kube-controller-manager`, or `kubelet` — these are control-plane/node system components
  administered as part of cluster setup, not everyday interactive commands; this documentation
  teaches cluster interaction through `kubectl` exclusively.
- Dockerfile-style build tooling and Helm (a common package manager for Kubernetes manifests) are
  outside this module's scope — this module teaches raw YAML manifests and `kubectl` directly, per
  the roadmap's explicit bullets.

**Previous module:** [23 Docker Basics](../23-docker-basics/index.md)
**Next module:** [25 Linux for Cloud →](../25-linux-for-cloud/index.md)
