---
title: "Troubleshooting"
description: "Module 33 of the Linux roadmap — a repeatable troubleshooting methodology applied to the most common production symptoms: disk full, high CPU/memory, services that won't start, permission denied, network connectivity, SSH connection failures, and boot issues."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
type: "module-index"
nextTopic: "33-troubleshooting/troubleshooting-methodology"
updatedAt: "2026-07-30"
canonicalUrl: "/docs/troubleshooting"
---

# Troubleshooting

Module 33 of 34 · Stage: Production Engineer · Previous: [28 Linux for Web Servers](../28-linux-for-web-servers/index.md)

This module is a repeatable diagnostic framework, then that framework applied to the seven
symptoms you'll actually hit most in production. It draws on nearly every earlier module — the
value here isn't new commands, it's the order you check things in and the discipline to fix the
real cause instead of the fastest workaround.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Troubleshooting Methodology](troubleshooting-methodology.md) | 🟢 Must Know |
| [Disk Full Issues](disk-full-issues.md) | 🟢 Must Know |
| [High CPU / Memory Issues](high-cpu-memory-issues.md) | 🟡 Good to Know |
| [Service Won't Start](service-wont-start.md) | 🟢 Must Know |
| [Permission Denied Issues](permission-denied-issues.md) | 🟢 Must Know |
| [Network Connectivity Issues](network-connectivity-issues.md) | 🟡 Good to Know |
| [SSH Connection Issues](ssh-connection-issues.md) | 🟢 Must Know |
| [Boot Issues and Rescue Mode](boot-issues-and-rescue-mode.md) | 🔴 Expert |

## What you should be able to do after this module

- Apply a repeatable methodology (define, gather evidence, hypothesize, test, fix, verify) to any
  unfamiliar problem, not just the seven covered here.
- Diagnose a full disk, high CPU/memory, or a failing service to its specific cause instead of a
  guess.
- Recognize why the fast fix (`chmod 777`, restarting blindly) often masks the real cause instead
  of fixing it.
- Work through network and SSH connectivity failures layer by layer instead of guessing which
  layer is at fault.
- Recover a system that won't boot using GRUB's rescue prompt and systemd's emergency/rescue targets.

## Known, intentional gaps in this module

- No canonical command pages were created for `grub-install`, `fsck`, or `dmesg` — no roadmap
  bullet names a specific command in backticks for the boot-issues topic, so they're shown
  illustratively, the same treatment given to Ansible/Terraform in Module 26.
- [Boot Issues and Rescue Mode](boot-issues-and-rescue-mode.md) has no `relatedLabs` or
  `relatedInterviewQuestions` entries yet — no existing lab or interview-question anchor covers
  boot/rescue-mode scenarios. This is a genuine content gap for a future expansion pass, not an
  oversight in this module's frontmatter.
- Several topics in this module intentionally do not re-teach material already owned elsewhere —
  [Permission Denied Issues](permission-denied-issues.md) points to Module 07's
  [Common Permission Errors](../07-permissions/common-permission-errors.md) for the full
  diagnostic checklist rather than duplicating it, per this documentation's "one concept, one
  owning page" rule.

**Previous module:** [28 Linux for Web Servers](../28-linux-for-web-servers/index.md)
**Next module:** [34 Final Revision →](../34-final-revision/index.md)
