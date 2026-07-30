---
title: "User Account Security Hardening"
description: "Beyond the defaults — password aging policy, account lockout, disabling unused accounts, and auditing who actually has access."
module: "08-users"
moduleTitle: "Users"
stage: "beginner"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["08-users/root-account-best-practices"]
relatedTopics: []
relatedCommands: []
careerRelevance: ["linux-administrator", "cybersecurity", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#account-hardening-checklist"]
relatedCheatsheet: ""
furtherReading: [{"label": "CIS Benchmarks (account/authentication sections)", "url": "https://www.cisecurity.org/cis-benchmarks"}]
nextTopic: "09-groups/groups-overview"
prevTopic: "08-users/root-account-best-practices"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["password aging linux", "chage command", "account lockout policy linux", "audit user accounts"]
canonicalUrl: "/docs/users/user-account-security-hardening"
---

# User Account Security Hardening

🔴 Expert · Relevant for: Linux Administrator · Cybersecurity · SRE

> **TL;DR:** Hardening beyond the defaults means enforcing password aging, locking accounts after
> failed login attempts, removing unused accounts, and periodically auditing who actually has
> access versus who's supposed to — the gap between those two lists is where real incidents come
> from.

## What is it?

A set of practices going beyond basic account creation and `sudo` configuration: enforcing
password aging policy, locking accounts after repeated failed logins, removing stale/unused
accounts, and periodically auditing actual access against intended access.

## Why does it exist?

Accounts accumulate risk over time in ways that basic setup doesn't address: an employee who left
six months ago whose account was never removed, a service account with a password that's never
been rotated, no lockout policy letting an attacker brute-force indefinitely. These aren't
one-time setup concerns — they require ongoing policy and periodic review.

## Where is it used?

- Compliance-driven environments (finance, healthcare, government) where account hygiene is
  formally audited
- Post-incident reviews, where a stale or over-privileged account is a common root cause
- General production hardening for any server exposed to meaningful risk

## How it works

> 📊 Diagram: a simple lifecycle loop — Account Created → Actively Used → (branch) Still Needed?
> → Yes: Periodic Review/Password Rotation, No: Locked/Removed — emphasizing that hardening is a
> continuous process, not a one-time configuration.

| Practice | What it does | Relevant tool |
|---|---|---|
| Password aging | Forces periodic password changes | `chage` (sets max age, warning period) |
| Account lockout after failed attempts | Slows/stops brute-force login attempts | PAM modules (`pam_tally2`/`pam_faillock`, distro-dependent) |
| Removing unused accounts | Eliminates stale attack surface | `userdel`/`usermod -L`, tied to an offboarding process |
| Periodic access audits | Confirms actual access matches intended access | Manual review of `/etc/passwd`, `sudoers`, and group membership |

This is the natural extension of everything else in Module 08 — account creation
(`useradd`/`usermod`), password management (`passwd`), and privilege elevation (`sudo`/sudoers)
are all inputs into an ongoing hardening process, not one-time setup steps.

## Real-world example

A post-incident review finds the actual entry point was a contractor's account, created for a
three-month engagement two years earlier and never removed, with a password that had never been
rotated and was eventually guessed. None of the individual pieces (account creation, password
policy, sudo scoping) were wrong in isolation — the failure was the absence of an ongoing
review process to catch an account that had clearly outlived its purpose.

## Commands

No new command example on this page — this page synthesizes commands already covered throughout
Module 08 (`useradd`, `usermod -L`, `userdel`, `passwd`) into an ongoing hardening practice, plus
`chage` as a forward reference for password aging specifically.

## Production example

```
$ sudo chage -M 90 -W 7 deploy
$ sudo chage -l deploy
Last password change     : Jul 25, 2026
Password expires         : Oct 23, 2026
Password inactive        : never
```

`chage -M 90 -W 7` sets a 90-day maximum password age with a 7-day warning before expiry.

## Do / Don't

| Do | Don't |
|---|---|
| Tie account creation/removal to a formal onboarding/offboarding process | Let account cleanup happen only reactively, if at all |
| Enforce password aging on accounts that use password authentication | Leave passwords permanently unrotated indefinitely |
| Periodically audit actual access against what's actually still needed | Assume access granted once is still appropriate indefinitely |

## Common mistakes

- Treating account setup as a one-time task instead of an ongoing process requiring periodic
  review.
- Not tying account creation and removal to a formal onboarding/offboarding process, leading to
  stale accounts long after they're needed — the exact failure in the real-world example above.
- Setting a password aging policy but never actually checking whether it's being enforced or
  bypassed.

## Best practices

- Tie every account's existence to a real, current business reason — review and remove accounts
  that no longer have one.
- Enforce password aging (`chage`) on any account still using password authentication, alongside
  the SSH key-based auth covered in Module 13 for anything that can move away from passwords
  entirely.
- Schedule periodic access audits, not just incident-triggered ones — this is precisely the gap
  that turns a minor oversight into a real incident.

## Exercises

1. Check password aging settings for a test account using `chage -l`.
2. Set a maximum password age and warning period for a test account using `chage -M` and `-W`.
3. Explain in one sentence why a formal offboarding process matters as much as the technical
   hardening controls themselves.

## Quiz

**Q: What does `chage -M 90` do?**
<details><summary>Show answer</summary>
Sets the maximum password age to 90 days, after which the account will be required to change its
password.
</details>

**Q: Why is periodic access auditing important even when account controls are configured
correctly?**
<details><summary>Show answer</summary>
Controls configured correctly at creation time can still become stale over time — an account that
no longer needs its access, or a permission that was never revoked, only gets caught by ongoing
review, not by the original configuration.
</details>

## Interview questions

- Describe a checklist for hardening user accounts on a production Linux server beyond the
  defaults. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Hardening extends beyond initial setup into ongoing policy: password aging, lockout, and
  periodic access review.
- `chage` manages password aging; PAM modules handle lockout after failed attempts.
- Stale, unused accounts are a real and common root cause of security incidents.
- Account hygiene requires a continuous process, tied to onboarding/offboarding, not one-time
  configuration.

## Further Reading

- [CIS Benchmarks — account/authentication sections](https://www.cisecurity.org/cis-benchmarks)

## Related topics

- [Module 09: Groups](../09-groups/index.md)
- [Module 19: Security](../19-security/index.md)
