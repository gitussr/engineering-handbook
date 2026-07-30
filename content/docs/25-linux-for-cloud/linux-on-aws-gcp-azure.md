---
title: "Linux on AWS / GCP / Azure — What's Actually Different"
description: "The same Linux you already know, running on someone else's hardware — what genuinely changes about administering it on AWS EC2, GCP Compute Engine, and Azure VMs, and what doesn't."
module: "25-linux-for-cloud"
moduleTitle: "Linux for Cloud"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/which-linux-skills-k8s-depends-on"]
relatedTopics: ["cloud-init", "managing-cloud-vms-over-ssh-at-scale"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "sre", "platform", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#linux-cloud-vm-differences"]
relatedCheatsheet: ""
furtherReading: [{"label": "AWS EC2 User Guide", "url": "https://docs.aws.amazon.com/ec2/"}, {"label": "GCP Compute Engine Documentation", "url": "https://cloud.google.com/compute/docs"}, {"label": "Azure Virtual Machines Documentation", "url": "https://learn.microsoft.com/en-us/azure/virtual-machines/"}]
nextTopic: "25-linux-for-cloud/cloud-init"
prevTopic: "24-kubernetes-basics/which-linux-skills-k8s-depends-on"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["linux on aws ec2", "linux on gcp compute engine", "linux on azure vm", "cloud linux administration differences", "linux cloud vm vs on-prem"]
canonicalUrl: "/docs/linux-for-cloud/linux-on-aws-gcp-azure"
---

# Linux on AWS / GCP / Azure — What's Actually Different

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Platform · Linux Administrator

> **TL;DR:** The Linux kernel, shell, and every command from Modules 01-24 work identically on a
> cloud VM — what changes is everything *around* the OS: how it boots, how it's provisioned, how
> it authenticates, and how it's expected to be disposable rather than hand-maintained.

## What is it?

A cloud virtual machine (AWS calls it an EC2 instance, GCP a Compute Engine instance, Azure a
Virtual Machine) runs the same Linux distributions covered throughout this roadmap — Ubuntu,
Debian, RHEL-family distros. The instance itself is still a real Linux system with a real kernel,
real processes, and real filesystems.

## Why does it exist?

Running Linux on physical hardware you own means you control provisioning, networking, and
lifecycle end to end. Cloud providers abstract that hardware away and replace manual provisioning
with APIs, images, and metadata services — which changes *how* a Linux system is created and
managed, without changing the Linux system itself.

## Where is it used?

Nearly every production Linux server outside of on-premises data centers and embedded systems runs
on one of these three providers today. DevOps, SRE, cloud, and platform roles all assume comfort
moving between "a Linux box" and "a cloud instance" without treating them as different skills.

## How it works

> 📊 Diagram: three parallel stacks (AWS, GCP, Azure) each showing the same Linux OS layer at the
> top, with each provider's distinct layer underneath it labeled — hypervisor, instance metadata
> service (with its distinct IP/path per provider), boot/image system, and IAM/identity layer —
> making clear the OS layer is identical while everything underneath differs.

| Difference | On-prem / bare metal | Cloud VM |
|---|---|---|
| Provisioning | Manual install from ISO/PXE boot | Boot from a pre-built image (AMI on AWS, image on GCP, managed image on Azure) in seconds |
| First-boot config | Manual, or a custom install script | `cloud-init` reads provider-supplied metadata automatically — see [cloud-init](cloud-init.md) |
| Identity/instance metadata | N/A | A metadata service reachable only from the instance itself (`169.254.169.254` on AWS and GCP, similarly on Azure) exposes instance ID, IAM role, network config, and more |
| Disk | Physical disks you manage directly | Network-attached block storage (EBS on AWS, Persistent Disk on GCP, Managed Disk on Azure) that can be detached/reattached independently of the VM |
| Lifecycle expectation | Long-lived, hand-maintained | Often short-lived and disposable — replaced via a new image rather than patched in place |
| Networking | Physical switches/routers you configure | Software-defined virtual networks (VPC on AWS/GCP, VNet on Azure) configured via API, not `ip`/`nmcli` on the box |

The instance metadata service is the detail most likely to surprise someone coming from bare
metal: from inside the VM, a plain HTTP request retrieves details about the instance itself.

```
$ curl http://169.254.169.254/latest/meta-data/instance-id
i-0abcd1234efgh5678
```

This works on AWS without any credentials, because reachability from inside the VM is treated as
the authorization boundary — see [IAM and Instance Roles](iam-and-instance-roles.md) for why this
matters for security.

## Real-world example

An engineer used to managing a fleet of physical Ubuntu servers spins up their first EC2 instance
and immediately tries to run the same manual hardening script they use on-prem — setting a static
IP with `ip addr add`, editing `/etc/network/interfaces` by hand. The changes get silently
overwritten on next boot, because `cloud-init` re-applies the provider's supplied network
configuration on every boot unless explicitly told not to. The fix isn't to fight `cloud-init` —
it's to express the same configuration *through* `cloud-init`'s own config, which persists
correctly across reboots and re-launches.

## Syntax

No new syntax — provider-specific configuration happens through each provider's own CLI/console
(`aws`, `gcloud`, `az`), which are cloud automation tools outside this roadmap's Linux command
scope, not Linux shell commands themselves.

## Commands

No new canonical command on this page — see [cloud-init](cloud-init.md) for the first
cloud-specific command this module introduces.

## Production example

```
$ curl http://169.254.169.254/latest/meta-data/public-ipv4
203.0.113.42

$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
ec2-instance-role

$ cat /etc/os-release
NAME="Ubuntu"
VERSION="22.04.4 LTS (Jammy Jellyfish)"
```

An engineer confirming, entirely from the shell, what public IP an instance was assigned and which
IAM role it's running as — information that on bare metal would require checking a router or
asking whoever provisioned the box.

## Do / Don't

| Do | Don't |
|---|---|
| Configure networking/hostname through `cloud-init`, not manual file edits | Hand-edit network config files that get overwritten on next boot |
| Treat cloud instances as disposable/replaceable | Hand-patch a production instance the way you would a long-lived physical server |
| Learn each provider's metadata service endpoint | Assume all three providers expose metadata identically |

## Common mistakes

- Manually editing network configuration files on a cloud VM, not realizing `cloud-init` will
  silently reapply the provider's supplied configuration on the next boot, discarding the change.
- Assuming AWS, GCP, and Azure instance metadata services behave identically — the endpoint,
  authentication model, and exact paths differ across providers even though the concept is shared.
- Treating a cloud VM like a pet server that gets hand-maintained indefinitely, instead of the
  disposable, image-replaceable resource it's designed to be.

## Best practices

- Learn to distinguish "this is Linux, and I already know it" from "this is provider-specific, and
  I need to check the docs" — most day-to-day skills from Modules 01-24 transfer directly.
- Use each provider's metadata service for programmatic instance information instead of
  hardcoding values or asking a human.
- Prefer replacing an instance from an updated image over hand-patching it in place, once a fleet
  is large enough that manual per-instance maintenance doesn't scale.

## Exercises

1. From a cloud VM (or a local VM simulating one), query the instance metadata service and
   retrieve the instance ID and public IP.
2. Compare `/etc/os-release` output on an AWS, GCP, or Azure default image against a local
   installation of the same distribution — confirm the OS layer is identical.
3. Identify one configuration file on a cloud VM that gets managed by `cloud-init` rather than
   edited directly.

## Quiz

**Q: What actually changes about Linux itself when it runs on a cloud VM instead of bare metal?**
<details><summary>Show answer</summary>
Nothing about the Linux kernel, shell, or commands changes — what changes is everything around it:
provisioning, first-boot configuration, networking, and identity, all mediated through
provider-specific systems like `cloud-init` and the instance metadata service.
</details>

**Q: Why does manually editing network configuration on a cloud VM often fail to persist?**
<details><summary>Show answer</summary>
`cloud-init` re-applies the provider-supplied network configuration on every boot by default,
overwriting manual edits unless the configuration is expressed through `cloud-init` itself.
</details>

**Q: What is the instance metadata service, and why is it reachable without credentials?**
<details><summary>Show answer</summary>
An HTTP endpoint (typically `169.254.169.254`) reachable only from inside the instance itself,
exposing instance details like ID, IP, and IAM role — network reachability from inside the VM is
treated as the authorization boundary.
</details>

## Interview questions

- What's actually different about administering Linux on a cloud VM versus bare metal? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- The Linux OS layer — kernel, shell, commands — is identical on AWS, GCP, and Azure and on bare
  metal; everything else (provisioning, boot, identity, networking) differs.
- `cloud-init` re-applies provider-supplied configuration on every boot, which is why manual edits
  to network/hostname files don't reliably persist.
- Every major provider exposes an in-instance-only metadata service for instance details and
  credentials, with different endpoints and conventions per provider.
- Cloud VMs are designed to be disposable and image-replaceable, not hand-maintained indefinitely.

## Further Reading

- [AWS EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [GCP Compute Engine Documentation](https://cloud.google.com/compute/docs)
- [Azure Virtual Machines Documentation](https://learn.microsoft.com/en-us/azure/virtual-machines/)

## Related topics

- [cloud-init](cloud-init.md)
- [Managing Cloud VMs over SSH at Scale](managing-cloud-vms-over-ssh-at-scale.md)
