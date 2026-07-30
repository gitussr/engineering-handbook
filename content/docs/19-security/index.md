---
title: "Security"
description: "Module 19 of the Linux roadmap — the Linux security model, server hardening, firewall configuration in depth, SELinux/AppArmor, Fail2ban, auditing, vulnerability scanning, and SOC/pentest workflows."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
type: "module-index"
nextTopic: "19-security/linux-security-model-overview"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/security"
---

# Security

Module 19 of 34 · Stage: Professional · Previous: [18 Shell Scripting](../18-shell-scripting/index.md)

This module opens the Professional stage by pulling together and deepening security-relevant
material from nearly every earlier module — permissions (07), users (08-09), networking (12),
SSH (13) — into a layered security model, then goes further: advanced firewall patterns, Mandatory
Access Control, intrusion prevention, kernel-level auditing, vulnerability scanning, and how it
all applies differently to SOC and penetration-testing roles.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Linux Security Model Overview](linux-security-model-overview.md) | 🟢 Must Know |
| [Server Hardening Checklist](server-hardening-checklist.md) | 🟢 Must Know |
| [Firewall Configuration in Depth](firewall-configuration-in-depth.md) | 🟡 Good to Know |
| [SELinux and AppArmor](selinux-and-apparmor.md) | 🔴 Expert |
| [Fail2ban](fail2ban.md) | 🟡 Good to Know |
| [Auditing: auditd, ausearch](auditing-auditd-ausearch.md) | 🔴 Expert |
| [Vulnerability Scanning Basics](vulnerability-scanning-basics.md) | 🟡 Good to Know |
| [Security Workflows for SOC/Pentest Roles](security-workflows-for-soc-pentest-roles.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain Linux's layered security model and the difference between DAC and MAC.
- Apply a repeatable server-hardening checklist assembled from prior modules.
- Configure advanced firewall patterns: custom `iptables` chains, `firewalld` rich rules, `ufw`
  rate limiting, and each tool's emergency/panic mode.
- Explain the difference between SELinux's label-based and AppArmor's path-based approaches to
  Mandatory Access Control.
- Configure Fail2ban jails to automatically mitigate brute-force login attempts.
- Use `auditd`/`ausearch` to record and query kernel-level security-relevant events.
- Distinguish vulnerability scanning from penetration testing, and authenticated from
  unauthenticated scans.
- Recognize how the same Linux security fundamentals apply differently to SOC (defensive) and
  penetration-testing (offensive) workflows.

## Known, intentional gaps in this module

- Canonical command pages exist for [`auditd`](../../commands/auditd.md) and
  [`ausearch`](../../commands/ausearch.md) — the two commands explicitly named in this module's
  Auditing bullet.
- The existing [`iptables`](../../commands/iptables.md), [`firewall-cmd`](../../commands/firewall-cmd.md),
  and [`ufw`](../../commands/ufw.md) pages (Module 12) were extended with this module's advanced
  flags (custom chains, rich rules, rate limiting, panic modes) rather than duplicated into new
  pages — one command, one canonical page, same pattern as Module 16's `journalctl` extension.
- SELinux (`getenforce`, `setenforce`, `sestatus`), AppArmor (`aa-status`, `aa-enforce`,
  `aa-complain`), and Fail2ban (`fail2ban-client`) commands are covered illustratively within
  their topic pages but don't get separate canonical pages — these roadmap bullets name concepts,
  not specific commands in backticks, consistent with the scoping rule established in Module 15.
- Dedicated offensive-security scanning tools (`nmap`, vulnerability scanners) remain deferred to
  [Module 27: Linux for Cybersecurity](../27-linux-for-cybersecurity/index.md), where they receive
  full treatment alongside the broader security-specialist toolset.

**Previous module:** [18 Shell Scripting](../18-shell-scripting/index.md)
**Next module:** [20 Monitoring →](../20-monitoring/index.md)
