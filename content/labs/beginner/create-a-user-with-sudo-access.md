---
title: "Lab: Create a User with Sudo Access"
description: "A beginner Linux lab, framed as a real onboarding ticket: create a new user account with sudo privileges, correctly and safely."
type: "lab"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops"]
updatedAt: "2026-07-30"
keywords: ["create user sudo access lab", "useradd lab linux", "linux sudoers lab", "beginner linux user management lab"]
canonicalUrl: "/labs/beginner/create-a-user-with-sudo-access"
---

# Lab: Create a User with Sudo Access

🟢 Beginner · Relevant for: Linux Administrator, DevOps

## Scenario

**Ticket #LX-102:** "New hire starts Monday and needs an account on the shared dev server with
sudo access, so they can install packages and manage services without needing root every time.
Set up their account before their start date."

## Environment Setup

- Access to a Linux server or VM as a user who already has sudo access (or root).
- No pre-existing account for the new hire.

## Tasks

1. Create a new user account for the new hire.
2. Set an initial password for the account (or note that they'll set one on first login).
3. Grant the account sudo access.
4. Verify the account can actually run a command with `sudo` — don't just assume the group
   membership worked.

## Hints

<details><summary>Show hint</summary>
On Debian/Ubuntu-based systems, adding a user to the `sudo` group is usually enough; on
RHEL/CentOS-based systems, it's typically the `wheel` group. Check `/etc/sudoers` (via `visudo`,
never edited directly) if you want to see exactly which group is granted access.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Create the account
sudo useradd -m -s /bin/bash newhire

# Set an initial password
sudo passwd newhire

# Grant sudo access (Debian/Ubuntu)
sudo usermod -aG sudo newhire

# Grant sudo access (RHEL/CentOS/Rocky)
sudo usermod -aG wheel newhire

# Verify — log in as the new user and confirm sudo works
su - newhire
sudo whoami
# Expected output: root
```

If `sudo whoami` prompts for a password and then fails, double check the group name matches
what's actually referenced in `/etc/sudoers` (`%sudo` or `%wheel`) with `sudo visudo`.
</details>

## What You Learned

- The difference between creating an account and granting it privileges — they're separate steps.
- Why the sudo-granting group differs between Debian-based and RHEL-based distros.
- Why you verify sudo access by actually using it, not by trusting the group-add command succeeded.

## Related Modules

- [Users](../../docs/08-users/index.md)
- [Groups](../../docs/09-groups/index.md)

## Related topics

- [Permissions Cheat Sheet](../../cheatsheets/permissions.md)
- [User Onboarding Automation Project](../../projects/intermediate/user-onboarding-automation-script.md)
- [Labs Hub](../index.md)
