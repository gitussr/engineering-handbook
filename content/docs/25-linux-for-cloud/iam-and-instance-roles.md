---
title: "IAM and Instance Roles (Linux-Relevant Parts)"
description: "Why cloud instances almost never store hardcoded API credentials — how an instance role lets a Linux process authenticate to cloud APIs using short-lived, automatically-rotated credentials pulled from the metadata service."
module: "25-linux-for-cloud"
moduleTitle: "Linux for Cloud"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["25-linux-for-cloud/managing-cloud-vms-over-ssh-at-scale"]
relatedTopics: ["linux-on-aws-gcp-azure", "mounting-cloud-storage-s3fs-blob-mounts"]
relatedCommands: []
careerRelevance: ["cloud", "devops", "cybersecurity", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#instance-role-vs-hardcoded-credentials"]
relatedCheatsheet: ""
furtherReading: [{"label": "AWS IAM Roles for EC2 Instances", "url": "https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html"}]
nextTopic: "25-linux-for-cloud/mounting-cloud-storage-s3fs-blob-mounts"
prevTopic: "25-linux-for-cloud/managing-cloud-vms-over-ssh-at-scale"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["iam instance role explained", "ec2 instance profile", "cloud vm credentials", "instance metadata service credentials", "avoid hardcoded api keys"]
canonicalUrl: "/docs/linux-for-cloud/iam-and-instance-roles"
---

# IAM and Instance Roles (Linux-Relevant Parts)

🟡 Good to Know · Relevant for: Cloud · DevOps · Cybersecurity · Platform

> **TL;DR:** An instance role lets a Linux process on a cloud VM call cloud provider APIs (read a
> storage bucket, write a log) without any credentials ever being stored on disk — the instance
> fetches short-lived, automatically-rotated credentials from the metadata service instead.

## What is it?

IAM (Identity and Access Management) roles attached to a cloud instance — an "instance role" on
AWS, a "service account" on GCP, or a "managed identity" on Azure — grant the processes running on
that instance permission to call specific cloud APIs, without a static access key ever being
written to the filesystem.

## Why does it exist?

Hardcoding an API access key into a config file or environment variable on a server means that key
exists indefinitely, gets copied into backups and images, and grants full access if the server is
ever compromised. Instance roles replace static credentials with short-lived ones the instance
requests itself, automatically rotated, scoped to only the specific permissions that instance
actually needs.

## Where is it used?

Any Linux process on a cloud instance that needs to call a cloud API — an application reading
objects from a storage bucket, a backup script writing to another account, monitoring agents
sending metrics, or the `s3fs`/blob-mount tooling covered in
[Mounting Cloud Storage](mounting-cloud-storage-s3fs-blob-mounts.md), which authenticates using
exactly this mechanism rather than a stored key file.

## How it works

> 📊 Diagram: an application process on an EC2 instance calling the local-only metadata service
> (`169.254.169.254`) to retrieve short-lived, auto-rotating temporary credentials tied to an
> attached IAM role — contrasted side-by-side with a legacy pattern of a static access key sitting
> in a config file on disk, with the static-key path marked as the anti-pattern.

From inside the instance, retrieving the role's temporary credentials is a plain HTTP request to
the same metadata service introduced in
[Linux on AWS/GCP/Azure](linux-on-aws-gcp-azure.md):

```
$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
ec2-instance-role

$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/ec2-instance-role
{
  "AccessKeyId": "ASIAEXAMPLE...",
  "SecretAccessKey": "wJalrXUtnFEMI...",
  "Token": "AgoJb3JpZ2luX2VjE...",
  "Expiration": "2026-07-29T18:00:00Z"
}
```

These credentials expire (typically within hours) and are transparently refreshed by any properly
configured cloud SDK or CLI — an application never needs to parse this response manually in normal
use; SDKs check the metadata service automatically when no other credentials are configured.

## Real-world example

A security review of a fleet of EC2 instances finds a long-lived AWS access key hardcoded in an
application's config file, unrotated for two years, with far broader permissions than the
application actually needed — a direct consequence of the key being created once, working, and
never being revisited. Replacing it with an instance role scoped to only the specific S3 bucket and
actions the application actually calls eliminates the stored credential entirely and bounds the
blast radius if the instance is ever compromised to exactly that role's permissions.

## Syntax

No new syntax — instance roles are configured through the provider's IAM console/API (`aws iam`,
equivalent GCP/Azure tooling), not a Linux command; the Linux-relevant part is that applications
retrieve credentials automatically via the metadata service rather than reading a key file.

## Commands

No new canonical command on this page — retrieving instance-role credentials is shown above via
`curl` against the metadata service, already introduced in
[Linux on AWS/GCP/Azure](linux-on-aws-gcp-azure.md).

## Production example

```
$ aws s3 ls s3://prod-app-data/
2026-07-29 09:14:22       4821 config.json
```

The `aws` CLI succeeds with no access key configured anywhere on the instance — it silently
retrieved temporary credentials from the metadata service because an instance role is attached,
exactly the intended behavior.

## Do / Don't

| Do | Don't |
|---|---|
| Attach a scoped instance role for API access | Hardcode a static access key in a config file |
| Grant only the specific permissions a workload needs | Attach a broad, catch-all role out of convenience |
| Let the SDK/CLI fetch credentials from the metadata service automatically | Manually parse and cache metadata-service credentials in application code |

## Common mistakes

- Hardcoding a static access key instead of using an instance role, creating a long-lived
  credential that outlives its usefulness and must be manually rotated.
- Attaching an overly broad IAM role "to avoid permission errors," expanding the blast radius of a
  compromised instance far beyond what it actually needs.
- Assuming every process on an instance should share the same instance role, instead of running
  workloads that need different permissions on separately-scoped instances or roles.

## Best practices

- Scope every instance role to the minimum permissions the workload actually requires (least
  privilege), reviewed periodically as the workload changes.
- Never write instance-role credentials to disk or logs — let the SDK/CLI retrieve and refresh
  them from the metadata service transparently.
- Treat any long-lived static credential found on an instance as a finding to remediate, not a
  normal pattern.

## Exercises

1. From a cloud instance with an attached role, retrieve its temporary credentials from the
   metadata service and note the expiration time.
2. Explain why a short-lived, auto-rotated credential is safer than a static access key, even if
   both currently grant the same permissions.
3. Identify one workload in a hypothetical fleet that should have a more narrowly scoped role than
   the rest of the fleet.

## Quiz

**Q: Why is a static, hardcoded API key considered a security risk on a cloud instance?**
<details><summary>Show answer</summary>
It's long-lived, gets copied into backups/images, and grants its full permissions indefinitely if
the instance is ever compromised — unlike an instance role's short-lived, auto-rotated
credentials.
</details>

**Q: How does an application on a cloud instance actually obtain instance-role credentials?**
<details><summary>Show answer</summary>
By querying the instance metadata service over HTTP, typically handled automatically by the
cloud provider's SDK or CLI rather than by application code directly.
</details>

**Q: What does "least privilege" mean in the context of an instance role?**
<details><summary>Show answer</summary>
Granting only the specific permissions a workload actually needs, rather than a broad role that
would grant far more access than necessary if the instance were compromised.
</details>

## Interview questions

- Why should a cloud instance use an IAM instance role instead of a hardcoded access key? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Instance roles let processes on a cloud VM call provider APIs using short-lived, auto-rotated
  credentials instead of a static, stored access key.
- Credentials are retrieved from the same in-instance-only metadata service that exposes other
  instance details.
- Least privilege — scoping a role to exactly what a workload needs — bounds the damage if an
  instance is ever compromised.
- A hardcoded, long-lived credential found on an instance is a finding to fix, not a normal
  pattern.

## Further Reading

- [AWS IAM Roles for EC2 Instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)

## Related topics

- [Linux on AWS/GCP/Azure](linux-on-aws-gcp-azure.md)
- [Mounting Cloud Storage: S3FS, Blob Mounts](mounting-cloud-storage-s3fs-blob-mounts.md)
