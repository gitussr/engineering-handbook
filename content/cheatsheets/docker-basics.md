---
title: "Docker Basics Cheat Sheet"
description: "A single-page quick reference for Docker: running and inspecting containers, Dockerfile basics, networking, volumes, and Docker Compose."
type: "cheatsheet"
module: "docker-basics"
updatedAt: "2026-07-30"
keywords: ["docker cheat sheet", "docker commands cheat sheet", "dockerfile cheat sheet", "docker compose cheat sheet"]
canonicalUrl: "/cheatsheets/docker-basics"
---

# Docker Basics Cheat Sheet

The day-to-day Docker commands for running, inspecting, and cleaning up containers, plus the
basics of Dockerfiles, networking, volumes, and Compose.

## Images and containers

| Command | Description |
|---|---|
| [`docker`](../commands/docker.md) `images` | List locally downloaded images |
| `docker pull {image}` | Download an image from a registry without running it |
| `docker rmi {image}` | Remove a local image |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers, including stopped ones |
| `docker rm {container}` | Remove a stopped container |

Full explanation → [Images vs Containers](../docs/23-docker-basics/images-vs-containers.md) · [Docker Architecture](../docs/23-docker-basics/docker-architecture.md)

## Running and inspecting containers

| Command | Description |
|---|---|
| `docker run {image}` | Create and start a new container from an image |
| `docker run -d -p 8080:80 {image}` | Run detached (background), mapping host port 8080 to container port 80 |
| `docker exec -it {container} bash` | Open an interactive shell inside a running container |
| `docker logs {container}` | Show a container's stdout/stderr output |
| `docker logs -f {container}` | Follow a container's logs live |
| `docker stop {container}` | Gracefully stop a running container |
| `docker rm -f {container}` | Force-remove a running container |

Full explanation → [docker run, ps, exec, logs](../docs/23-docker-basics/docker-run-ps-exec-logs.md)

## Dockerfile basics

| Instruction | Description |
|---|---|
| `FROM {image}` | Base image to build on top of |
| `RUN {command}` | Execute a command at build time (installs, setup) |
| `COPY src dst` | Copy files from the build context into the image |
| `WORKDIR /path` | Set the working directory for subsequent instructions |
| `EXPOSE {port}` | Document which port the container listens on (informational) |
| `CMD ["executable", "arg"]` | Default command run when the container starts |

Full explanation → [Dockerfile Basics](../docs/23-docker-basics/dockerfile-basics.md)

## Networking

| Command | Description |
|---|---|
| `docker network ls` | List Docker networks |
| `docker network create {name}` | Create a custom bridge network |
| `docker run --network {name} {image}` | Attach a container to a specific network |

Full explanation → [Docker Networking Basics](../docs/23-docker-basics/docker-networking-basics.md)

## Volumes

| Command | Description |
|---|---|
| `docker volume create {name}` | Create a named, Docker-managed volume |
| `docker run -v {name}:/data {image}` | Mount a named volume into a container |
| `docker run -v /host/path:/container/path {image}` | Bind-mount a specific host path into a container |
| `docker volume ls` | List volumes |

Full explanation → [Docker Volumes](../docs/23-docker-basics/docker-volumes.md)

## Docker Compose

| Command | Description |
|---|---|
| `docker compose up` | Start every service defined in `docker-compose.yml` |
| `docker compose up -d` | Start in detached (background) mode |
| `docker compose down` | Stop and remove all services, networks, and (by default) anonymous volumes |
| `docker compose logs -f` | Follow logs across all services |

Full explanation → [Docker Compose Intro](../docs/23-docker-basics/docker-compose-intro.md)

## Related topics

- [Package Managers Cheat Sheet](package-managers.md)
- [Docker Basics Module](../docs/23-docker-basics/index.md)
- [Docker Best Practices](../docs/23-docker-basics/docker-best-practices.md)
- [Cheat Sheets Hub](index.md)
