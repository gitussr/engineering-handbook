---
title: "Docker Best Practices"
description: "The production-grade habits this module's individual pages each pointed toward — small images, non-root containers, pinned tags, and treating containers as genuinely disposable."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/docker-compose-intro"]
relatedTopics: ["docker-compose-intro", "dockerfile-basics"]
relatedCommands: ["docker"]
careerRelevance: ["devops", "cloud", "platform", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#docker-security-best-practices"]
relatedCheatsheet: "docker-basics"
furtherReading: []
nextTopic: "24-kubernetes-basics/what-is-kubernetes-and-why-it-exists"
prevTopic: "23-docker-basics/docker-compose-intro"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["docker best practices", "docker non root user", "docker minimal base image", "docker image security"]
canonicalUrl: "/docs/docker-basics/docker-best-practices"
---

# Docker Best Practices

🔴 Expert · Relevant for: DevOps · Cloud · Platform · Cybersecurity

> **TL;DR:** Use small, specific base images; pin version tags; run as a non-root user inside the
> container; use volumes for anything persistent; and treat containers as genuinely disposable —
> the applied synthesis of every page in this module.

## What is it?

The production-grade habits this module's individual pages each pointed toward individually — not
new material, but a consolidated checklist applying everything from
[Images vs Containers](images-vs-containers.md) through
[Docker Compose Intro](docker-compose-intro.md) deliberately, together.

## Why does it exist?

Each earlier page in this module taught a mechanism correctly in isolation, but a real production
container setup needs all of them applied consistently and deliberately — this page exists to be
the checklist that ties the module together, the same role
[Server Hardening Checklist](../19-security/server-hardening-checklist.md) played for Module 19.

## Where is it used?

Any container image or setup headed for production — the practical checklist to run through
before trusting a Dockerfile, image, or Compose setup with real traffic or data.

## How it works

> 📊 Diagram: a checklist rendered as a vertical flow, each item with a small icon linking back to
> its owning page in this module (image icon → [Images vs Containers](images-vs-containers.md),
> Dockerfile icon → [Dockerfile Basics](dockerfile-basics.md), lock icon →
> [Module 19: Security](../19-security/index.md)) — reinforcing that best practices apply existing
> knowledge rather than introducing new mechanisms.

**The checklist:**

1. **Use small, specific base images** — a minimal base (`node:20-slim` rather than the full
   `node:20`) reduces image size, build time, and attack surface, building on
   [Dockerfile Basics](dockerfile-basics.md)'s `FROM` instruction.
2. **Pin specific version tags, never `latest`** — for reproducible builds, per
   [Images vs Containers](images-vs-containers.md)'s tagging discussion.
3. **Order Dockerfile instructions for cache efficiency** — least-frequently-changing first, per
   [Dockerfile Basics](dockerfile-basics.md).
4. **Run as a non-root user inside the container** — by default, a container's process runs as
   root unless the Dockerfile explicitly creates and switches to an unprivileged user (`USER`
   instruction); a container compromise while running as root has a much larger blast radius,
   directly connecting to [Module 19's](../19-security/linux-security-model-overview.md) DAC/root
   discussion.
5. **Never bake secrets into an image** — a secret (API key, password) in a Dockerfile `RUN`/`ENV`
   instruction remains in the image's layer history indefinitely, even if later "removed" —
   use environment variables passed at runtime, or a dedicated secrets-management mechanism
   instead.
6. **Use volumes for anything persistent** — never rely on a container's writable layer for data
   that matters, per [Docker Volumes](docker-volumes.md).
7. **Publish only necessary ports** — least-privilege networking, per
   [Docker Networking Basics](docker-networking-basics.md).
8. **Treat containers as genuinely disposable** — design so any container can be killed and
   replaced without data loss or manual recovery steps, the practical foundation
   [Why Containers Matter](../22-containers/why-containers-matter-for-devops-cloud-roles.md)
   depends on.

## Real-world example

A security review of a team's production image finds three issues at once: the Dockerfile bakes a
database password directly into an `ENV` instruction (recoverable from the image's layer history by
anyone who can pull it), the container runs as root with no `USER` instruction, and the base image
is the full, bloated `ubuntu:latest` rather than a minimal, pinned tag. Each individual mechanism
(environment variables, the `USER` instruction, base image selection) was already covered
correctly elsewhere in this module — the review's real finding is that best practices weren't
applied *together*, consistently, which is exactly the gap this page's checklist exists to close.

## Syntax

```dockerfile
FROM node:20-slim
RUN useradd -m appuser
USER appuser
```

## Commands

See [`docker`](../../commands/docker.md) for the commands referenced throughout this checklist —
no new command page is introduced here, since this page applies existing commands deliberately
rather than teaching new ones.

## Production example

```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package.json .
RUN npm install --production
COPY . .
RUN useradd -m appuser && chown -R appuser /app
USER appuser
CMD ["node", "server.js"]
```

A Dockerfile applying several checklist items at once: a slim, pinned base image, cache-efficient
instruction order, and a non-root user created and switched to before the container's main process
starts.

## Do / Don't

| Do | Don't |
|---|---|
| Apply this checklist to every production-bound image, not just security-sensitive ones | Treat best practices as optional polish for "important" images only |
| Pass secrets at runtime (environment variables, secrets managers) | Bake secrets into a Dockerfile `ENV`/`RUN` instruction |
| Create and switch to a non-root user with `USER` | Leave a container running as root by default |

## Common mistakes

- Baking a secret into a Dockerfile instruction, not realizing it remains recoverable from the
  image's layer history even after a later instruction appears to remove it.
- Leaving a container running as root by default (the implicit default if no `USER` instruction is
  given), unnecessarily increasing the blast radius of a potential compromise.
- Applying only some of these practices (a pinned tag, but still running as root) rather than the
  full checklist together.

## Best practices

- Run through this full checklist deliberately for every image headed to production, not just
  ad hoc as issues are noticed.
- Default every Dockerfile to a non-root `USER` instruction unless there's a specific, understood
  reason a container genuinely needs root.
- Pass secrets at runtime only — never through a Dockerfile instruction that becomes part of the
  image's permanent layer history.

## Exercises

1. Take a Dockerfile you've written earlier in this module and check it against every item in
   this checklist.
2. Explain why a "removed" secret in a later Dockerfile instruction is still recoverable from the
   image.
3. Rewrite a Dockerfile to add a non-root `USER` instruction, explaining each step.

## Quiz

**Q: Why is baking a secret into a Dockerfile instruction dangerous, even if a later instruction appears to remove it?**
<details><summary>Show answer</summary>
Docker images are layered, and each instruction's layer is preserved in the image's history — a
secret set in an earlier layer remains recoverable from that layer even if a later layer appears to
delete or overwrite it.
</details>

**Q: What does a container run as by default if the Dockerfile has no USER instruction?**
<details><summary>Show answer</summary>
Root — significantly increasing the blast radius of a potential container compromise compared to
running as an unprivileged user.
</details>

**Q: Why is pinning a specific base image tag considered a best practice?**
<details><summary>Show answer</summary>
It ensures reproducible builds — an unpinned or `latest` base image can change unexpectedly over
time, producing inconsistent results from what was originally tested.
</details>

## Interview questions

- Walk through the security best practices you'd apply when writing a production Dockerfile. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- This page applies every earlier page in this module together: small pinned base images,
  cache-efficient ordering, non-root users, no baked-in secrets, volumes for persistence,
  minimal published ports, and disposable-by-design containers.
- Secrets baked into a Dockerfile instruction remain recoverable from the image's layer history
  indefinitely.
- Containers run as root by default unless a Dockerfile explicitly switches to an unprivileged
  user.
- This page closes Module 23 — Module 24 moves from single-host Docker to Kubernetes-scale
  orchestration.

## Related topics

- [Docker Compose Intro](docker-compose-intro.md)
- [Module 24: Kubernetes Basics](../24-kubernetes-basics/index.md)
