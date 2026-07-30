---
title: "Lab: SSH Into a Cloud Instance and Recover from a Full Disk"
description: "A production-tier Linux lab: a cloud VM's disk is completely full and SSH itself is failing because of it — recover access and free space without a console reboot."
type: "lab"
tier: "production"
careerRelevance: ["sre", "devops", "cloud", "linux-administrator"]
updatedAt: "2026-07-30"
keywords: ["full disk recovery lab linux", "cloud vm disk full lab", "ssh fails disk full lab", "production linux lab disk"]
canonicalUrl: "/labs/production/recover-a-cloud-instance-from-a-full-disk"
---

# Lab: SSH Into a Cloud Instance and Recover from a Full Disk

🔴 Production · Relevant for: SRE, DevOps, Cloud, Linux Administrator

## Scenario

**Alert:** "`api-prod-03` disk at 100%. SSH connections are timing out or dropping immediately
after auth. Application is down. Recover the instance."

## Environment Setup

```bash
sudo dd if=/dev/zero of=/fill-disk bs=1M count=$(( $(df --output=avail / | tail -1) / 1024 - 50 )) 2>/dev/null
df -h /
```

This leaves the disk nearly completely full — enough to reproduce SSH session instability caused
by a full disk (SSH needs to write to disk for PAM/session bookkeeping) without fully locking you
out for this lab.

## Tasks

1. Get a working shell on the instance despite the disk pressure — note what actually breaks
   with SSH when disk is completely full, and why.
2. Confirm the disk is genuinely full and identify the largest consumer.
3. Free enough space to stabilize the instance.
4. Confirm the application/service can start again now that space is available.
5. Recommend one preventive measure so this doesn't recur silently next time.

## Hints

<details><summary>Show hint</summary>
A disk at true 100% can make even basic session operations (writing to `/var/log/wtmp`, PAM
session files) fail — if a normal SSH session won't complete, most cloud providers offer a
browser-based serial/console connection that doesn't depend on the same disk writes in the same
way. Once you have any shell, the `df` → `du` drill-down is the same as any disk investigation.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# If normal SSH is unstable, use the cloud provider's serial/console access instead — for this
# lab, simulate that by connecting locally instead of expecting a normal remote SSH login:

df -h /
# Filesystem  Size  Used Avail Use% Mounted on
# /dev/sda1    20G   20G     0 100% /

# Find the largest consumer
sudo du -sh /* 2>/dev/null | sort -rh | head -5
# The artificial /fill-disk file created in the setup step will show as the top consumer

# Free space immediately
sudo rm /fill-disk
df -h /

# Confirm the application can start again
sudo systemctl status checkout-api 2>/dev/null || echo "example: verify your actual app service here"

# Preventive recommendation for the ticket:
# "Add a disk-usage alert at 80% (not just 100%), well before SSH/session operations start
# failing — this incident should have been caught and resolved during business hours, not
# discovered only once the instance was nearly inaccessible."
```

The core lesson under real time pressure: don't fight a struggling SSH session repeatedly — a
disk-full instance often needs an alternate access path (console/serial), and once you're in, the
investigation is the same `df`/`du` process as any other disk-full ticket.
</details>

## What You Learned

- Why a completely full disk can degrade or break SSH sessions specifically, not just application
  behavior.
- That cloud providers' console/serial access exists precisely for situations where normal SSH
  is unreliable — know it exists before you need it at 2am.
- Why alerting at 80% disk usage, not 100%, is the actual fix — this incident is a monitoring gap
  as much as a disk-space problem.

## Related Modules

- [Storage](../../docs/15-storage/index.md)
- [Linux for Cloud](../../docs/25-linux-for-cloud/index.md)
- [SSH](../../docs/13-ssh/index.md)

## Related topics

- [Diagnose High Disk Usage Lab (Intermediate)](../intermediate/diagnose-high-disk-usage-and-free-space-safely.md)
- [Linux Commands Cheat Sheet](../../cheatsheets/linux-commands.md)
- [Labs Hub](../index.md)
