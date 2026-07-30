---
title: "Project: Build a CI/CD Pipeline That Deploys to a Cloud VM"
description: "An advanced Linux project: build a CI/CD pipeline that tests, builds, and automatically deploys an application to a cloud VM over SSH on every push."
type: "project"
tier: "advanced"
careerRelevance: ["devops", "cloud", "platform"]
updatedAt: "2026-07-30"
keywords: ["cicd pipeline project linux", "deploy to cloud vm project", "github actions ssh deploy project", "devops pipeline project advanced"]
canonicalUrl: "/projects/advanced/build-a-cicd-pipeline-that-deploys-to-a-cloud-vm"
---

# Project: Build a CI/CD Pipeline That Deploys to a Cloud VM

🔴 Advanced · Relevant for: DevOps, Cloud, Platform

## Goal

Build a real, working CI/CD pipeline: pushing to your main branch runs tests, builds the
application, and automatically deploys it to a live cloud VM — with a working rollback path if
the deploy breaks something.

## Requirements

- A CI pipeline (GitHub Actions, GitLab CI, or self-hosted runner — your choice) triggered on
  every push to `main`.
- The pipeline runs the application's test suite and stops the deploy if tests fail.
- The pipeline deploys to a cloud VM over SSH using a dedicated deploy user with least-privilege
  access, not your personal admin account.
- Deploy credentials (SSH key, any secrets) are stored in the CI platform's secret store, never
  committed to the repository.
- A documented, tested rollback procedure — you must actually break a deploy on purpose and
  successfully roll back, not just write the steps down.

## Suggested Approach

1. Get manual deployment working first: SSH in, pull the latest code, restart the service, by
   hand — you can't automate a process you haven't done manually and understood.
2. Create the dedicated deploy user on the VM with only the permissions the deploy actually needs
   (write access to the app directory, permission to restart its systemd service — nothing else).
3. Wire up the CI platform's secret store with the deploy SSH key, and write the pipeline job
   that SSHes in and performs the same steps you did manually, one CI run at a time.
4. Add the test-gate: confirm a deliberately-broken test actually blocks the deploy step from
   running, not just that passing tests deploy successfully.
5. Practice a rollback: deploy a version with a deliberate bug, confirm you notice, then execute
   your rollback procedure and confirm the previous version is serving traffic again.

## Stretch Goals

- Add a staging environment the pipeline deploys to first, with a manual approval gate before
  production.
- Add a health check step after deploy that automatically rolls back if the app doesn't come up
  healthy within a timeout.
- Add deployment notifications (Slack/webhook) so the team knows what shipped and when.
- Parameterize the pipeline to deploy the same app to multiple environments from one workflow file.

## Related Modules

- [Linux for DevOps](../../docs/26-linux-for-devops/index.md) — Linux's role in CI/CD, building a pipeline on Linux servers
- [SSH](../../docs/13-ssh/index.md) — key-based deploy access, hardening
- [Services](../../docs/11-services/index.md) — restarting the app as a managed service during deploy

## Related topics

- [Deploy a Web App with Nginx Project (Intermediate)](../intermediate/deploy-and-reverse-proxy-a-web-app-with-nginx.md)
- [SSH Cheat Sheet](../../cheatsheets/ssh.md)
- [Projects Hub](../index.md)
