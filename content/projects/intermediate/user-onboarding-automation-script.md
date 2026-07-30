---
title: "Project: Build a User and Permissions Onboarding Automation Script"
description: "An intermediate Linux project: build a script that automates creating a new team member's account, group memberships, SSH access, and permissions — the way a real ops team would."
type: "project"
tier: "intermediate"
careerRelevance: ["linux-administrator", "devops", "cybersecurity"]
updatedAt: "2026-07-30"
keywords: ["user onboarding script linux", "useradd automation project", "linux permissions automation project", "sysadmin scripting project"]
canonicalUrl: "/projects/intermediate/user-onboarding-automation-script"
---

# Project: Build a User and Permissions Onboarding Automation Script

🟡 Intermediate · Relevant for: Linux Administrator, DevOps, Cybersecurity

## Goal

Automate the repetitive, error-prone parts of adding a new team member to a shared Linux server:
creating their account, assigning the right groups, setting up SSH access, and applying the
correct permissions to shared project directories — consistently, every time.

## Requirements

- Takes a username and a role (e.g. `developer`, `read-only`) as input and creates the account
  accordingly, with different group memberships per role.
- Generates or accepts an SSH public key and installs it correctly with the right ownership and
  permissions on `~/.ssh`.
- Grants access to the correct shared directories for that role via group permissions, not by
  editing individual file permissions per user.
- Refuses to proceed (with a clear error) if the username already exists, rather than silently
  overwriting an existing account.
- Logs every action taken, so there's an audit trail of who was onboarded when and with what access.

## Suggested Approach

1. Design the role-to-group mapping on paper first — decide what "developer" vs "read-only"
   actually means in terms of Linux groups before writing any code.
2. Build account creation and group assignment first, testing that `id {username}` shows exactly
   the groups you expect for each role.
3. Add SSH key installation next, being careful about `~/.ssh` and `authorized_keys` permissions —
   SSH silently refuses to use keys with overly permissive file modes, which is a common bug here.
4. Add the "refuse if user exists" guard early, and test it explicitly — this is exactly the kind
   of safety check that's easy to forget until it causes a real problem.
5. Add logging last, once the core logic is solid, so the log format doesn't have to be
   redesigned every time the script's logic changes.

## Stretch Goals

- Add an offboarding counterpart script that disables an account and revokes access cleanly.
- Support reading a batch of users from a CSV file for bulk onboarding.
- Add a `--dry-run` mode that prints every action without executing it.
- Integrate a basic approval step (e.g. requiring a second argument confirming intent) before
  any destructive or account-creating action runs.

## Related Modules

- [Users](../../docs/08-users/index.md) — `useradd`, sudoers, account security
- [Groups](../../docs/09-groups/index.md) — group-based permission strategy
- [Permissions](../../docs/07-permissions/index.md) — applying correct permissions to shared directories
- [SSH](../../docs/13-ssh/index.md) — installing keys correctly

## Related topics

- [Permissions Cheat Sheet](../../cheatsheets/permissions.md)
- [SSH Key-Based Access Project (Beginner)](../beginner/ssh-key-based-access-to-a-personal-server.md)
- [Projects Hub](../index.md)
