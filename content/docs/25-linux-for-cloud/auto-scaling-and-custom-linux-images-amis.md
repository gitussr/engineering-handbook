---
title: "Auto-Scaling and Custom Linux Images (AMIs)"
description: "Building a golden image once and launching a fleet of identical instances from it — how custom images and auto-scaling groups replace manual server provisioning at scale."
module: "25-linux-for-cloud"
moduleTitle: "Linux for Cloud"
stage: "production-engineer"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["25-linux-for-cloud/mounting-cloud-storage-s3fs-blob-mounts"]
relatedTopics: ["cloud-init", "linux-on-aws-gcp-azure"]
relatedCommands: []
careerRelevance: ["cloud", "devops", "sre", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["senior#golden-image-vs-cloud-init"]
relatedCheatsheet: ""
furtherReading: [{"label": "AWS EC2 Auto Scaling Documentation", "url": "https://docs.aws.amazon.com/autoscaling/ec2/userguide/"}, {"label": "HashiCorp Packer Documentation", "url": "https://developer.hashicorp.com/packer/docs"}]
nextTopic: "26-linux-for-devops/linux-role-in-ci-cd"
prevTopic: "25-linux-for-cloud/mounting-cloud-storage-s3fs-blob-mounts"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["custom ami explained", "golden image linux", "auto scaling group linux", "packer build ami", "immutable infrastructure images"]
canonicalUrl: "/docs/linux-for-cloud/auto-scaling-and-custom-linux-images-amis"
---

# Auto-Scaling and Custom Linux Images (AMIs)

🔴 Expert · Relevant for: Cloud · DevOps · SRE · Platform

> **TL;DR:** A custom image (AMI on AWS, machine image on GCP, managed image on Azure) is a
> pre-configured "golden image" baked once, then launched repeatedly — combined with auto-scaling,
> this replaces manually provisioning each new instance with automatically launching identical
> ones from the same known-good starting point.

## What is it?

A custom Linux image is a snapshot of a fully configured instance — OS, packages, application code
— saved so new instances can boot directly from it instead of starting from a bare provider image
and running `cloud-init` setup every time. An auto-scaling group launches (and terminates)
instances from that image automatically based on demand.

## Why does it exist?

Running full `cloud-init` provisioning (package installs, application deployment) on every single
instance launch works, but it's slower and more fragile at scale than launching from an image
where all of that has already happened once and been verified. Auto-scaling groups need to launch
new instances in seconds during a traffic spike — waiting minutes for `cloud-init` to finish
installing packages defeats the purpose.

## Where is it used?

Any fleet that needs to grow and shrink automatically with load — web tiers behind a load balancer
scaling out during peak traffic and back in overnight, or batch-processing fleets that scale to
zero when idle. It's also how teams achieve "immutable infrastructure": instead of patching a
running instance, they build a new image and replace the fleet.

## How it works

> 📊 Diagram: a pipeline showing a base OS image plus a build tool (Packer) plus provisioning
> scripts producing a custom AMI, which an auto-scaling group then launches multiple identical
> copies of behind a load balancer — with a second arrow showing the auto-scaling group terminating
> instances and launching from a newer AMI during a rolling update.

Building a custom image typically uses a tool like Packer: start from a base OS image, run
provisioning scripts (install packages, deploy application code, harden the OS using
[Module 19's](../19-security/server-hardening-checklist.md) checklist), then snapshot the result
into a new image.

```
$ packer build web-server.pkr.hcl
==> amazon-ebs: Creating AMI web-server-2026-07-29 from instance...
==> amazon-ebs: AMI: ami-0abcd1234efgh5678
```

That image ID is then referenced by an auto-scaling group's launch template, which still typically
runs a small amount of `cloud-init` user-data at boot (see [cloud-init](cloud-init.md)) for
instance-specific details like hostname, but skips the slow, repeated provisioning work already
baked into the image.

The tradeoff between the two approaches:

| Approach | Boot time | Consistency | Update process |
|---|---|---|---|
| Base image + full `cloud-init` provisioning | Slower (minutes) | Can drift if provisioning scripts change | Edit user-data, relaunch |
| Custom golden image | Fast (seconds) | Every instance identical to the image | Rebuild image, roll out new version |

## Real-world example

A team's auto-scaling group launches from a stock Ubuntu image with a `cloud-init` script that
installs their application's dependencies on every boot. During a real traffic spike, new instances
take almost four minutes to become healthy — long enough that the spike has often already passed
by the time capacity catches up. Switching to a custom image with dependencies pre-baked cuts
that to under thirty seconds, because the auto-scaling group is now launching a nearly
ready-to-serve instance instead of provisioning one from scratch under load.

## Syntax

No new syntax — image building uses external tooling (Packer, or a provider's own image-creation
API), not a new Linux command; the Linux-relevant part is what gets baked into the image and how
it interacts with `cloud-init` at boot.

## Commands

No new canonical command on this page — see [cloud-init](cloud-init.md) (this module) for the
boot-time configuration that still runs against a custom image, and
[Module 19's server hardening checklist](../19-security/server-hardening-checklist.md) for what
typically gets baked into a production image.

## Production example

```
$ aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names web-prod-asg \
    --query 'AutoScalingGroups[0].[DesiredCapacity,MinSize,MaxSize]'
[4, 2, 10]

$ aws autoscaling set-desired-capacity --auto-scaling-group-name web-prod-asg --desired-capacity 6
```

Confirming a fleet's current scaling bounds, then manually adjusting desired capacity ahead of an
anticipated traffic event — a common pre-emptive step alongside automatic scaling policies.

## Do / Don't

| Do | Don't |
|---|---|
| Bake dependencies into a custom image for fast, consistent launches | Reinstall dependencies via `cloud-init` on every boot at scale |
| Roll out image updates by replacing instances | Patch running auto-scaled instances individually |
| Keep a small amount of instance-specific `cloud-init` config | Bake instance-specific values (hostnames, IPs) into the image itself |

## Common mistakes

- Running full application provisioning via `cloud-init` on every auto-scaled launch, causing slow
  scale-out exactly when fast capacity is needed most.
- Hand-patching individual instances in an auto-scaling group instead of rebuilding the image and
  rolling it out, causing the fleet to drift out of sync with itself.
- Baking instance-specific values into the image rather than leaving them to a small, targeted
  `cloud-init` step at boot, making the image non-reusable across environments.

## Best practices

- Bake anything slow or unlikely to change per-launch (packages, application code, OS hardening)
  into the image; leave only genuinely instance-specific configuration to `cloud-init`.
- Treat image updates as the unit of change — rebuild and roll out a new image rather than patching
  a running fleet in place.
- Version and tag images clearly (by build date or application version) so a rollback means
  launching from a known-good previous image.

## Exercises

1. Explain why an auto-scaling group launching under a traffic spike benefits from a custom image
   over a base image plus full `cloud-init` provisioning.
2. Describe what should be baked into a custom image versus left to `cloud-init` at boot time.
3. Research how a rolling update works for an auto-scaling group switching to a new image version.

## Quiz

**Q: Why is boot time from a custom image typically much faster than from a base image with full `cloud-init` provisioning?**
<details><summary>Show answer</summary>
Package installs and application deployment already happened once when the image was built, so
the instance boots nearly ready to serve instead of repeating that provisioning work on every
launch.
</details>

**Q: What's the recommended way to update a running fleet of auto-scaled instances?**
<details><summary>Show answer</summary>
Rebuild the image with the update baked in and roll the fleet over to the new image, rather than
hand-patching individual running instances.
</details>

**Q: What should typically remain in `cloud-init` user-data even when launching from a custom image?**
<details><summary>Show answer</summary>
Genuinely instance-specific configuration (like hostname) that can't be baked into a reusable
image shared across every instance in the fleet.
</details>

## Interview questions

- What's the tradeoff between provisioning via `cloud-init` on every boot versus baking a custom
  golden image? →
  [Full answer in Senior/Expert Interview Questions](../../interview-questions/senior.md)

## Key Takeaways

- A custom image bakes provisioning in once, letting new instances launch fast and identically
  rather than repeating setup on every boot.
- Auto-scaling groups depend on fast launch times, making custom images the standard pattern for
  fleets that need to scale quickly under load.
- Updates to an auto-scaled fleet should replace instances from a new image, not patch running
  ones in place — the immutable-infrastructure pattern.
- `cloud-init` still runs against a custom image, but scoped to only genuinely instance-specific
  configuration.
- This closes Module 25 — Module 26 continues into Linux's role in CI/CD and infrastructure
  automation.

## Further Reading

- [AWS EC2 Auto Scaling Documentation](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)
- [HashiCorp Packer Documentation](https://developer.hashicorp.com/packer/docs)

## Related topics

- [cloud-init](cloud-init.md)
- [Linux on AWS/GCP/Azure](linux-on-aws-gcp-azure.md)
