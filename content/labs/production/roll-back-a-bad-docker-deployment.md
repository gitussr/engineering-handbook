---
title: "Lab: Roll Back a Bad Docker Deployment"
description: "A production-tier Linux lab: a deploy just shipped a broken container to production, and you have to roll back to the last known-good version quickly and confirm the rollback actually worked."
type: "lab"
tier: "production"
careerRelevance: ["devops", "sre", "platform"]
updatedAt: "2026-07-30"
keywords: ["docker rollback lab", "bad deployment recovery lab", "production docker incident lab", "rollback container exercise"]
canonicalUrl: "/labs/production/roll-back-a-bad-docker-deployment"
---

# Lab: Roll Back a Bad Docker Deployment

🔴 Production · Relevant for: DevOps, SRE, Platform

## Scenario

**Slack alert, mid-deploy:** "Just shipped `orders-api:v14` — error rate immediately spiked to
40%. This is actively affecting customers right now. Roll back to `v13` immediately, we'll debug
`v14` after service is stable."

## Environment Setup

```bash
# A "good" previous version
docker run -d --name orders-api --label version=v13 -p 8080:80 nginx:alpine

# Simulate the bad deploy that just happened
docker rm -f orders-api
docker run -d --name orders-api --label version=v14 -p 8080:80 --health-cmd="exit 1" \
  --health-interval=5s nginx:alpine
```

Treat `v13` as the last known-good image tag you need to roll back to — in a real incident this
would come from your deploy history/registry, not something you have to guess.

## Tasks

1. Confirm the current deployed version is unhealthy — don't just trust the report, verify it.
2. Roll back to the last known-good version as quickly as possible — speed matters here, this is
   actively affecting customers.
3. Confirm the rollback actually restored healthy behavior, not just that a container is running.
4. Note what you'd do differently after service is stable (this is explicitly a "stabilize first,
   debug later" situation).

## Hints

<details><summary>Show hint</summary>
Don't try to fix `v14` under pressure — the ticket is explicit that the priority is restoring
service, not debugging the bad version live in production. `docker inspect` and container health
status can confirm whether a container is actually healthy, not just running.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Step 1 — confirm the current version is genuinely unhealthy
docker ps --filter name=orders-api
docker inspect orders-api --format='{{.State.Health.Status}}'
# unhealthy

# Step 2 — roll back fast: stop the bad version, start the known-good one
docker rm -f orders-api
docker run -d --name orders-api --label version=v13 -p 8080:80 nginx:alpine

# Step 3 — confirm the rollback actually restored healthy behavior
docker ps --filter name=orders-api
curl -I http://127.0.0.1:8080
docker inspect orders-api --format='{{index .Config.Labels "version"}}'
# v13

# Step 4 — for the incident channel, once stable:
# "Rolled back orders-api to v13, confirmed healthy via direct request. v14 is NOT being
# redeployed until root-caused in a non-production environment — will investigate the health
# check failure from v14's build/config separately, off the production host."
```

The discipline this lab is really testing: resisting the urge to debug the broken version live in
production under pressure. The correct move is restore-first, investigate-later, in an environment
where a wrong guess doesn't cost more customer impact.
</details>

## What You Learned

- Why "rollback first, root-cause later" is the correct default when customers are actively
  affected — production is not the place to debug a bad deploy.
- How to confirm a container's health status programmatically instead of assuming "running" means
  "working."
- Why labeling/tagging deployed versions (`--label version=`) matters — you can't roll back
  confidently to something you can't identify.

## Related Modules

- [Docker Basics](../../docs/23-docker-basics/index.md)
- [Linux for DevOps](../../docs/26-linux-for-devops/index.md)

## Related topics

- [Docker Basics Cheat Sheet](../../cheatsheets/docker-basics.md)
- [CI/CD Pipeline That Deploys to a Cloud VM Project](../../projects/advanced/build-a-cicd-pipeline-that-deploys-to-a-cloud-vm.md)
- [Labs Hub](../index.md)
