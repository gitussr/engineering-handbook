---
title: "Lab: Investigate a Container That Keeps Crash-Looping"
description: "An advanced Linux lab, framed as a real ticket: a Docker container keeps restarting in a crash loop, and you have to find the root cause from its logs and exit code."
type: "lab"
tier: "advanced"
careerRelevance: ["devops", "sre", "platform"]
updatedAt: "2026-07-30"
keywords: ["docker crash loop lab", "container debugging exercise", "docker logs exit code lab", "advanced linux lab containers"]
canonicalUrl: "/labs/advanced/investigate-a-container-that-keeps-crash-looping"
---

# Lab: Investigate a Container That Keeps Crash-Looping

🔴 Advanced · Relevant for: DevOps, SRE, Platform

## Scenario

**Ticket #LX-304:** "The `payments-worker` container keeps restarting every few seconds —
`docker ps` shows it cycling between `Restarting` and briefly `Up`. Find out why it's crashing
and fix it."

## Environment Setup

```bash
sudo tee /tmp/Dockerfile > /dev/null <<'EOF'
FROM alpine:3.19
COPY worker.sh /worker.sh
RUN chmod +x /worker.sh
CMD ["/worker.sh"]
EOF

# A deliberately broken entrypoint — references a config file that was never mounted
sudo tee /tmp/worker.sh > /dev/null <<'EOF'
#!/bin/sh
cat /etc/worker/config.yml
echo "Worker started"
EOF

docker build -t payments-worker /tmp
docker run -d --name payments-worker --restart unless-stopped payments-worker
```

## Tasks

1. Confirm the container is actually crash-looping and note its restart behavior.
2. Find the specific error causing the crash, not just that it's crashing.
3. Determine the exit code and what it indicates.
4. Fix the underlying cause and confirm the container stays up.

## Hints

<details><summary>Show hint</summary>
`docker logs` shows what the container printed before dying — usually the most direct path to the
cause. `docker ps -a` shows the exit code in the status column, which helps distinguish an
application crash from something more structural (missing entrypoint, immediate self-exit).
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Confirm the crash-loop behavior
docker ps -a --filter name=payments-worker
# STATUS: Restarting (1) 4 seconds ago

# Find the specific error
docker logs payments-worker
# cat: /etc/worker/config.yml: No such file or directory

# The exit code confirms an application-level failure, not a missing CMD/entrypoint issue
docker inspect payments-worker --format='{{.State.ExitCode}}'
# 1

# Fix: the config file needs to be mounted into the container, not baked into a broken assumption
sudo mkdir -p /etc/worker
sudo tee /etc/worker/config.yml > /dev/null <<'EOF'
setting: production
EOF

docker rm -f payments-worker
docker run -d --name payments-worker --restart unless-stopped \
  -v /etc/worker:/etc/worker:ro \
  payments-worker

# Confirm it stays up
sleep 5
docker ps --filter name=payments-worker
docker logs payments-worker
```

The root cause here is a common one in practice: the image assumed a config file would be present
at runtime, but nothing in the `docker run` command actually provided it — the fix is a volume
mount, not a code change to the worker script itself.
</details>

## What You Learned

- `docker logs` is the first place to look — a crash-looping container almost always printed
  something before dying.
- How to read a container's exit code with `docker inspect` to distinguish an application crash
  from a more structural startup problem.
- Why a container failing on a missing mounted file is a deployment/configuration issue, not
  necessarily a bug in the application itself.

## Related Modules

- [Docker Basics](../../docs/23-docker-basics/index.md)
- [Containers](../../docs/22-containers/index.md)

## Related topics

- [Docker Basics Cheat Sheet](../../cheatsheets/docker-basics.md)
- [Scenario-Based Interview Questions: Docker container exits immediately](../../interview-questions/scenario-based.md#docker-container-exits-immediately-after-start)
- [Labs Hub](../index.md)
