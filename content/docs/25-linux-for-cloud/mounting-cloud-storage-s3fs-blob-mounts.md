---
title: "Mounting Cloud Storage: S3FS, Blob Mounts"
description: "Making object storage (S3, Azure Blob, GCS) appear as a regular mounted filesystem — how it works, why it's not a real filesystem underneath, and when to reach for it versus an SDK."
module: "25-linux-for-cloud"
moduleTitle: "Linux for Cloud"
stage: "production-engineer"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["25-linux-for-cloud/iam-and-instance-roles"]
relatedTopics: ["iam-and-instance-roles", "auto-scaling-and-custom-linux-images-amis"]
relatedCommands: ["mount"]
careerRelevance: ["cloud", "devops", "platform", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["senior#s3fs-vs-native-object-storage-sdk"]
relatedCheatsheet: ""
furtherReading: [{"label": "s3fs-fuse GitHub Repository", "url": "https://github.com/s3fs-fuse/s3fs-fuse"}, {"label": "Azure Blob Storage: Mount with BlobFuse2", "url": "https://learn.microsoft.com/en-us/azure/storage/blobs/blobfuse2-what-is"}]
nextTopic: "25-linux-for-cloud/auto-scaling-and-custom-linux-images-amis"
prevTopic: "25-linux-for-cloud/iam-and-instance-roles"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["s3fs mount s3 bucket", "azure blob fuse mount", "mount object storage as filesystem", "fuse filesystem cloud storage", "s3fs vs sdk"]
canonicalUrl: "/docs/linux-for-cloud/mounting-cloud-storage-s3fs-blob-mounts"
---

# Mounting Cloud Storage: S3FS, Blob Mounts

🔴 Expert · Relevant for: Cloud · DevOps · Platform · Backend

> **TL;DR:** Tools like `s3fs` and `blobfuse` let object storage (S3, Azure Blob, GCS) appear as a
> regular mounted directory via `mount`, but it's a FUSE-based translation layer, not a real
> filesystem — expect higher latency and weaker consistency guarantees than local or network-block
> storage, and prefer a native SDK for anything performance-sensitive.

## What is it?

FUSE (Filesystem in Userspace) tools — `s3fs` for AWS S3, `blobfuse`/`blobfuse2` for Azure Blob
Storage, `gcsfuse` for Google Cloud Storage — translate object storage API calls into standard
filesystem operations, so an application can `ls`, `cat`, or `cp` against a bucket as if it were a
locally mounted directory.

## Why does it exist?

Many existing applications and scripts are written to work with files on a filesystem, not to call
an object storage API directly, and rewriting them isn't always practical. Mounting object storage
as a filesystem lets legacy or file-oriented tooling work against cloud storage with no code
changes, at the cost of the storage no longer behaving exactly like a real filesystem underneath.

## Where is it used?

Legacy applications expecting a filesystem path, batch jobs that read/write files and shouldn't be
rewritten just to move to the cloud, and situations where the convenience of `cp`/`ls`-style
interaction outweighs the performance cost — never for latency-sensitive or high-throughput
workloads, which should call the object storage SDK directly instead.

## How it works

> 📊 Diagram: an application calling standard filesystem syscalls (`open`, `read`, `write`)
> against a mounted directory, intercepted by the FUSE kernel module and handed to `s3fs` in
> userspace, which translates each syscall into an actual HTTPS API call to S3 — labeled clearly
> as an extra translation layer, not a native filesystem path.

Object storage (S3, Blob, GCS) is fundamentally a key-value API over HTTPS, not a filesystem — it
has no directories in the traditional sense, no atomic in-place edits, and higher per-operation
latency than local or network-block storage. `s3fs` and similar tools bridge this gap using FUSE, a
kernel module that lets a userspace program implement filesystem operations:

```bash
$ s3fs my-bucket /mnt/s3data -o iam_role=auto

$ ls /mnt/s3data
config.json  logs/  backups/

$ cat /mnt/s3data/config.json
{"env": "production"}
```

`-o iam_role=auto` uses the instance role from [IAM and Instance Roles](iam-and-instance-roles.md)
rather than a stored access key — the same credential pattern applies here as everywhere else on a
cloud instance.

Every filesystem operation against the mount becomes a real network API call, which is why `s3fs`
and its equivalents are dramatically slower than local disk or even network block storage for
workloads with many small operations, and why write consistency can lag behind what a real
filesystem guarantees.

## Real-world example

A team mounts an S3 bucket with `s3fs` to let a legacy reporting script write output files "the
same way it always has," without needing to rewrite it to call the S3 API directly. The script
works, but a later performance investigation finds it spending most of its runtime on filesystem
operations that translate into individual HTTPS requests — switching the script to batch its
writes and use the AWS SDK directly cuts its runtime by an order of magnitude, illustrating exactly
why `s3fs` is a compatibility bridge, not a performance-equivalent replacement for native storage
access.

## Syntax

```
s3fs BUCKET MOUNTPOINT [OPTIONS]
```

## Commands

No new canonical command page — `s3fs`/`blobfuse` are shown illustratively above; see
[`mount`](../../commands/mount.md) (Module 05) for the standard Linux mounting concept these tools
build on via FUSE.

## Production example

```
$ s3fs my-bucket /mnt/s3data -o iam_role=auto,allow_other

$ df -h /mnt/s3data
Filesystem      Size  Used Avail Use% Mounted on
s3fs            256T     0  256T   0% /mnt/s3data

$ time cat /mnt/s3data/large-report.csv > /dev/null
real    0m2.847s
```

The reported size is effectively unbounded (S3's actual capacity), and even a simple read shows
measurable latency compared to local disk — both worth explaining to a team unfamiliar with what's
actually happening underneath a mounted bucket.

## Do / Don't

| Do | Don't |
|---|---|
| Use `s3fs`/`blobfuse` for compatibility with existing file-oriented tooling | Use it for a new, performance-sensitive workload |
| Authenticate via an IAM instance role | Store an access key in the mount options |
| Understand write consistency is weaker than a real filesystem | Assume atomic, immediately-consistent writes |

## Common mistakes

- Choosing `s3fs` for a new, performance-sensitive application instead of calling the object
  storage SDK directly, then being surprised by the latency.
- Assuming a mounted bucket behaves exactly like a real filesystem for concurrent writes or partial
  file updates, when object storage's consistency model is weaker.
- Storing a static access key in mount options instead of using the instance's IAM role.

## Best practices

- Reserve `s3fs`/`blobfuse`-style mounts for compatibility with legacy or file-oriented tooling,
  not new development.
- Authenticate via IAM instance roles, never a stored key in mount configuration.
- Benchmark before committing to a mounted-storage approach for anything with meaningful I/O
  volume — the FUSE translation overhead is often the deciding factor.

## Exercises

1. Explain why object storage isn't a real filesystem, and what a FUSE tool like `s3fs` actually
   does to bridge that gap.
2. Compare the expected performance of many small file writes through `s3fs` versus a native SDK
   call batching the same writes.
3. Identify one legitimate use case for mounting object storage and one situation where a native
   SDK would clearly be the better choice.

## Quiz

**Q: What kind of tool is `s3fs`, and what kernel facility does it rely on?**
<details><summary>Show answer</summary>
A FUSE (Filesystem in Userspace) tool that translates standard filesystem operations into object
storage API calls.
</details>

**Q: Why is `s3fs` generally a poor choice for a new, performance-sensitive workload?**
<details><summary>Show answer</summary>
Every filesystem operation against the mount becomes a real network API call, making it
significantly slower than local disk or a native SDK, especially for many small operations.
</details>

**Q: How should a mounted-storage tool like `s3fs` authenticate on a cloud instance?**
<details><summary>Show answer</summary>
Via the instance's IAM role, the same short-lived credential mechanism used everywhere else on the
instance — not a static access key stored in mount options.
</details>

## Interview questions

- When would you choose `s3fs` over calling the S3 SDK directly, and what are the tradeoffs? →
  [Full answer in Senior/Expert Interview Questions](../../interview-questions/senior.md)

## Key Takeaways

- Tools like `s3fs` and `blobfuse` make object storage appear as a mounted filesystem via FUSE, a
  userspace translation layer, not a real filesystem.
- Every operation against the mount becomes a network API call, meaning meaningfully higher
  latency and weaker consistency than local or network-block storage.
- Authenticate via IAM instance roles, never a stored access key in mount options.
- Reserve mounted object storage for compatibility with existing file-oriented tooling, not new,
  performance-sensitive development.

## Further Reading

- [s3fs-fuse GitHub Repository](https://github.com/s3fs-fuse/s3fs-fuse)
- [Azure Blob Storage: Mount with BlobFuse2](https://learn.microsoft.com/en-us/azure/storage/blobs/blobfuse2-what-is)

## Related topics

- [IAM and Instance Roles](iam-and-instance-roles.md)
- [Auto-Scaling and Custom Linux Images](auto-scaling-and-custom-linux-images-amis.md)
