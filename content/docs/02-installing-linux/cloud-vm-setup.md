---
title: "Spinning Up a Cloud VM"
description: "Launching a Linux VM on AWS EC2, GCP Compute Engine, or Azure — the decisions that matter (image, security group, key pair) and the cost/security mistakes that catch beginners."
module: "02-installing-linux"
moduleTitle: "Installing Linux"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["02-installing-linux/installing-rhel-centos-rocky"]
relatedTopics: ["installing-ubuntu-debian"]
relatedCommands: ["ssh"]
careerRelevance: ["devops", "cloud", "sre", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#cloud-vm-setup"]
relatedCheatsheet: ""
furtherReading: [{"label": "AWS EC2 Getting Started", "url": "https://docs.aws.amazon.com/ec2/index.html"}, {"label": "GCP Compute Engine documentation", "url": "https://cloud.google.com/compute/docs"}]
nextTopic: "02-installing-linux/dual-boot-considerations"
prevTopic: "02-installing-linux/installing-rhel-centos-rocky"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["aws ec2", "gcp compute engine", "azure vm", "launch cloud vm", "ssh key pair"]
canonicalUrl: "/docs/installing-linux/cloud-vm-setup"
---

# Spinning Up a Cloud VM

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Platform

> **TL;DR:** Launching a cloud VM means picking an image, an instance size, a key pair for
> SSH access, and a firewall rule (security group) — get the firewall rule wrong and you've
> exposed a server to the entire internet; forget to stop the instance and you keep paying for it.

## What is it?

Provisioning a Linux virtual machine on a cloud provider (AWS EC2, GCP Compute Engine, Azure VM)
instead of installing locally. This is how the overwhelming majority of production Linux systems
actually get created.

## Why does it exist?

Cloud VMs remove the need to own or manage physical hardware — you rent compute by the
hour/second, scale up or down on demand, and get built-in networking, storage, and snapshotting
without setting any of it up yourself. This is the deployment model nearly every DevOps/Cloud/SRE
role works in daily.

## Where is it used?

- Production application servers, databases, and background workers
- CI/CD runners
- Short-lived test/POC environments (spin up, test, tear down)
- The actual infrastructure behind managed services like Kubernetes node pools

Every major provider's flow maps to the same four decisions:

| Decision | AWS EC2 | GCP Compute Engine | Azure |
|---|---|---|---|
| Base image | AMI | Image | VM Image |
| Instance size | Instance type (e.g. `t3.micro`) | Machine type | VM size |
| Network access rule | Security Group | Firewall rule | Network Security Group |
| Remote access credential | Key pair | SSH key | SSH key |

## How it works

> 📊 Diagram: flowchart — Choose Image → Choose Instance Size → Attach/Create Key Pair →
> Configure Security Group/Firewall (open only what's needed) → Launch → SSH In → (later)
> Stop/Terminate.

The two decisions that actually matter for safety and cost:

1. **Security group / firewall rule** — this decides who on the internet can reach the VM at all.
   The default temptation is to open SSH (port 22) to `0.0.0.0/0` (anywhere) to "make it work" —
   this is the single most common beginner mistake and a real security exposure.
2. **Instance lifecycle** — a running instance costs money every hour (or is billed even while
   stopped, depending on attached storage). Forgetting to stop or terminate a test instance is
   the most common beginner cost mistake.

## Real-world example

A learner spins up a `t3.micro` EC2 instance to practice SSH, leaves the security group open to
`0.0.0.0/0` on port 22 "just for now," and forgets about it. Within hours, automated internet
scanners find the open port and start brute-forcing SSH logins — a real, common outcome, not a
hypothetical. The fix takes thirty seconds (restrict the security group to your own IP) but has
to be done at launch time, not remembered later.

## Commands

- [`ssh`](../../commands/ssh.md) — connect to the VM once it's running (full syntax and key-based
  auth covered in [Module 13: SSH](../13-ssh/index.md); shown here only as the immediate next step
  after launch)

## Production example

```
$ ssh -i my-key.pem ubuntu@34.201.XX.XX
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 6.8.0-1015-aws x86_64)
ubuntu@ip-172-31-40-12:~$
```

The `-i my-key.pem` flag points to the private key that matches the key pair chosen at launch —
this is how cloud VMs authenticate SSH instead of a password by default.

## Do / Don't

| Do | Don't |
|---|---|
| Restrict SSH access to your own IP in the security group | Open port 22 to `0.0.0.0/0` "just to make it work" |
| Stop or terminate test instances when done | Leave a test VM running and forget about it |
| Use key-based SSH auth (the provider default) | Disable key auth in favor of a weak password |

## Common mistakes

- Opening SSH to the entire internet instead of a specific IP range — the fastest way to get a
  fresh cloud VM attacked within hours.
- Losing the private key file with no way to recover access — most providers have no password
  fallback if the key pair is lost, meaning the instance becomes inaccessible.
- Forgetting to stop/terminate instances after testing — the most common "surprise cloud bill"
  cause for beginners.
- Attaching a large storage volume "just in case" and forgetting it keeps billing even when the
  instance is stopped.

## Best practices

- Scope security group rules to the narrowest access that still works — your IP only, not the
  world.
- Tag every instance with a purpose/owner so nothing running is a mystery later.
- For anything beyond a quick POC, provision through Infrastructure as Code (Terraform, covered
  later) instead of manual console clicks — repeatable and reviewable.
- Set a habit of tearing down test instances the moment you're done with them.

## Exercises

1. Launch a free-tier-eligible VM on any major cloud provider.
2. Configure the security group/firewall rule to allow SSH only from your own IP address.
3. SSH into the instance, confirm access, then stop or terminate it.

## Quiz

**Q: What's the most common beginner security mistake when launching a cloud VM?**
<details><summary>Show answer</summary>
Opening SSH (port 22) to the entire internet (`0.0.0.0/0`) in the security group/firewall rule
instead of restricting it to a known IP range.
</details>

**Q: Why might a stopped cloud instance still be costing money?**
<details><summary>Show answer</summary>
Attached storage volumes typically continue to bill even while the compute instance itself is
stopped — only terminating the instance (and its volumes) fully stops charges in most cases.
</details>

## Interview questions

- What security group/firewall configuration would you use for a public-facing web server vs. an
  internal database server? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Every cloud provider's VM launch flow reduces to the same four decisions: image, size, network
  access rule, and SSH credential.
- The security group/firewall rule is the single highest-impact setting — scope it narrowly.
- Forgetting to stop/terminate instances is the most common beginner cost mistake.
- Key-based SSH auth is the default and correct way to access a cloud VM, not a password.

## Further Reading

- [AWS EC2 Getting Started](https://docs.aws.amazon.com/ec2/index.html)
- [GCP Compute Engine documentation](https://cloud.google.com/compute/docs)

## Related topics

- [Module 13: SSH](../13-ssh/index.md)
- [Module 25: Linux for Cloud](../25-linux-for-cloud/index.md)
