---
title: "Containers"
description: "Module 22 of the Linux roadmap — what containerization is, how it differs from virtual machines, the namespaces/cgroups kernel mechanisms behind it, container runtimes, and why it matters for DevOps/Cloud careers."
module: "22-containers"
moduleTitle: "Containers"
stage: "professional"
type: "module-index"
nextTopic: "22-containers/what-is-containerization"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/containers"
---

# Containers

Module 22 of 34 · Stage: Professional · Previous: [21 Performance](../21-performance/index.md)

This module is entirely conceptual — no hands-on commands yet. It builds the mental model
([Module 23: Docker Basics](../23-docker-basics/index.md) provides the hands-on practice) for what
a container actually is, how it genuinely differs from a virtual machine, the Linux kernel
features (namespaces, cgroups) that implement it, the runtime layer underneath tools like Docker,
and why all of this matters concretely for DevOps/Cloud/Platform/SRE careers.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [What Is Containerization](what-is-containerization.md) | 🟢 Must Know |
| [Containers vs VMs](containers-vs-vms.md) | 🟢 Must Know |
| [Namespaces and cgroups Explained](namespaces-and-cgroups-explained.md) | 🔴 Expert |
| [Container Runtimes Overview: containerd, CRI-O](container-runtimes-overview.md) | 🟡 Good to Know |
| [Why Containers Matter for DevOps/Cloud Roles](why-containers-matter-for-devops-cloud-roles.md) | 🟢 Must Know |

## What you should be able to do after this module

- Explain what a container is as a namespaced, cgroup-limited Linux process — not a separate OS.
- Compare containers and VMs on isolation strength, startup time, and resource density, and choose
  correctly between them.
- Explain what namespaces and cgroups each control, and why a container is not a distinct kernel
  object.
- Explain the relationship between Docker's CLI, `containerd`, CRI-O, and Kubernetes' Container
  Runtime Interface.
- Articulate concretely why container fluency matters for DevOps/Cloud/Platform/SRE roles.

## Known, intentional gaps in this module

- No canonical command pages are introduced in this module — every roadmap bullet names a concept,
  not a specific command in backticks. The practical `docker` CLI (and the commands it wraps) is
  the subject of [Module 23: Docker Basics](../23-docker-basics/index.md), which owns those
  canonical pages.
- `containerd` and CRI-O are covered conceptually as runtime implementations, not as commands —
  their own low-level CLIs (`ctr`, `crictl`) are specialized debugging tools outside this
  documentation's scope and don't get canonical pages.
- Namespace and cgroup manipulation is covered conceptually, not as a hands-on skill — this
  documentation teaches container behavior through the `docker` CLI (Module 23), not through direct
  low-level kernel-feature manipulation.

**Previous module:** [21 Performance](../21-performance/index.md)
**Next module:** [23 Docker Basics →](../23-docker-basics/index.md)
