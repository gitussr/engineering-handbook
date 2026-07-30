---
title: "docker — Build, Run, and Manage Containers"
description: "The single CLI for the entire container workflow — running containers, inspecting what's running, executing commands inside them, reading their logs, and building images."
relatedConcepts: ["23-docker-basics/docker-run-ps-exec-logs", "23-docker-basics/dockerfile-basics", "23-docker-basics/images-vs-containers", "23-docker-basics/docker-networking-basics", "23-docker-basics/docker-volumes"]
relatedCommands: ["systemctl"]
careerRelevance: ["devops", "cloud", "platform", "software-engineering"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["docker command", "docker run examples", "docker ps -a", "docker exec -it", "docker logs -f", "docker build -t", "docker network create", "docker volume create"]
canonicalUrl: "/commands/docker"
---

# docker

🟢 Must Know · Relevant for: DevOps · Cloud · Platform · Software Engineering

> **TL;DR:** `docker run -d --name NAME IMAGE` starts a container; `docker ps -a` lists all of
> them; `docker exec -it NAME bash` gets a shell inside a running one; `docker logs -f NAME`
> follows its output; `docker build -t NAME:TAG .` builds an image from a `Dockerfile`.

## Purpose

`docker` is the client CLI for the entire container workflow — building images, running
containers, and inspecting/interacting with them — see
[Docker Architecture](../docs/23-docker-basics/docker-architecture.md) for how it relates to the
daemon (`dockerd`) that actually does the work.

## Syntax

```
docker SUBCOMMAND [OPTIONS] [ARGUMENTS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SUBCOMMAND` | The operation to perform (`run`, `ps`, `exec`, `logs`, `build`, `images`, `stop`, `rm`, etc.) | Yes |
| `IMAGE` | An image name, optionally with `:TAG` (e.g. `nginx:1.25`) | Yes, for `run`/`build`/`images`-related operations |
| `CONTAINER` | A container's name or ID | Yes, for `exec`/`logs`/`stop`/`rm` |

## Options

| Subcommand | Flag | Meaning |
|---|---|---|
| `run` | `-d` | Detached — run in the background |
| `run` | `-it` | Interactive with a pseudo-TTY — for a live shell session |
| `run` | `--name NAME` | Assign an explicit, memorable container name |
| `run` | `-p HOST:CONTAINER` | Publish a container port to the host |
| `run` | `--rm` | Automatically remove the container when it exits |
| `run` | `--network NAME` | Attach the container to a specific user-defined network |
| `run` | `-v NAME:PATH` | Mount a named volume at a container path (see [Docker Volumes](../docs/23-docker-basics/docker-volumes.md)) |
| `run` | `-v HOST_PATH:PATH` | Bind-mount a specific host path into the container |
| `ps` | `-a` | Show all containers, including stopped ones (default shows only running) |
| `exec` | `-it` | Interactive with a pseudo-TTY — required for an interactive shell |
| `logs` | `-f` | Follow output continuously, like `tail -f` |
| `logs` | `--tail N` | Show only the last N lines |
| `build` | `-t NAME:TAG` | Tag the resulting image |
| `images` | (none) | List locally stored images |
| `stop` | (none) | Gracefully stop a running container |
| `rm` | (none) | Remove a stopped container |
| `rm` | `-f` | Force-remove, stopping it first if still running |
| `network create` | (none) | Create a user-defined network |
| `network ls` | (none) | List existing networks |
| `volume create` | (none) | Create a named, Docker-managed volume |
| `volume ls` | (none) | List existing volumes |

## Examples

```
$ docker run -d --name web -p 8080:80 nginx
```
Start an `nginx` container in the background, named `web`, publishing port 80 as 8080 on the host.

```
$ docker ps -a
```
List every container, running or stopped.

```
$ docker exec -it web bash
```
Open an interactive shell inside the running `web` container.

```
$ docker logs -f web
```
Follow `web`'s output continuously.

```
$ docker build -t myapp:v1.0 .
```
Build an image from the `Dockerfile` in the current directory, tagged `myapp:v1.0`.

```
$ docker stop web && docker rm web
```
Gracefully stop, then remove, the `web` container.

```
$ docker network create app-net
$ docker run -d --name db --network app-net postgres
```
Create a user-defined network and attach a container to it, enabling name-based DNS resolution
between containers on the same network — see
[Docker Networking Basics](../docs/23-docker-basics/docker-networking-basics.md).

```
$ docker volume create pgdata
$ docker run -d -v pgdata:/var/lib/postgresql/data postgres
```
Create a named volume and attach it so the container's data survives removal/recreation — see
[Docker Volumes](../docs/23-docker-basics/docker-volumes.md).

## Expected Output

```
$ docker ps
CONTAINER ID   IMAGE   COMMAND                  STATUS         PORTS                  NAMES
8f3e21a9c012   nginx   "/docker-entrypoint.…"   Up 5 seconds   0.0.0.0:8080->80/tcp   web
```

## Exit Status

`0` on success, non-zero if the daemon is unreachable, the image/container doesn't exist, or the
command inside a container (for `run`/`exec`) itself exits non-zero.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `permission denied` connecting to the Docker socket | Current user isn't in the `docker` group, or hasn't started a fresh session since being added | See [Installing Docker](../docs/23-docker-basics/installing-docker.md)'s post-install step |
| `Cannot connect to the Docker daemon` | `dockerd` isn't running | Start it: `sudo systemctl start docker` (Module 11) |
| `No such container` on `exec`/`logs`/`stop` | Wrong name/ID, or the container was already removed | Confirm with `docker ps -a` |
| Container exits immediately after `docker run` | The container's main process finished or crashed right away | Check `docker logs CONTAINER` for the reason |

## Security Considerations

Membership in the `docker` group is effectively equivalent to root access on the host — a
container can be run with host filesystem mounts and elevated capabilities. Treat `docker` group
membership with the same care as `sudo` access, not as an ordinary, low-privilege group
(see [Installing Docker](../docs/23-docker-basics/installing-docker.md)).

## Performance Considerations

Containers started with `-d` run in the background with minimal overhead beyond the process
itself; `docker logs -f` on a very high-output container can generate significant terminal/network
traffic if left running unattended.

## Production Usage

Production environments rarely run bare `docker run` commands manually — they're typically
orchestrated via [Docker Compose](../docs/23-docker-basics/docker-compose-intro.md) for
multi-container applications, or a full orchestrator (Kubernetes) for larger deployments. Direct
`docker` commands remain the standard tool for local development, debugging (`exec`, `logs`), and
building images (`build`).

## Related Commands

- [`systemctl`](systemctl.md) — manages the `docker` daemon service itself (Module 11)

## Related Concepts

- [docker run, ps, exec, logs](../docs/23-docker-basics/docker-run-ps-exec-logs.md)
- [Dockerfile Basics](../docs/23-docker-basics/dockerfile-basics.md)
- [Images vs Containers](../docs/23-docker-basics/images-vs-containers.md)
- [Docker Networking Basics](../docs/23-docker-basics/docker-networking-basics.md)
- [Docker Volumes](../docs/23-docker-basics/docker-volumes.md)
