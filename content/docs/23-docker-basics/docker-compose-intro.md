---
title: "Docker Compose Intro"
description: "Defining a multi-container application declaratively in one YAML file instead of chaining individual docker run, network, and volume commands by hand."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/docker-volumes"]
relatedTopics: ["docker-volumes", "docker-best-practices"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#docker-compose-vs-manual-run"]
relatedCheatsheet: "docker-basics"
furtherReading: [{"label": "Docker Compose Documentation", "url": "https://docs.docker.com/compose/"}]
nextTopic: "23-docker-basics/docker-best-practices"
prevTopic: "23-docker-basics/docker-volumes"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["docker compose yaml example", "docker compose up", "docker compose vs docker run", "compose.yaml services"]
canonicalUrl: "/docs/docker-basics/docker-compose-intro"
---

# Docker Compose Intro

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** Docker Compose defines a multi-container application (services, networks, volumes) in
> one declarative YAML file, replacing a manually-typed sequence of `docker run`/`network create`/
> `volume create` commands with `docker compose up`.

## What is it?

A tool for defining and running multi-container applications from a single declarative YAML file,
rather than manually chaining together the individual `docker` commands (networking, volumes,
container startup) covered on the previous few pages.

## Why does it exist?

A realistic application — a web service, a database, maybe a cache — already needs a user-defined
network ([Docker Networking Basics](docker-networking-basics.md)), a volume
([Docker Volumes](docker-volumes.md)), and several coordinated `docker run` commands, each with
several flags. Typing and remembering that exact sequence correctly every time doesn't scale or
survive being shared with a team — Compose exists to make that entire setup declarative,
version-controlled, and reproducible with one command.

## Where is it used?

Local development environments that need multiple coordinated services running together, and
simpler production deployments where a full orchestrator (Kubernetes) would be more complexity
than the workload actually needs.

## How it works

> 📊 Diagram: a single `compose.yaml` file declaring two services (`web`, `db`) with their images,
> ports, volumes, and environment variables, feeding into `docker compose up` — which
> transparently creates a user-defined network, any declared volumes, and starts both containers
> attached correctly to each other, replacing what would otherwise be several manually-typed
> `docker` commands run in the right order.

**A minimal `compose.yaml`:**

```yaml
services:
  web:
    build: .
    ports:
      - "8080:80"
    depends_on:
      - db
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

This single file declares everything the previous few pages covered manually: a build-from-source
service (`web`), an image-based service (`db`), port publishing, a named volume, and an implicit
shared network — Compose automatically creates a user-defined network for services in the same
file, giving them name-based resolution (`web` can reach `db` by name) with no extra configuration.

**The core commands:**

```bash
docker compose up -d       # start every service defined in the file, in the background
docker compose down        # stop and remove every service (volumes persist by default)
docker compose logs -f     # follow logs across all services at once
```

**`depends_on`** controls start *order* only — it does not wait for the dependency to be actually
ready (e.g. for `db` to have finished initializing before `web` starts) — a common point of
confusion that requires the application itself to handle a not-yet-ready dependency gracefully
(commonly a retry loop, the same pattern from [Module 18's](../18-shell-scripting/loops-for-while-until.md)
bounded retry loops).

## Real-world example

A team's application requires a web service, a database, and a specific shared network configured
correctly — previously documented as a multi-step list of manual `docker` commands that a new
team member had to run in the exact right order, prone to being done wrong or getting out of date.
Converting this into a single `compose.yaml` file, checked into version control alongside the
application code, reduces onboarding to one command (`docker compose up`) and ensures the exact
same configuration is used by every team member and in CI, eliminating an entire class of
"someone forgot a flag" setup inconsistency.

## Syntax

```yaml
services:
  SERVICE_NAME:
    image: IMAGE
    ports: ["HOST:CONTAINER"]
    volumes: ["VOLUME:PATH"]
```

## Commands

No canonical command page exists for `docker compose` — it's covered as a concept here rather than
a fully-documented command page, since this roadmap bullet names a concept ("Docker Compose
intro"), not a command in backticks (see this module's [intentional gaps](index.md)); its core
commands (`up`, `down`, `logs`) are shown illustratively above.

## Production example

```
$ docker compose up -d
[+] Running 3/3
 ✔ Network myapp_default   Created
 ✔ Container myapp-db-1    Started
 ✔ Container myapp-web-1   Started

$ docker compose logs -f
```

One command creates the network, starts both services in the correct dependency order, and a
second command follows combined logs across every service simultaneously.

## Do / Don't

| Do | Don't |
|---|---|
| Version-control `compose.yaml` alongside application code | Keep multi-container setup steps only in someone's memory or a wiki page |
| Handle not-yet-ready dependencies with retry logic in the application itself | Assume `depends_on` guarantees a dependency is fully ready, not just started |
| Use Compose for local development and simpler deployments | Reach for Compose when a workload's actual complexity calls for a full orchestrator |

## Common mistakes

- Assuming `depends_on` waits for a dependency to be fully ready, when it only controls start
  order — the application still needs its own readiness handling (e.g. a retry loop against the
  database connection).
- Not version-controlling `compose.yaml`, losing the reproducibility benefit that's the entire
  point of using Compose over manual commands.
- Reaching for a full orchestrator (Kubernetes) for a workload simple enough that Compose alone
  would have been sufficient, adding unnecessary operational complexity.

## Best practices

- Always check `compose.yaml` into version control alongside the application it defines.
- Build in retry/readiness handling at the application level for service dependencies —
  `depends_on` alone isn't sufficient for genuine readiness.
- Reserve Compose for local development and single-host deployments; move to a full orchestrator
  only once genuine multi-host scaling or advanced orchestration needs actually arise.

## Exercises

1. Write a `compose.yaml` for a hypothetical two-service application (a web app and a database).
2. Explain what `depends_on` does and does not guarantee.
3. Describe why version-controlling `compose.yaml` matters for a team, not just an individual
   developer.

## Quiz

**Q: Does depends_on wait for a dependency service to be fully ready before starting the next one?**
<details><summary>Show answer</summary>
No — it only controls start order, not readiness; the application itself needs to handle a
not-yet-ready dependency, commonly with a retry loop.
</details>

**Q: What problem does Docker Compose solve compared to manually running individual docker commands?**
<details><summary>Show answer</summary>
It replaces a manually-typed, error-prone sequence of `docker run`/`network create`/`volume
create` commands with one declarative, version-controllable YAML file and a single command
(`docker compose up`).
</details>

**Q: When might a full orchestrator like Kubernetes be preferred over Docker Compose?**
<details><summary>Show answer</summary>
When a workload genuinely needs multi-host scaling or advanced orchestration features — Compose is
best suited to local development and simpler, single-host deployments.
</details>

## Interview questions

- What problem does Docker Compose solve, and when might you still prefer a full orchestrator? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Docker Compose defines a multi-container application declaratively in one YAML file, replacing
  manually-chained `docker` commands.
- Services on the same Compose file share an automatically-created network with name-based
  resolution.
- `depends_on` controls start order only, not readiness — applications still need their own retry
  logic.
- Compose suits local development and simpler deployments; larger-scale orchestration needs may
  call for Kubernetes instead.

## Related topics

- [Docker Volumes](docker-volumes.md)
- [Docker Best Practices](docker-best-practices.md)
