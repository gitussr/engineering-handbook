---
title: "Server Hardening Checklist"
description: "A practical, ordered checklist that pulls together SSH, users, permissions, packages, and firewall rules already taught in earlier modules into one hardening pass."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["19-security/linux-security-model-overview"]
relatedTopics: ["linux-security-model-overview", "firewall-configuration-in-depth"]
relatedCommands: []
careerRelevance: ["cybersecurity", "devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#server-hardening-steps"]
relatedCheatsheet: ""
furtherReading: [{"label": "CIS Benchmarks", "url": "https://www.cisecurity.org/cis-benchmarks"}]
nextTopic: "19-security/firewall-configuration-in-depth"
prevTopic: "19-security/linux-security-model-overview"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["linux server hardening checklist", "harden ubuntu server", "harden centos server", "ssh hardening checklist"]
canonicalUrl: "/docs/security/server-hardening-checklist"
---

# Server Hardening Checklist

🟢 Must Know · Relevant for: Cybersecurity · DevOps · Linux Administrator · SRE

> **TL;DR:** Hardening a server is applying what's already been taught, deliberately and in
> order: disable root SSH login, enforce key-only auth, keep packages current, enable a
> default-deny firewall, and remove anything unnecessary. This page is the checklist, not new
> material.

## What is it?

An ordered, practical checklist for hardening a fresh Linux server, assembled entirely from
techniques already taught in earlier modules — this page's job is sequencing and completeness,
not introducing new commands.

## Why does it exist?

Knowing individual hardening techniques (SSH key-only auth, firewall rules, package updates) in
isolation doesn't guarantee they're all actually applied to a given server. A checklist turns
scattered knowledge into a repeatable process that can be run against every new server
consistently, and audited against later.

## Where is it used?

Provisioning any new server before it goes into production, and periodically re-auditing existing
servers — especially internet-facing ones — against the same list to catch configuration drift.

## How it works

> 📊 Diagram: a numbered checklist rendered as a vertical flow, each item with a small icon
> linking to its owning module (SSH icon → Module 13, firewall icon → Module 12/this module,
> package icon → Module 14, user icon → Module 08) — visually reinforcing that hardening applies
> existing knowledge rather than teaching new mechanisms.

**The checklist, in a practical order:**

1. **Disable root SSH login and enforce key-only authentication** — see
   [SSH Hardening](../13-ssh/ssh-hardening-disable-root-login-key-only-auth.md). Do this early;
   it's the single highest-leverage step against remote brute-force attacks.
2. **Create a non-root user with `sudo` access** — see [Module 08: Users](../08-users/index.md) —
   so root login is never needed at all, not just disabled.
3. **Enable a default-deny firewall, allowing only necessary ports** — see
   [Module 12](../12-networking/firewalls-iptables-firewalld-ufw.md) and this module's
   [Firewall Configuration in Depth](firewall-configuration-in-depth.md). Always allow the
   management port (SSH) *before* enabling — see the `ufw`/`firewall-cmd` Security Considerations
   from Module 12.
4. **Keep packages current** — see [Module 14: Package Managers](../14-package-managers/index.md)
   — unpatched software is one of the most common initial access vectors.
5. **Remove or disable unnecessary services** — every running service is attack surface; if it's
   not needed, it shouldn't be running (check with `systemctl list-units`, [Module 11](../11-services/index.md)).
6. **Review file permissions on sensitive files** — see [Module 07](../07-permissions/index.md) —
   especially anything world-writable that shouldn't be.
7. **Consider enabling MAC enforcement** (SELinux/AppArmor) — see
   [SELinux and AppArmor](selinux-and-apparmor.md) — for defense beyond DAC permissions alone.
8. **Set up intrusion prevention** (Fail2ban) — see [Fail2ban](fail2ban.md) — to automatically
   block repeated failed login attempts.

## Real-world example

A team provisions a new server, remembers to disable root SSH login, but forgets to enable the
firewall before deploying — leaving a database port open to the entire internet for several days
until a routine audit catches it. Following a fixed, ordered checklist (rather than relying on
memory for a list of independent steps) is exactly what would have caught this gap immediately
during provisioning instead of days later during an audit.

## Syntax

No new syntax — this page sequences existing commands from earlier modules.

## Commands

No new command example on this page — see [Linux Security Model Overview](linux-security-model-overview.md)
for context, or [Firewall Configuration in Depth](firewall-configuration-in-depth.md) for the
next new command usage in this module.

## Production example

```
$ systemctl list-units --type=service --state=running | wc -l
14

$ ufw status
Status: active
22/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
```

A quick audit snapshot: how many services are actually running, and confirming the firewall is
both active and scoped to only necessary ports.

## Do / Don't

| Do | Don't |
|---|---|
| Apply hardening steps in a fixed, repeatable order on every new server | Rely on memory for an unordered list of "things to remember" |
| Allow the SSH port before enabling a default-deny firewall | Enable a firewall before confirming the management port is allowed |
| Periodically re-audit existing servers against the same checklist | Treat hardening as a one-time task done only at provisioning |

## Common mistakes

- Enabling a default-deny firewall before allowing the SSH (or other management) port, locking
  yourself out of a remote server with no console access.
- Hardening SSH and the firewall but leaving default/unnecessary services running, unnecessarily
  expanding attack surface.
- Treating hardening as a one-time checklist run at provisioning, then never re-auditing as
  configuration drifts over the server's life.

## Best practices

- Bake this checklist into provisioning automation (configuration management, a
  [shell script](../18-shell-scripting/real-automation-scripts.md)) rather than performing it
  manually and inconsistently per server.
- Re-run the checklist (or an automated audit against it) periodically, not just at initial setup.
- Prioritize SSH hardening and firewall configuration first — they close off the most common
  remote attack vectors before anything else matters.

## Exercises

1. Write out this checklist in your own words as a script's comment-only skeleton (no actual
   commands yet) to plan an automation script.
2. Explain why disabling root SSH login should happen before enabling the firewall, not after.
3. Identify which module already taught each checklist item, and note anything you're unsure
   about revisiting.

## Quiz

**Q: What's the correct order for enabling SSH hardening and the firewall — which comes first, and why?**
<details><summary>Show answer</summary>
Confirm the SSH/management port is allowed in the firewall configuration before enabling a
default-deny firewall — reversing the order risks locking yourself out with no remaining access
path.
</details>

**Q: Why should unnecessary services be removed or disabled as part of hardening?**
<details><summary>Show answer</summary>
Every running service is additional attack surface — a vulnerability in a service that isn't even
needed is still a real risk if it's left running.
</details>

**Q: Is hardening a one-time task performed only at server provisioning?**
<details><summary>Show answer</summary>
No — configuration drifts over time, so hardening should be periodically re-audited against the
same checklist, not treated as complete after the initial setup.
</details>

## Interview questions

- Walk through the steps you'd take to harden a freshly provisioned Linux server. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Hardening applies existing knowledge (SSH, users, firewall, packages, permissions) in a
  deliberate, ordered checklist — it isn't new material on its own.
- SSH hardening and firewall configuration are the highest-leverage first steps.
- Always allow the management port before enabling a default-deny firewall.
- Hardening should be automated and periodically re-audited, not a one-time manual task.

## Related topics

- [Linux Security Model Overview](linux-security-model-overview.md)
- [Firewall Configuration in Depth](firewall-configuration-in-depth.md)
