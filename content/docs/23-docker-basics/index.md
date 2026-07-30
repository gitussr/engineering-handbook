---
title: "Docker Basics"
description: "Module 23 of the Linux roadmap — Docker's architecture, installation, images vs containers, Dockerfiles, the everyday docker commands, networking, volumes, Compose, and production best practices."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
type: "module-index"
nextTopic: "23-docker-basics/docker-architecture"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/docker-basics"
---

# Docker Basics

Module 23 of 34 · Stage: Professional · Previous: [22 Containers](../22-containers/index.md)

Module 22 built the conceptual foundation — what a container is, how it differs from a VM, the
kernel mechanisms behind it. This module makes it hands-on: Docker's client-daemon architecture,
installation, the image/container distinction in practice, writing a Dockerfile, the everyday
commands for running and inspecting containers, networking and persistent storage, Compose for
multi-container setups, and the production habits that tie it all together.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Docker Architecture](docker-architecture.md) | 🟢 Must Know |
| [Installing Docker](installing-docker.md) | 🟢 Must Know |
| [Images vs Containers](images-vs-containers.md) | 🟢 Must Know |
| [Dockerfile Basics](dockerfile-basics.md) | 🟢 Must Know |
| [docker run, ps, exec, logs](docker-run-ps-exec-logs.md) | 🟢 Must Know |
| [Docker Networking Basics](docker-networking-basics.md) | 🟡 Good to Know |
| [Docker Volumes](docker-volumes.md) | 🟡 Good to Know |
| [Docker Compose Intro](docker-compose-intro.md) | 🟡 Good to Know |
| [Docker Best Practices](docker-best-practices.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain Docker's client-daemon-registry architecture and troubleshoot a non-running daemon.
- Install Docker and complete the post-install group-membership step correctly.
- Distinguish images from containers, and explain layer caching and tagging.
- Write a Dockerfile with correctly ordered instructions for build-cache efficiency.
- Use `docker run`, `ps`, `exec`, and `logs` confidently for everyday container work.
- Publish ports and connect containers by name on a user-defined network.
- Persist data beyond a container's lifetime with named volumes or bind mounts.
- Define a multi-container application declaratively with Docker Compose.
- Apply production best practices: pinned tags, non-root users, no baked-in secrets, minimal
  images.

## Known, intentional gaps in this module

- A canonical command page exists for [`docker`](../../commands/docker.md), created during
  [docker run, ps, exec, logs](docker-run-ps-exec-logs.md) and extended across this module's later
  topics (`build`, `network`, `volume`) — one command, one canonical page, the same pattern used
  for `iptables`/`firewall-cmd`/`ufw` in Module 19.
- `docker compose` doesn't get a separate canonical command page — "Docker Compose intro" names a
  concept, not a command in backticks, and its core commands (`up`, `down`, `logs`) are shown
  illustratively on [Docker Compose Intro](docker-compose-intro.md) instead.
- Dockerfile instructions (`FROM`, `RUN`, `COPY`, `CMD`, etc.) are covered in full on
  [Dockerfile Basics](dockerfile-basics.md) but aren't Linux shell commands and don't get canonical
  command pages — they're a build-file syntax, not CLI commands.

**Previous module:** [22 Containers](../22-containers/index.md)
**Next module:** [24 Kubernetes Basics →](../24-kubernetes-basics/index.md)
