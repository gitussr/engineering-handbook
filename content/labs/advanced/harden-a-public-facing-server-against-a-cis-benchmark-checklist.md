---
title: "Lab: Harden a Public-Facing Server Against a CIS Benchmark Checklist"
description: "An advanced Linux lab, framed as a real pre-launch ticket: apply a focused set of CIS-Benchmark-style hardening controls to a server before it goes live to the public internet."
type: "lab"
tier: "advanced"
careerRelevance: ["cybersecurity", "linux-administrator", "cloud-security"]
updatedAt: "2026-07-30"
keywords: ["cis benchmark lab linux", "server hardening lab exercise", "ssh hardening lab", "advanced linux lab security"]
canonicalUrl: "/labs/advanced/harden-a-public-facing-server-against-a-cis-benchmark-checklist"
---

# Lab: Harden a Public-Facing Server Against a CIS Benchmark Checklist

🔴 Advanced · Relevant for: Cybersecurity, Linux Administrator, Cloud Security

## Scenario

**Ticket #LX-303:** "This server goes live to the public internet next week. Security wants it
hardened against our baseline checklist before launch — SSH config, firewall, and unnecessary
services, at minimum. Apply the controls and confirm each one, don't just claim it's done."

## Environment Setup

A server or VM currently in its default, unhardened state, with SSH access as a sudo-capable user.

## Tasks

1. Harden SSH: disable root login, disable password authentication, confirm key-based access
   still works before closing your session.
2. Configure the firewall to allow only the ports the server actually needs (SSH and whatever
   application port it serves) and deny everything else by default.
3. Identify and disable at least one unnecessary service that increases attack surface without
   being needed.
4. Verify every control — don't just apply configuration and assume it took effect.

## Hints

<details><summary>Show hint</summary>
Test the new SSH restrictions in a *second* terminal session while keeping your original session
open — this is the one step that, skipped, turns a hardening ticket into a lockout incident.
`systemctl list-units --type=service --state=running` shows what's actually running and worth
questioning.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# --- SSH hardening ---
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# In a SECOND terminal, confirm key-based login as your non-root user still works
ssh -i ~/.ssh/id_ed25519 user@server-ip
# Only close your original session once this is confirmed

# --- Firewall: default-deny, allow only what's needed ---
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status verbose

# --- Disable an unnecessary service ---
systemctl list-units --type=service --state=running
# Example: disable a service the server doesn't actually need, e.g. an unused print service
sudo systemctl disable --now cups 2>/dev/null || true

# --- Verify every control ---
sudo sshd -T | grep -i "permitrootlogin\|passwordauthentication"
sudo ufw status verbose
systemctl is-active cups
```

Each verification command exists because "I edited the config" and "the running service is
actually enforcing the new config" are different claims — `sshd -T` shows the *effective* config
sshd would actually use, not just what's written in the file.
</details>

## What You Learned

- Why testing new SSH restrictions in a second session, before closing the first, is non-negotiable.
- How to configure a default-deny firewall policy and open only what's needed.
- Why verifying effective configuration (`sshd -T`, `ufw status`, `systemctl is-active`) matters
  more than trusting that an edited config file took effect.

## Related Modules

- [Security](../../docs/19-security/index.md)
- [SSH](../../docs/13-ssh/index.md)
- [Networking](../../docs/12-networking/index.md)

## Related topics

- [SSH Cheat Sheet](../../cheatsheets/ssh.md)
- [Harden and Audit a Server Project](../../projects/advanced/harden-and-audit-a-server-against-a-cis-style-checklist.md)
- [Labs Hub](../index.md)
