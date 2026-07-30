---
title: "docker run, ps, exec, logs"
description: "The four commands used constantly in day-to-day container work — starting a container, listing what's running, getting a shell inside one, and reading its output."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/dockerfile-basics"]
relatedTopics: ["dockerfile-basics", "docker-networking-basics"]
relatedCommands: ["docker"]
careerRelevance: ["devops", "cloud", "platform", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#docker-exec-vs-attach"]
relatedCheatsheet: "docker-basics"
furtherReading: []
nextTopic: "23-docker-basics/docker-networking-basics"
prevTopic: "23-docker-basics/dockerfile-basics"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["docker run examples", "docker ps -a", "docker exec -it", "docker logs -f"]
canonicalUrl: "/docs/docker-basics/docker-run-ps-exec-logs"
---

# docker run, ps, exec, logs

🟢 Must Know · Relevant for: DevOps · Cloud · Platform · Software Engineering

> **TL;DR:** `docker run` starts a container, `docker ps` lists running ones (`-a` for all,
> including stopped), `docker exec -it` opens an interactive shell inside a running one, and
> `docker logs -f` follows its output — the four commands covering nearly all day-to-day container
> interaction.

## What is it?

The four everyday commands for working with containers directly, building on the image/container
model from [Images vs Containers](images-vs-containers.md) and the images built via
[Dockerfile Basics](dockerfile-basics.md).

## Why does it exist?

Building an image is only half the workflow — running it, checking what's currently running,
getting inside a running container to investigate something, and reading its output are the
day-to-day operational tasks every container-based workflow depends on.

## Where is it used?

Every stage of working with containers: starting a service locally for development, checking
what's running on a shared host, debugging a misbehaving container by getting a shell inside it,
and reading application output the same way [Module 16's](../16-logs/index.md) log-investigation
skills apply to any other running process.

## How it works

> 📊 Diagram: a container lifecycle timeline — `docker run` starting a container (from an image),
> `docker ps` listing it while running, `docker exec -it` branching into it for a live interactive
> session, and `docker logs -f` tapping its output stream continuously — four distinct interaction
> points around the same running container.

**`docker run`** — start a container from an image:

```bash
docker run -d --name web nginx           # detached (background), named
docker run -it ubuntu bash               # interactive, with a pseudo-TTY — for exploring
docker run -p 8080:80 nginx              # publish port 80 in the container as 8080 on the host
```

**`docker ps`** — list containers:

```bash
docker ps            # running containers only
docker ps -a          # all containers, including stopped ones
```

**`docker exec`** — run a command inside an already-running container:

```bash
docker exec -it web bash    # open an interactive shell inside the running "web" container
```

`exec` requires the container to already be running — it's for getting *into* a live container,
distinct from `run`, which starts a new one.

**`docker logs`** — read a container's output:

```bash
docker logs web        # print all output so far
docker logs -f web      # follow continuously, like tail -f (Module 06)
```

## Real-world example

An engineer needs to investigate why a running web application container is returning errors.
`docker ps` confirms the container (`web`) is indeed running, `docker logs -f web` streams its
output live while a test request is sent — revealing a stack trace pointing to a missing
configuration file. `docker exec -it web bash` opens an interactive shell directly inside the
running container to confirm the file is genuinely absent, without stopping or restarting anything
— the same live-investigation instinct from [Module 16](../16-logs/reading-logs-during-a-production-incident.md),
applied inside a container instead of directly on a host.

## Syntax

```
docker run [OPTIONS] IMAGE [COMMAND]
docker ps [-a]
docker exec [OPTIONS] CONTAINER COMMAND
docker logs [-f] CONTAINER
```

## Commands

See [`docker`](../../commands/docker.md) for the full canonical reference, covering these four
subcommands plus `build`, `images`, `stop`, and `rm`.

## Production example

```
$ docker run -d --name web -p 8080:80 nginx
8f3e21a9c012...

$ docker ps
CONTAINER ID   IMAGE   STATUS         PORTS                  NAMES
8f3e21a9c012   nginx   Up 5 seconds   0.0.0.0:8080->80/tcp   web

$ docker logs web
/docker-entrypoint.sh: Configuration complete; ready for start up

$ docker exec -it web bash
root@8f3e21a9c012:/# 
```

Starting a named, port-published container, confirming it's running, reading its startup log, and
opening a shell inside it — the complete everyday workflow in four commands.

## Do / Don't

| Do | Don't |
|---|---|
| Name containers explicitly (`--name`) for easier reference | Rely on Docker's randomly-generated container names in scripts or documentation |
| Use `docker ps -a` when a container seems to have disappeared | Assume `docker ps` (without `-a`) shows every container that ever existed |
| Use `exec` to get inside an already-running container | Use `run` when you meant to attach to an existing container, accidentally starting a duplicate |

## Common mistakes

- Forgetting `-a` on `docker ps` and concluding a container doesn't exist, when it's actually just
  stopped, not running.
- Running `docker run` again to "get back into" a container, accidentally starting a brand new,
  separate container instead of using `exec` to enter the existing one.
- Using `docker logs` (without `-f`) expecting live output, not realizing it only prints what's
  already been logged up to that point unless `-f` is added.

## Best practices

- Always name containers explicitly (`--name`) rather than relying on Docker's auto-generated
  names, which are harder to reference consistently in scripts and runbooks.
- Reach for `docker exec -it CONTAINER bash` (or `sh`, if `bash` isn't available in a minimal image)
  as the default way to investigate a running container directly.
- Use `docker logs -f` the same way `tail -f` or `journalctl -f` are used elsewhere in this
  documentation — the standard pattern for watching live output.

## Exercises

1. Run a named container in detached mode, then use `docker ps` to confirm it's running.
2. Use `docker exec -it` to open a shell inside that container.
3. Explain the difference between `docker run` and `docker exec` in your own words.

## Quiz

**Q: What's the difference between docker run and docker exec?**
<details><summary>Show answer</summary>
`docker run` starts a brand new container from an image; `docker exec` runs a command inside an
already-running container — they aren't interchangeable.
</details>

**Q: Why might docker ps show no containers even though one was run recently?**
<details><summary>Show answer</summary>
`docker ps` without `-a` shows only currently running containers — a stopped container is still
visible with `docker ps -a`, but not in the default view.
</details>

**Q: What does docker logs -f do differently from docker logs alone?**
<details><summary>Show answer</summary>
`-f` follows the log output continuously, like `tail -f`, rather than printing only what's already
been logged and exiting immediately.
</details>

## Interview questions

- What's the difference between docker run and docker exec, and when would you use each? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `docker run` starts a new container; `docker exec` enters an already-running one — a common
  point of confusion for newcomers.
- `docker ps -a` shows every container, including stopped ones; plain `docker ps` shows only
  running ones.
- `docker logs -f` follows output continuously, the same pattern as `tail -f`/`journalctl -f`
  elsewhere in this documentation.
- These four commands cover the large majority of day-to-day container interaction.

## Related topics

- [Dockerfile Basics](dockerfile-basics.md)
- [Docker Networking Basics](docker-networking-basics.md)
