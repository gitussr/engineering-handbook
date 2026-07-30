---
title: "Lab: Investigate a Suspected Security Breach from Auth Logs"
description: "A production-tier Linux lab: unusual login activity was flagged on a production server, and you have to investigate the auth logs to determine whether the account was actually compromised."
type: "lab"
tier: "production"
careerRelevance: ["cybersecurity", "sre", "linux-administrator"]
updatedAt: "2026-07-30"
keywords: ["auth log investigation lab", "security incident lab linux", "ssh breach investigation exercise", "production linux lab security"]
canonicalUrl: "/labs/production/investigate-a-suspected-security-breach-from-auth-logs"
---

# Lab: Investigate a Suspected Security Breach from Auth Logs

🔴 Production · Relevant for: Cybersecurity, SRE, Linux Administrator

## Scenario

**Alert from a log-monitoring rule:** "Multiple failed SSH login attempts against `deploy-user`
on `prod-db-02` from an unfamiliar IP, followed by one successful login. Investigate immediately —
we need to know if this account is actually compromised."

## Environment Setup

```bash
sudo tee -a /var/log/auth.log > /dev/null <<'EOF'
Jul 30 01:58:03 prod-db-02 sshd[1001]: Failed password for deploy-user from 203.0.113.77 port 51000 ssh2
Jul 30 01:58:07 prod-db-02 sshd[1002]: Failed password for deploy-user from 203.0.113.77 port 51001 ssh2
Jul 30 01:58:15 prod-db-02 sshd[1003]: Failed password for deploy-user from 203.0.113.77 port 51002 ssh2
Jul 30 01:58:40 prod-db-02 sshd[1004]: Accepted password for deploy-user from 203.0.113.77 port 51010 ssh2
Jul 30 01:59:02 prod-db-02 sshd[1004]: pam_unix(sshd:session): session opened for user deploy-user
EOF
```

## Tasks

1. Confirm the pattern in the logs: how many failed attempts, from where, and what happened
   right after.
2. Treat the successful login as a suspected compromise, not a coincidence — determine what you'd
   check next to confirm or rule that out (account history, recent commands, new files/keys).
3. Decide and justify an immediate containment action for the account.
4. Write a short incident note: what you found, what you did, what's still open.

## Hints

<details><summary>Show hint</summary>
The pattern itself — a burst of failures immediately followed by one success from the same IP —
is the signature of either a successful brute-force or a credential that was already known to the
attacker (leaked/reused password). Both are bad; the difference matters for the follow-up, but not
for the immediate containment decision.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Step 1 — confirm the pattern
grep "deploy-user" /var/log/auth.log | tail -10
# 3 failed attempts from 203.0.113.77, ~5-10 seconds apart, then one Accepted from the same IP

# Step 2 — what would confirm/rule out compromise (run these in a real incident):
last deploy-user                     # recent login history for the account
sudo -u deploy-user history           # shell history, if accessible/not cleared
find /home/deploy-user -newer /var/log/auth.log -type f 2>/dev/null   # recently modified files
cat /home/deploy-user/.ssh/authorized_keys   # check for an attacker-added key

# Step 3 — immediate containment: disable the account and rotate its credentials NOW,
# don't wait for full investigation to finish first
sudo passwd -l deploy-user
sudo usermod -s /usr/sbin/nologin deploy-user
# If deploy-user is used for automated deploys, this also means coordinating with whoever owns
# that pipeline — containment has a real operational cost here, and that's expected.

# Step 4 — incident note
# "Detected successful SSH login to deploy-user from unfamiliar IP 203.0.113.77 immediately
# following 3 failed attempts — treated as suspected compromise per policy. Account locked and
# shell disabled as immediate containment. Still open: confirm whether any files/keys were
# modified during the ~1-minute session window, and whether this IP appears against any other
# accounts."
```

The judgment call this lab is really testing: containment (locking the account) happens
*immediately*, before the investigation into what the attacker actually did is finished — you
don't wait for full certainty to stop the bleeding.
</details>

## What You Learned

- Why a successful login immediately following failed attempts from the same IP is treated as a
  suspected compromise by default, not investigated as a maybe.
- The difference between containment (immediate, before full investigation) and root-cause
  investigation (can take longer, happens in parallel).
- What to check next after containment: login history, shell history, modified files, and
  SSH `authorized_keys` for attacker-added persistence.

## Related Modules

- [Security](../../docs/19-security/index.md)
- [Linux for Cybersecurity](../../docs/27-linux-for-cybersecurity/index.md)
- [Logs](../../docs/16-logs/index.md)

## Related topics

- [Scenario-Based Interview Questions: suspicious login activity](../../interview-questions/scenario-based.md#suspicious-login-activity-in-auth-logs)
- [Cybersecurity Roles Interview Questions](../../interview-questions/company-wise/cybersecurity-roles.md)
- [Labs Hub](../index.md)
