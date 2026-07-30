---
title: "Linux for DevOps"
description: "Module 26 of the Linux roadmap — a career-branch module on Linux's role in CI/CD, automation with Bash and Ansible, Infrastructure as Code with Terraform, and assembling a full DevOps toolchain and pipeline on Linux servers."
module: "26-linux-for-devops"
moduleTitle: "Linux for DevOps"
stage: "production-engineer"
type: "module-index"
nextTopic: "26-linux-for-devops/linux-role-in-ci-cd"
updatedAt: "2026-07-29"
canonicalUrl: "/docs/linux-for-devops"
---

# Linux for DevOps

Module 26 of 34 · Stage: Production Engineer · Previous: [25 Linux for Cloud](../25-linux-for-cloud/index.md)

The second career-branch module, buildable in any order relative to Modules 25, 27, and 28. This
module covers what CI/CD platforms actually run underneath their dashboards, fleet automation with
Bash and Ansible, provisioning infrastructure declaratively with Terraform, a full map of how a
modern DevOps toolchain fits together on Linux, and a hands-on capstone assembling a minimal
pipeline from primitives already covered across this roadmap.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Linux's Role in CI/CD](linux-role-in-ci-cd.md) | 🟢 Must Know |
| [Automation with Bash and Ansible](automation-with-bash-and-ansible.md) | 🟡 Good to Know |
| [Infrastructure as Code Basics: Terraform on Linux](infrastructure-as-code-basics-terraform-on-linux.md) | 🟡 Good to Know |
| [The DevOps Toolchain on Linux, End to End](devops-toolchain-on-linux-end-to-end.md) | 🟢 Must Know |
| [Building a CI/CD Pipeline on Linux Servers](building-a-cicd-pipeline-on-linux-servers.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain that a CI/CD pipeline step is, underneath the platform's YAML, an ordinary shell command
  running on a real Linux runner.
- Explain the difference between Bash automation, Ansible, and Terraform, and when each is the
  right tool.
- Trace a DevOps toolchain error down to its actual underlying Linux-level cause, rather than
  stopping at a tool's own error message.
- Assemble a minimal CI/CD pipeline from Bash scripting, cron/webhooks, and systemd.

## Known, intentional gaps in this module

- No canonical command pages were created in this module — no roadmap bullet in Module 26 names a
  specific command in backticks. `ansible`/`ansible-playbook` and `terraform` are shown
  illustratively throughout; per
  [architecture/01-roadmap.md's](../../../architecture/01-roadmap.md) scalability rule, both are
  candidates for a future sibling section outside the core 34-module roadmap (the same treatment
  planned for Git in Developer Essentials), rather than getting full canonical pages folded into
  this module.
- CI/CD platforms themselves (GitHub Actions, GitLab CI, Jenkins) are covered conceptually as
  external systems, the same treatment given to Prometheus/Grafana in Module 20 and ELK/Loki in
  Module 16 — this module teaches the underlying Linux mechanics they all share, not any single
  platform's specific configuration syntax.
- Git and version control are out of scope for this module — covered separately in the Developer
  Essentials section per
  [architecture/09-developer-essentials.md](../../../architecture/09-developer-essentials.md).

**Previous module:** [25 Linux for Cloud](../25-linux-for-cloud/index.md)
**Next module:** [27 Linux for Cybersecurity →](../27-linux-for-cybersecurity/index.md)
