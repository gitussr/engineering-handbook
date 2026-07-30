---
title: "Linux for Cloud"
description: "Module 25 of the Linux roadmap — the first Production Engineer career-branch module. What genuinely differs about running Linux on AWS, GCP, and Azure: cloud-init, fleet-scale SSH, IAM instance roles, mounting object storage, and auto-scaling with custom images."
module: "25-linux-for-cloud"
moduleTitle: "Linux for Cloud"
stage: "production-engineer"
type: "module-index"
nextTopic: "25-linux-for-cloud/linux-on-aws-gcp-azure"
updatedAt: "2026-07-29"
canonicalUrl: "/docs/linux-for-cloud"
---

# Linux for Cloud

Module 25 of 34 · Stage: Production Engineer · Previous: [24 Kubernetes Basics](../24-kubernetes-basics/index.md)

Modules 01-24 built Linux fundamentals that apply everywhere. This module is the first of four
career-branch modules (25-28) applying those fundamentals specifically to cloud infrastructure:
what genuinely differs about Linux on AWS, GCP, and Azure, `cloud-init` for first-boot
configuration, managing fleets of instances over SSH, IAM instance roles for credential-free API
access, mounting object storage, and auto-scaling from custom images. Modules 25-28 can be studied
in any order once 01-24 are complete.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Linux on AWS / GCP / Azure — What's Actually Different](linux-on-aws-gcp-azure.md) | 🟢 Must Know |
| [cloud-init](cloud-init.md) | 🟡 Good to Know |
| [Managing Cloud VMs over SSH at Scale](managing-cloud-vms-over-ssh-at-scale.md) | 🟢 Must Know |
| [IAM and Instance Roles (Linux-Relevant Parts)](iam-and-instance-roles.md) | 🟡 Good to Know |
| [Mounting Cloud Storage: S3FS, Blob Mounts](mounting-cloud-storage-s3fs-blob-mounts.md) | 🔴 Expert |
| [Auto-Scaling and Custom Linux Images (AMIs)](auto-scaling-and-custom-linux-images-amis.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain what actually changes about Linux administration on a cloud VM versus bare metal, and
  what stays identical.
- Write a `cloud-init` user-data file to configure a fresh instance without manual login.
- Manage a fleet of cloud instances over SSH using a bastion host and key distribution at launch,
  instead of exposing every instance directly.
- Explain why an IAM instance role is preferred over a hardcoded API credential, and how a process
  retrieves one.
- Explain what `s3fs`/`blobfuse`-style tools actually do, and when to use them versus a native SDK.
- Explain how custom images and auto-scaling groups combine to launch fleets quickly and
  consistently under load.

## Known, intentional gaps in this module

- A canonical command page exists for [`cloud-init`](../../commands/cloud-init.md) — the only
  command this module's roadmap bullets name explicitly in backticks.
- Cloud provider CLIs (`aws`, `gcloud`, `az`) are shown illustratively throughout this module but
  don't get canonical command pages — they're cloud automation tools, not Linux shell commands,
  and fall outside this roadmap's core Linux command scope (the same boundary applied to
  Terraform/Ansible in [architecture/01-roadmap.md](../../../architecture/01-roadmap.md)'s Module
  26 preview).
- `s3fs`, `blobfuse`/`blobfuse2`, and `packer` are shown illustratively without canonical command
  pages — their roadmap bullets ("S3FS, blob mounts," "custom Linux images") name concepts, not
  specific commands in backticks, the same treatment LVM/RAID received in Module 15.
- The instance metadata service, IAM roles, and auto-scaling groups are configured through each
  provider's own console/API, not a Linux command — this module teaches the Linux-relevant
  consequences (credential retrieval, `cloud-init` interaction) rather than provider-specific
  console/API usage.

**Previous module:** [24 Kubernetes Basics](../24-kubernetes-basics/index.md)
**Next module:** [26 Linux for DevOps →](../26-linux-for-devops/index.md)
