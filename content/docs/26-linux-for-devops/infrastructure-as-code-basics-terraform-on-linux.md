---
title: "Infrastructure as Code Basics: Terraform on Linux"
description: "Ansible configures machines that already exist; Terraform creates the machines (and networks, and storage) themselves, declaratively, from a Linux CLI — the layer beneath everything Module 25 covered."
module: "26-linux-for-devops"
moduleTitle: "Linux for DevOps"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["26-linux-for-devops/automation-with-bash-and-ansible"]
relatedTopics: ["automation-with-bash-and-ansible", "devops-toolchain-on-linux-end-to-end"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#terraform-vs-ansible"]
relatedCheatsheet: ""
furtherReading: [{"label": "Terraform Documentation", "url": "https://developer.hashicorp.com/terraform/docs"}]
nextTopic: "26-linux-for-devops/devops-toolchain-on-linux-end-to-end"
prevTopic: "26-linux-for-devops/automation-with-bash-and-ansible"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["terraform explained", "infrastructure as code basics", "terraform vs ansible", "terraform on linux", "terraform state file"]
canonicalUrl: "/docs/linux-for-devops/infrastructure-as-code-basics-terraform-on-linux"
---

# Infrastructure as Code Basics: Terraform on Linux

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform · SRE

> **TL;DR:** Terraform provisions cloud infrastructure itself — instances, networks, storage —
> declaratively from a config file, running as an ordinary Linux CLI process on a workstation or
> CI runner; the Terraform binary is Linux tooling even though what it manages is the cloud.

## What is it?

Terraform is an Infrastructure as Code (IaC) tool: infrastructure (cloud VMs, networks, storage,
IAM roles from [Module 25](../25-linux-for-cloud/iam-and-instance-roles.md)) is described in a
declarative configuration file, and Terraform creates, updates, or destroys real cloud resources
to match it.

## Why does it exist?

Clicking through a cloud console to create resources is manual, unrepeatable, and leaves no record
of exactly what was created or why. Describing infrastructure as code makes it version-controlled,
reviewable, and repeatable — the same environment can be recreated exactly, and changes go through
the same review process as application code.

## Where is it used?

Provisioning the cloud infrastructure that everything else in this module runs on top of: the
instances a CI/CD runner or Ansible-managed fleet actually runs on, the networks connecting them,
and the storage they use — Terraform typically creates the infrastructure; Ansible (or `cloud-init`
from Module 25) then configures what runs on it.

## How it works

> 📊 Diagram: a Terraform config file on the left, a `terraform plan`/`apply` step in the middle
> (running as a Linux process on a workstation or CI runner), and real cloud resources (an EC2
> instance, a VPC, an S3 bucket) on the right — with a small state file shown as what Terraform
> uses to track what it has already created, distinguishing IaC from Ansible's stateless,
> re-checked-every-run model.

```hcl
# main.tf
resource "aws_instance" "web" {
  ami           = "ami-0abcd1234efgh5678"
  instance_type = "t3.micro"
  tags = {
    Name = "web-prod-03"
  }
}
```

```
$ terraform init
$ terraform plan
$ terraform apply
```

`terraform plan` shows exactly what would change before anything happens — the declarative
equivalent of a dry run — and `terraform apply` makes it real. Terraform tracks what it has already
created in a state file, which is how it knows whether to create a new resource or leave an
existing one untouched on a second run.

## Real-world example

A team manually creates a new EC2 instance through the AWS console for a one-off project, and six
months later no one remembers exactly what settings were used or whether it's still needed —
"click-ops" infrastructure with no record and no easy way to recreate it elsewhere. A team using
Terraform for the same task has the instance's exact configuration in version control, can recreate
an identical instance in a different environment with the same file, and can see in `git blame`
exactly who changed what and when.

## Syntax

```
terraform init
terraform plan
terraform apply
terraform destroy
```

## Commands

No new canonical command page — `terraform` is shown illustratively above; per
[architecture/01-roadmap.md's](../../../architecture/01-roadmap.md) scalability rule, full
Terraform usage is a candidate for a future sibling section outside the core 34-module roadmap,
the same treatment planned for Ansible.

## Production example

```
$ terraform plan
Terraform will perform the following actions:

  # aws_instance.web will be created
  + resource "aws_instance" "web" {
      + ami           = "ami-0abcd1234efgh5678"
      + instance_type = "t3.micro"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

Reviewing exactly what Terraform intends to do before running `apply` — a standard step in any
production change process, the IaC equivalent of reviewing a code diff before merging.

## Do / Don't

| Do | Don't |
|---|---|
| Review `terraform plan` output before every `apply` | Run `apply` blind without checking the plan |
| Keep Terraform configuration in version control | Make manual "click-ops" changes to Terraform-managed resources |
| Treat Terraform and Ansible as complementary | Try to make Terraform configure software the way Ansible does |

## Common mistakes

- Making a manual console change to a resource Terraform manages, causing Terraform's next `plan`
  to show an unexpected drift between actual and declared state.
- Running `terraform apply` without reviewing the plan output first, applying an unintended change.
- Confusing Terraform's job (creating infrastructure) with Ansible's job (configuring what runs on
  it) and trying to make one tool do both.

## Best practices

- Always review `terraform plan` output before running `apply`, treating it like a code review.
- Never make manual changes to infrastructure Terraform manages — every change should go through
  the same file, reviewed and version-controlled.
- Use Terraform to create infrastructure and Ansible (or `cloud-init`) to configure it, rather than
  blurring the two tools' responsibilities.

## Exercises

1. Read a simple Terraform configuration file and identify what resources it would create.
2. Explain the difference between `terraform plan` and `terraform apply`.
3. Describe what would happen if someone manually deleted a resource Terraform created, then ran
   `terraform plan` again.

## Quiz

**Q: What does Terraform actually create — the infrastructure itself, or configuration on top of existing infrastructure?**
<details><summary>Show answer</summary>
The infrastructure itself — instances, networks, storage — as distinct from Ansible, which
configures software on infrastructure that already exists.
</details>

**Q: What does `terraform plan` do, and why is it run before `apply`?**
<details><summary>Show answer</summary>
It shows exactly what would change without making any changes yet — reviewed before `apply` the
same way a code diff is reviewed before merging.
</details>

**Q: Why does a manual console change to a Terraform-managed resource cause problems?**
<details><summary>Show answer</summary>
It creates drift between the actual infrastructure and Terraform's declared configuration, which
Terraform's next plan will detect and attempt to reconcile, potentially undoing the manual change.
</details>

## Interview questions

- What's the difference between Terraform and Ansible, and when would you use each? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Terraform provisions infrastructure itself, declaratively, tracked via a state file.
- `terraform plan` previews changes before `apply` makes them real — a standard review step.
- Manual changes to Terraform-managed resources cause drift and should be avoided.
- Terraform and Ansible are complementary: Terraform creates infrastructure, Ansible (or
  `cloud-init`) configures it.

## Further Reading

- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)

## Related topics

- [Automation with Bash and Ansible](automation-with-bash-and-ansible.md)
- [The DevOps Toolchain on Linux, End to End](devops-toolchain-on-linux-end-to-end.md)
