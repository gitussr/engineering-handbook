---
title: "Docker Volumes"
description: "Persisting data beyond a container's lifetime — named volumes versus bind mounts, and why a container's writable layer alone is the wrong place to store anything that matters."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/docker-networking-basics"]
relatedTopics: ["docker-networking-basics", "docker-compose-intro"]
relatedCommands: ["docker"]
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#docker-volume-vs-bind-mount"]
relatedCheatsheet: "docker-basics"
furtherReading: []
nextTopic: "23-docker-basics/docker-compose-intro"
prevTopic: "23-docker-basics/docker-networking-basics"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["docker volume vs bind mount", "docker volume create", "persist data docker container", "docker -v flag"]
canonicalUrl: "/docs/docker-basics/docker-volumes"
---

# Docker Volumes

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** A container's writable layer is deleted along with the container — anything that
> needs to survive (a database's data) belongs in a volume (Docker-managed, portable) or a bind
> mount (a specific host path, less portable but simpler for local development).

## What is it?

Docker's mechanisms for persisting data beyond a single container's lifetime — recalling
[Images vs Containers](images-vs-containers.md)'s point that a container's writable layer is thin
and disposable, volumes and bind mounts are how genuinely important data avoids being tied to that
disposable layer.

## Why does it exist?

A container is meant to be disposable — restarted, replaced, or removed freely
([Why Containers Matter](../22-containers/why-containers-matter-for-devops-cloud-roles.md)'s
rollback example depends on exactly this). But some data (a database's actual records, uploaded
files) must survive that disposability — volumes and bind mounts exist to separate data lifetime
from container lifetime entirely.

## Where is it used?

Any container running a database or anything else with state that must survive a restart,
redeploy, or crash — without a volume, restarting a database container would silently discard
every record it had stored, since the container's own writable layer disappears when it's removed.

## How it works

> 📊 Diagram: a container's disposable writable layer shown being discarded on removal, contrasted
> with a separate, persistent volume (managed by Docker, stored outside any single container's
> lifecycle) that survives that same removal and can be reattached to a brand new container
> instance — plus a third variant, a bind mount, pointing directly at a specific host directory
> instead of a Docker-managed location.

**Named volumes** — Docker-managed storage, the recommended default for persistent application
data:

```bash
docker volume create mydata
docker run -d -v mydata:/var/lib/postgresql/data postgres
```

Docker manages exactly where `mydata` actually lives on the host — portable across different host
setups, and manageable with dedicated `docker volume` commands.

**Bind mounts** — mapping a specific host directory directly into the container:

```bash
docker run -d -v /home/user/app-config:/etc/app/config myapp
```

Simpler for local development (directly editing files on the host and seeing changes reflected
immediately in the container) but tied to that specific host path, making it less portable across
different environments.

**The critical distinction:** without *either* mechanism, any data written inside a container lives
only in its thin writable layer and is permanently lost the moment that container is removed — this
is not a bug, but the intended, disposable-by-default behavior that makes containers replaceable in
the first place.

## Real-world example

A team runs a database in a container without a volume, and after a routine redeploy (removing the
old container and starting a new one from an updated image), discovers every record is gone — the
database's data lived only in the removed container's writable layer, which was discarded exactly
as designed. Adding a named volume mounted at the database's data directory means future redeploys
replace the container while the volume (and its data) persists untouched, attached fresh to
whatever new container starts next.

## Syntax

```
docker volume create NAME
docker run -v NAME:CONTAINER_PATH IMAGE
docker run -v HOST_PATH:CONTAINER_PATH IMAGE
```

## Commands

See [`docker`](../../commands/docker.md) — extended below with `volume` subcommand coverage.

## Production example

```
$ docker volume create pgdata
$ docker run -d --name db -v pgdata:/var/lib/postgresql/data postgres

$ docker rm -f db
$ docker run -d --name db -v pgdata:/var/lib/postgresql/data postgres
```

The container is removed and recreated entirely, but because `pgdata` is a separate, persistent
volume, the database's actual data survives across that removal untouched.

## Do / Don't

| Do | Don't |
|---|---|
| Use a named volume for any data that must survive a container's removal | Store anything important only in a container's writable layer |
| Prefer named volumes for portability across environments | Rely on bind mounts to a specific host path in production, where that exact path may not exist |
| Use bind mounts for local development convenience | Assume a bind mount path is portable to a teammate's different machine setup |

## Common mistakes

- Running a stateful container (a database) with no volume at all, then losing all its data on the
  next routine redeploy or crash.
- Using a bind mount to a specific local path in a production deployment, breaking on any host
  where that exact path doesn't exist.
- Assuming a container's writable layer is a safe place for persistent data, when it's designed to
  be disposable along with the container itself.

## Best practices

- Always attach a named volume to any container storing data that must survive — never rely on
  the container's own writable layer for anything important.
- Prefer named volumes over bind mounts for production data, since Docker manages their location
  portably rather than depending on a specific host path existing.
- Reserve bind mounts for local development convenience, where directly editing host files and
  seeing immediate container-side effects is genuinely useful.

## Exercises

1. Create a named volume, attach it to a container, write data inside it, then remove and recreate
   the container — confirm the data persists.
2. Explain the difference between a named volume and a bind mount.
3. Describe what would happen to a database container's data if it were run with no volume or bind
   mount at all.

## Quiz

**Q: What happens to data written inside a container that has no volume or bind mount attached?**
<details><summary>Show answer</summary>
It's stored only in the container's thin, disposable writable layer and is permanently lost when
that container is removed — this is the intended default behavior, not a bug.
</details>

**Q: What's the main tradeoff of a bind mount compared to a named volume?**
<details><summary>Show answer</summary>
A bind mount is simpler for local development (direct access to a specific host path) but less
portable, since it depends on that exact path existing on whatever host runs the container.
</details>

**Q: Does removing a container also remove its attached named volume?**
<details><summary>Show answer</summary>
No — a named volume's lifetime is independent of any specific container; it persists after the
container is removed and can be reattached to a new container.
</details>

## Interview questions

- Why would a database container lose all its data after a routine redeploy, and how would you fix
  it? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A container's writable layer is disposable — anything that must persist needs a volume or bind
  mount.
- Named volumes are Docker-managed and portable; bind mounts map a specific host path, simpler for
  local development but less portable.
- A volume's lifetime is independent of any specific container — it survives container removal and
  recreation.
- Never store important data only in a container's own writable layer.

## Related topics

- [Docker Networking Basics](docker-networking-basics.md)
- [Docker Compose Intro](docker-compose-intro.md)
