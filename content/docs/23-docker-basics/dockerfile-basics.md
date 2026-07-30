---
title: "Dockerfile Basics"
description: "The instruction set for building a custom image — FROM, RUN, COPY, CMD, and the layer-caching behavior that makes instruction order matter for build speed."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/images-vs-containers"]
relatedTopics: ["images-vs-containers", "docker-run-ps-exec-logs"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#dockerfile-instructions"]
relatedCheatsheet: "docker-basics"
furtherReading: [{"label": "Dockerfile Reference", "url": "https://docs.docker.com/engine/reference/builder/"}]
nextTopic: "23-docker-basics/docker-run-ps-exec-logs"
prevTopic: "23-docker-basics/images-vs-containers"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["dockerfile instructions explained", "dockerfile from run copy cmd", "dockerfile layer caching", "dockerfile example"]
canonicalUrl: "/docs/docker-basics/dockerfile-basics"
---

# Dockerfile Basics

🟢 Must Know · Relevant for: DevOps · Cloud · Platform · Software Engineering

> **TL;DR:** A `Dockerfile` is a plain-text recipe for building a custom image — `FROM` sets the
> base, `RUN` executes build-time commands, `COPY` adds files, `CMD` sets the default startup
> command. Each instruction creates a new, cached layer — order them from least to most frequently
> changing.

## What is it?

A plain-text file of instructions that `docker build` reads to construct a custom image, layer by
layer, on top of a base image.

## Why does it exist?

Manually creating a custom image by running a container, modifying it interactively, and saving the
result would be slow, unrepeatable, and impossible to version-control meaningfully. A `Dockerfile`
makes image construction declarative, repeatable, and reviewable as ordinary text — every image
build starts from the exact same instructions, every time.

## Where is it used?

Every custom application image — turning "this is our application plus its dependencies" into a
reproducible, buildable artifact rather than a manually-assembled one, feeding directly into the
image/container model from the [previous page](images-vs-containers.md).

## How it works

> 📊 Diagram: a Dockerfile shown as a vertical sequence of instructions (`FROM`, `RUN`, `COPY`,
> `CMD`), each one producing a labeled, stacked image layer directly below it — with a callout
> showing that if an early instruction's layer is unchanged since the last build, Docker reuses the
> cached layer instead of re-executing it, skipping straight to the first genuinely changed
> instruction.

**Core instructions:**

| Instruction | Purpose |
|---|---|
| `FROM` | Sets the base image everything else builds on top of — always the first instruction |
| `RUN` | Executes a command at build time (e.g. installing packages), creating a new layer |
| `COPY` | Copies files from the build context into the image |
| `WORKDIR` | Sets the working directory for subsequent instructions |
| `EXPOSE` | Documents which port(s) the container listens on (informational — doesn't actually publish the port; see [Docker Networking Basics](docker-networking-basics.md)) |
| `CMD` | The default command run when a container starts from this image (overridable at `docker run` time) |

**A minimal example:**

```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["node", "server.js"]
```

**Layer caching — why instruction order matters:** each instruction produces a cached layer; if a
layer's inputs haven't changed since the last build, Docker reuses the cached result instead of
re-executing it. Ordering instructions from least-frequently-changing (base image, dependency
installation) to most-frequently-changing (application source code) means routine code changes only
invalidate the cache from that point forward, not the entire build — exactly why the example above
copies `package.json` and runs `npm install` *before* copying the rest of the application source.

## Real-world example

A team's Dockerfile copies the entire application source code before installing dependencies,
meaning every single code change — even a one-line fix — invalidates the dependency-installation
layer's cache, forcing a full, slow reinstall on every build. Reordering the Dockerfile to copy only
the dependency manifest first, install dependencies, and *then* copy the rest of the source code
(as in the example above) means routine code changes reuse the cached dependency layer entirely,
cutting build time dramatically without changing what the image actually contains.

## Syntax

```dockerfile
FROM IMAGE[:TAG]
RUN COMMAND
COPY SRC DEST
CMD ["executable", "arg1", "arg2"]
```

## Commands

No canonical command page exists yet on this page — `docker build` (which reads a `Dockerfile` and
produces an image) is illustrated below; see
[Docker Run, ps, exec, logs](docker-run-ps-exec-logs.md) (next page) for the first canonical
`docker` command reference, which also covers `build`.

## Production example

```
$ docker build -t myapp:v1.0 .
[+] Building 12.4s
 => [1/5] FROM docker.io/library/node:20-slim
 => [2/5] WORKDIR /app
 => [3/5] COPY package.json .
 => [4/5] RUN npm install
 => [5/5] COPY . .
 => exporting to image
 => naming to docker.io/library/myapp:v1.0
```

`docker build -t myapp:v1.0 .` reads the `Dockerfile` in the current directory (`.`) and tags the
resulting image `myapp:v1.0` — the practical command that turns this page's instructions into a
real, runnable image.

## Do / Don't

| Do | Don't |
|---|---|
| Order instructions from least to most frequently changing | Copy the entire application source before installing dependencies |
| Use a specific base image tag (`node:20-slim`), not an untagged/`latest` base | Build from an unpinned base image and get unpredictable results over time |
| Keep `Dockerfile`s minimal and purpose-built per application | Reuse one bloated, generic Dockerfile across unrelated applications |

## Common mistakes

- Copying application source code before installing dependencies, invalidating the
  dependency-install cache layer on every single code change.
- Using an untagged or `latest` base image, producing inconsistent builds over time as the
  underlying base image changes without warning.
- Running unnecessary commands in separate `RUN` instructions when they could be combined,
  creating more layers than needed and increasing final image size.

## Best practices

- Order instructions deliberately: base image and dependency installation first, application code
  last, to maximize cache reuse on routine changes.
- Pin a specific base image tag rather than relying on `latest`, for reproducible builds.
- Keep a `.dockerignore` file (analogous to `.gitignore`) to exclude files that shouldn't be copied
  into the build context, keeping builds fast and images lean.

## Exercises

1. Write a minimal Dockerfile for a hypothetical Python application (`FROM`, `WORKDIR`, `COPY`,
   `RUN`, `CMD`).
2. Explain why copying a dependency manifest before the rest of the source code improves build
   caching.
3. Describe what `EXPOSE` does and does not do (hint: it's informational, not the same as actually
   publishing a port).

## Quiz

**Q: What does the FROM instruction do, and where must it appear in a Dockerfile?**
<details><summary>Show answer</summary>
It sets the base image everything else builds on top of, and must be the first instruction in the
Dockerfile.
</details>

**Q: Why does instruction order affect build speed?**
<details><summary>Show answer</summary>
Each instruction's layer is cached; if an instruction's inputs are unchanged since the last build,
Docker reuses the cached layer instead of re-executing it — ordering frequently-changing
instructions last maximizes how much of the build can reuse the cache.
</details>

**Q: Does EXPOSE actually make a container's port reachable from outside?**
<details><summary>Show answer</summary>
No — it's informational documentation within the image; actually publishing a port to the host
requires an explicit flag at `docker run` time (covered in Docker Networking Basics).
</details>

## Interview questions

- Why does the order of instructions in a Dockerfile matter for build performance? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A Dockerfile is a declarative, repeatable recipe for building a custom image, read by
  `docker build`.
- Core instructions: `FROM` (base), `RUN` (build-time commands), `COPY` (add files), `CMD` (default
  startup command).
- Layer caching means instruction order directly affects build speed — least-frequently-changing
  instructions first.
- `EXPOSE` is documentation only — it doesn't itself publish a port to the host.

## Related topics

- [Images vs Containers](images-vs-containers.md)
- [Docker Run, ps, exec, logs](docker-run-ps-exec-logs.md)
