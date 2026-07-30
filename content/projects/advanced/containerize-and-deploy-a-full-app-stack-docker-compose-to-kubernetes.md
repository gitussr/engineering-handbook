---
title: "Project: Containerize a Full App Stack with Docker Compose, Then Port It to Kubernetes"
description: "An advanced Linux project: containerize an Nginx + app + database stack with Docker Compose, then re-deploy the same stack on a single-node Kubernetes cluster."
type: "project"
tier: "advanced"
careerRelevance: ["devops", "cloud", "platform", "sre"]
updatedAt: "2026-07-30"
keywords: ["docker compose to kubernetes project", "containerize app stack project", "kubernetes migration project", "advanced docker project"]
canonicalUrl: "/projects/advanced/containerize-and-deploy-a-full-app-stack-docker-compose-to-kubernetes"
---

# Project: Containerize a Full App Stack with Docker Compose, Then Port It to Kubernetes

🔴 Advanced · Relevant for: DevOps, Cloud, Platform, SRE

## Goal

Take a realistic three-tier application (Nginx + app server + database) from "runs on your laptop"
to "runs as a proper Docker Compose stack," then re-deploy that exact same stack on a single-node
Kubernetes cluster — experiencing firsthand what changes and what stays the same between the two.

## Requirements

- A Dockerfile for the app tier that follows real best practices (no running as root, minimal
  base image, no secrets baked into the image).
- A `docker-compose.yml` that brings up Nginx, the app, and a database with correct networking
  and a named volume for the database's data.
- The same stack re-expressed as Kubernetes manifests: Deployments, Services, and a
  PersistentVolumeClaim for the database.
- Environment-specific configuration (database credentials, app config) handled via ConfigMaps
  and Secrets in the Kubernetes version — not hardcoded into images or manifests.
- Both versions must survive a restart with no data loss for the database tier.

## Suggested Approach

1. Build and test the Docker Compose version fully first — don't attempt both platforms at once.
   Confirm the database's data survives `docker compose down && docker compose up`.
2. Write the Dockerfile with production practices from the start (non-root user, `.dockerignore`,
   multi-stage build if the app has a build step) rather than retrofitting them later.
3. Once Compose works, translate service-by-service into Kubernetes manifests — start with the
   database (PVC + Deployment + Service), confirm it persists data, then add the app, then Nginx.
4. Move any config that was an environment variable in Compose into a ConfigMap, and anything
   secret (passwords, keys) into a Secret — this is the step most people get lazy about.
5. Deliberately delete and recreate a pod (not the whole cluster) to confirm the app tier is
   genuinely stateless and the database tier's data survives via the PVC.

## Stretch Goals

- Add resource requests/limits to the Kubernetes manifests and observe what happens when you set
  them unrealistically low.
- Add a Horizontal Pod Autoscaler to the app tier and load-test it into scaling up.
- Add an Ingress resource in front of the app instead of a bare Service, and compare it to the
  Nginx reverse-proxy role in the Compose version.
- Write a short comparison doc: what got easier, what got harder, moving from Compose to Kubernetes.

## Related Modules

- [Docker Basics](../../docs/23-docker-basics/index.md) — Dockerfile, Compose, volumes, networking
- [Kubernetes Basics](../../docs/24-kubernetes-basics/index.md) — Pods, Deployments, Services, ConfigMaps/Secrets
- [Containers](../../docs/22-containers/index.md) — namespaces and cgroups underlying both platforms

## Related topics

- [Docker Basics Cheat Sheet](../../cheatsheets/docker-basics.md)
- [Deploy a Web App with Nginx Project (Intermediate)](../intermediate/deploy-and-reverse-proxy-a-web-app-with-nginx.md)
- [Projects Hub](../index.md)
