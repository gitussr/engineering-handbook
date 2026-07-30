---
title: "Images vs Containers"
description: "The template-versus-instance distinction from Module 22, made concrete with docker's own layered image format and copy-on-write container filesystem."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/installing-docker"]
relatedTopics: ["installing-docker", "dockerfile-basics"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#docker-image-layers"]
relatedCheatsheet: "docker-basics"
furtherReading: []
nextTopic: "23-docker-basics/dockerfile-basics"
prevTopic: "23-docker-basics/installing-docker"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["docker image layers explained", "image vs container docker", "docker copy on write", "docker image tags"]
canonicalUrl: "/docs/docker-basics/images-vs-containers"
---

# Images vs Containers

🟢 Must Know · Relevant for: DevOps · Cloud · Platform · Software Engineering

> **TL;DR:** An image is a read-only, layered template; a container is a running instance of it
> with a thin writable layer on top. Deleting a container doesn't touch the image it came from —
> the same image can spawn any number of independent containers.

## What is it?

Docker's concrete implementation of the image/container distinction introduced conceptually in
[Module 22](../22-containers/what-is-containerization.md) — a layered, read-only image format, and
the writable container instances built from it.

## Why does it exist?

Rebuilding an entire filesystem from scratch for every container would be wasteful — Docker's
layered image format lets multiple images share common base layers (an OS base layer, a language
runtime layer) and lets many containers share the same underlying image data, each adding only a
thin, container-specific writable layer on top.

## Where is it used?

Every Docker interaction: pulling an image from a registry, building a custom image
([Dockerfile Basics](dockerfile-basics.md), next page), and running any number of independent
containers from that same image.

## How it works

> 📊 Diagram: a stack of horizontal image layers (base OS, language runtime, application code),
> shown read-only and shared across multiple container instances — each container instance drawn
> with its own thin, separate writable layer on top of the same shared read-only stack, illustrating
> that containers don't duplicate the image data, only add a small delta each.

**Images are layered and read-only:**

```
$ docker history nginx
IMAGE          CREATED BY                                      SIZE
a6bd71f48f68   CMD ["nginx" "-g" "daemon off;"]                 0B
<missing>      COPY nginx.conf /etc/nginx/ ...                 1.2kB
<missing>      RUN apt-get install nginx ...                   68MB
<missing>      FROM debian:bookworm-slim                       80MB
```

Each layer is a set of filesystem changes stacked on the one below it; layers are cached and
shared — if two images both start `FROM debian:bookworm-slim`, that base layer is stored once, not
duplicated per image.

**Containers add a thin writable layer:**

```
$ docker run -d --name web1 nginx
$ docker run -d --name web2 nginx
```

Both `web1` and `web2` share the exact same read-only `nginx` image layers underneath, each with
its own separate, thin writable layer for any runtime changes — deleting `web1` never affects
`web2` or the shared `nginx` image itself.

**Tags — identifying a specific image version:**

```
nginx:1.25
nginx:latest
myapp:v2.3.1
```

A tag (after the `:`) identifies a specific image version; `latest` is just a conventional default
tag name, not a special "always current" pointer — a common early misunderstanding.

## Real-world example

A team runs ten containers from the same `myapp:v2.1.0` image across ten servers, then deletes one
misbehaving container to redeploy it fresh — this has zero effect on the image itself or the other
nine running containers, because the deleted container's writable layer is discarded while the
shared, read-only image layers remain completely untouched and available to spawn a new container
immediately. This is precisely the layered architecture's payoff: containers are cheap, disposable
instances; images are the stable, shared foundation.

## Syntax

```
docker images
docker history IMAGE
```

## Commands

See [Docker Run, ps, exec, logs](docker-run-ps-exec-logs.md) (next-but-one page) for the first
canonical `docker` command page, covering `docker images` and `docker run` in full.

## Production example

```
$ docker images
REPOSITORY   TAG       IMAGE ID       SIZE
nginx        1.25      a6bd71f48f68   187MB
myapp        v2.1.0    3f8c9a2b1d44   412MB

$ docker ps -a
CONTAINER ID   IMAGE      STATUS
8f3e21a9c012   nginx:1.25   Up 2 hours
```

Two images stored locally, and one running container built from one of them — the same image can
be reused to start additional containers at any time without re-downloading anything.

## Do / Don't

| Do | Don't |
|---|---|
| Treat `latest` as just a conventional tag name, not a special pointer | Assume `latest` always means "the newest version" automatically |
| Understand deleting a container as safe for the underlying shared image | Worry that removing a container might affect the image or other containers from it |
| Pin specific version tags in production | Rely on `latest` for reproducible production deployments |

## Common mistakes

- Believing `latest` is a special tag that always tracks the newest version, when it's just
  whatever tag was applied without an explicit one specified — it can point to an old, stale image
  if that's what was last tagged `latest`.
- Worrying that deleting a container might delete or corrupt the image it came from, when the two
  are entirely independent — the image persists regardless of any container built from it.
- Not pinning specific version tags in production, causing unpredictable behavior if `latest`
  silently refers to a different image than expected.

## Best practices

- Always pin specific version tags (`myapp:v2.1.0`, not `myapp:latest`) for production deployments,
  for reproducibility.
- Take advantage of layer caching by structuring Dockerfiles (next page) so frequently-changing
  content sits in later layers, minimizing rebuild time.
- Remember that deleting containers is safe and cheap — images are the durable artifact, containers
  are disposable instances of them.

## Exercises

1. Run `docker images` and `docker history` on an image you have locally (or explain what you'd
   expect to see).
2. Explain why two images that share a common base layer don't each store that layer separately on
   disk.
3. Describe why relying on the `latest` tag in production is risky.

## Quiz

**Q: Does deleting a running container affect the image it was created from?**
<details><summary>Show answer</summary>
No — the image's read-only layers are entirely independent of any container instance; deleting a
container only discards its own thin writable layer.
</details>

**Q: Is the latest tag a special pointer that always means "the newest version"?**
<details><summary>Show answer</summary>
No — it's just a conventional tag name applied by default when no explicit tag is given; it can
point to an old image if that's what was most recently tagged `latest`.
</details>

**Q: Why do Docker images use a layered format instead of one flat filesystem snapshot?**
<details><summary>Show answer</summary>
Layers can be shared and cached across multiple images and containers — a common base layer is
stored once, not duplicated per image, saving significant disk space and speeding up builds.
</details>

## Interview questions

- Explain the difference between a Docker image and a Docker container, and why that distinction
  matters. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- An image is a read-only, layered template; a container is a running instance with its own thin
  writable layer.
- Layers are shared and cached across images/containers, saving disk space and build time.
- `latest` is a conventional default tag, not a guaranteed "newest version" pointer — pin specific
  tags in production.
- Deleting a container never affects the image it came from or other containers sharing it.

## Related topics

- [Installing Docker](installing-docker.md)
- [Dockerfile Basics](dockerfile-basics.md)
