---
title: "Docker Networking Basics"
description: "How containers reach each other and the outside world by default — the bridge network, port publishing, and container-to-container DNS by name on a user-defined network."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/docker-run-ps-exec-logs"]
relatedTopics: ["docker-run-ps-exec-logs", "docker-volumes"]
relatedCommands: ["docker"]
careerRelevance: ["devops", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#docker-bridge-network-dns"]
relatedCheatsheet: "docker-basics"
furtherReading: []
nextTopic: "23-docker-basics/docker-volumes"
prevTopic: "23-docker-basics/docker-run-ps-exec-logs"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["docker bridge network explained", "docker port publishing -p", "docker container dns by name", "docker network create"]
canonicalUrl: "/docs/docker-basics/docker-networking-basics"
---

# Docker Networking Basics

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform

> **TL;DR:** By default, containers get their own isolated network via a bridge, reachable from
> the host only through explicitly published ports (`-p HOST:CONTAINER`). Containers on the same
> user-defined network can reach each other directly by container name — Docker provides that DNS
> resolution automatically.

## What is it?

How Docker gives containers network connectivity — isolated from the host and each other by
default (using the network namespaces from
[Module 22](../22-containers/namespaces-and-cgroups-explained.md)), with explicit mechanisms to
open specific, controlled paths in and between them.

## Why does it exist?

Containers need to communicate — with the outside world (a web server needs to be reachable) and
with each other (a web application needs to reach its database) — while still keeping the
namespace-based isolation from Module 22 that keeps unrelated containers from seeing each other's
network traffic by default.

## Where is it used?

Every multi-container application: a web server that needs to be reachable from outside, and a web
application that needs to reach a database container — exactly the kind of setup
[Docker Compose](docker-compose-intro.md) (a few pages ahead) is built to manage more conveniently
than manual networking commands.

## How it works

> 📊 Diagram: a Docker host with a bridge network connecting several containers to each other and,
> via a single published port, to the outside world — one container reaching another directly by
> name (`db`) through Docker's built-in DNS on that shared network, contrasted with a container on
> a *different* network unable to resolve or reach it at all.

**The default bridge network:** every container gets its own network namespace by default, attached
to a private bridge network — isolated from the host's network directly, and from other Docker
networks.

**Publishing a port — making a container reachable from the host:**

```bash
docker run -d -p 8080:80 nginx
```

`-p HOST_PORT:CONTAINER_PORT` maps a host port to a container port — without this flag, the
container's internal port 80 is running but completely unreachable from outside the container,
even though the container itself works fine.

**User-defined networks — container-to-container communication by name:**

```bash
docker network create mynet
docker run -d --name db --network mynet postgres
docker run -d --name web --network mynet myapp
```

Containers attached to the same user-defined network (`mynet`) can reach each other directly by
container name (`web`'s application code can simply connect to `db`, no IP address needed) — Docker
runs an internal DNS resolver for exactly this purpose. This name-based resolution is specific to
user-defined networks; it does **not** work on the default bridge network.

## Real-world example

A web application container tries to connect to a database container using the database's
container name (`db`) as the hostname, but the connection fails with a DNS resolution error — both
containers were started without an explicit `--network` flag, placing them on the default bridge
network, which doesn't provide name-based resolution between containers. Creating a user-defined
network and attaching both containers to it resolves the issue immediately, with no other code
changes needed — the application was written correctly; the containers just weren't on a network
that supports the DNS resolution it assumed.

## Syntax

```
docker run -p HOST_PORT:CONTAINER_PORT IMAGE
docker network create NETWORK_NAME
docker run --network NETWORK_NAME --name NAME IMAGE
```

## Commands

See [`docker`](../../commands/docker.md) — extended below with `network` subcommand coverage.

## Production example

```
$ docker network create app-net
$ docker run -d --name db --network app-net -e POSTGRES_PASSWORD=secret postgres
$ docker run -d --name web --network app-net -p 8080:80 myapp

$ docker exec web ping -c 1 db
PING db (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.089 ms
```

`web` reaches `db` by name over the shared user-defined network, while only `web`'s port is
published to the host — `db` remains unreachable from outside, exactly the intended isolation.

## Do / Don't

| Do | Don't |
|---|---|
| Create a user-defined network for multi-container applications that need to talk to each other | Rely on the default bridge network and expect containers to resolve each other by name |
| Publish only the ports that genuinely need external access | Publish every container's ports out of habit, unnecessarily expanding exposed surface |
| Use container names as hostnames on a shared user-defined network | Hardcode container IP addresses, which can change between restarts |

## Common mistakes

- Expecting name-based resolution between containers on the default bridge network, not realizing
  that behavior is specific to user-defined networks.
- Publishing a container's port unnecessarily when it only ever needs to be reached by other
  containers on the same network, not from the host or outside world.
- Hardcoding a container's IP address instead of using its name, breaking the connection the next
  time the container restarts with a different IP.

## Best practices

- Always create a user-defined network for any application with multiple communicating
  containers, rather than relying on the default bridge.
- Publish (`-p`) only ports that genuinely need to be reachable from outside the container network
  — least-privilege applied to networking, the same principle from
  [Module 19](../19-security/firewall-configuration-in-depth.md).
- Reference other containers by name on a shared network, never by IP address, since IPs aren't
  guaranteed stable across restarts.

## Exercises

1. Create a user-defined network and run two containers on it, confirming they can reach each
   other by name.
2. Explain why a container's internal port being "open" doesn't mean it's reachable from the host
   without `-p`.
3. Describe why the default bridge network doesn't support container-name-based DNS resolution.

## Quiz

**Q: Does publishing no ports (-p) mean a container has no network connectivity at all?**
<details><summary>Show answer</summary>
No — the container can still have full network connectivity to other containers on the same
network; `-p` specifically controls whether the host (and outside world) can reach it, not whether
the container has networking at all.
</details>

**Q: Why might two containers fail to resolve each other by name?**
<details><summary>Show answer</summary>
They're likely on the default bridge network, which doesn't provide container-name DNS resolution
— that feature is specific to user-defined networks created with `docker network create`.
</details>

**Q: Should you reference another container by its IP address or its name on a shared network?**
<details><summary>Show answer</summary>
By name — container IP addresses aren't guaranteed to stay the same across restarts, while the
container name remains a stable, resolvable reference on a user-defined network.
</details>

## Interview questions

- Why might two containers be unable to reach each other by name, and how would you fix it? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Containers are network-isolated by default; `-p` explicitly publishes a port to the host.
- User-defined networks (`docker network create`) provide automatic DNS resolution between
  containers by name — the default bridge network does not.
- Publish only the ports that genuinely need external access, following least-privilege.
- Reference other containers by name, never by IP address, on a shared network.

## Related topics

- [docker run, ps, exec, logs](docker-run-ps-exec-logs.md)
- [Docker Volumes](docker-volumes.md)
