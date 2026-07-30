---
title: "SELinux and AppArmor"
description: "The two competing Mandatory Access Control implementations for Linux — policy-and-label-based (SELinux) vs. path-based (AppArmor) — and how each constrains a process beyond DAC permissions."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["19-security/firewall-configuration-in-depth"]
relatedTopics: ["linux-security-model-overview", "fail2ban"]
relatedCommands: []
careerRelevance: ["cybersecurity", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#selinux-enforcing-vs-permissive"]
relatedCheatsheet: ""
furtherReading: [{"label": "SELinux Project Wiki", "url": "https://selinuxproject.org/page/Main_Page"}, {"label": "AppArmor Wiki", "url": "https://gitlab.com/apparmor/apparmor/-/wikis/Documentation"}]
nextTopic: "19-security/fail2ban"
prevTopic: "19-security/firewall-configuration-in-depth"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["selinux vs apparmor", "selinux enforcing permissive disabled", "apparmor profile modes", "getenforce setenforce"]
canonicalUrl: "/docs/security/selinux-and-apparmor"
---

# SELinux and AppArmor

🔴 Expert · Relevant for: Cybersecurity · Linux Administrator · SRE

> **TL;DR:** SELinux (RHEL-family default) labels every process and file with a security context
> and enforces policy rules between them. AppArmor (Debian/Ubuntu-family default) instead attaches
> a profile directly to a program by file path. Both implement Mandatory Access Control; neither
> is installed on the other family's default distro.

## What is it?

Two separate implementations of Mandatory Access Control (MAC — introduced in
[Linux Security Model Overview](linux-security-model-overview.md)) for Linux: SELinux and
AppArmor. Both constrain what a process can do beyond standard DAC permissions; they take
different approaches to how that constraint is expressed.

## Why does it exist?

DAC permissions alone can't stop a compromised process running as its legitimate owner (or root)
from doing anything that owner is allowed to do system-wide. MAC exists specifically to close that
gap — confining a process to only the specific actions its security policy allows, independent of
what its user/owner could otherwise do.

## Where is it used?

Any system where limiting the blast radius of a compromised service matters more than the
convenience of an unconfined system — increasingly the default posture on both RHEL-family
(SELinux, enabled out of the box) and many Debian/Ubuntu-family (AppArmor, also enabled by
default for select packages) production distributions.

## How it works

> 📊 Diagram: two contrasting mechanisms side by side — SELinux: a process and a file each shown
> with a security-context label (`type`), with a policy table deciding whether that process-type
> can interact with that file-type; AppArmor: a specific program's file path directly mapped to a
> named profile listing exactly what that program can access — no labeling of files themselves,
> only rules attached to programs by path.

**SELinux** — label-based, policy-driven:

- Every process and file carries a security *context* (commonly seen as a `type`, e.g.
  `httpd_t` for a web server process, `httpd_sys_content_t` for web content files).
- A policy defines which process types may interact with which file/resource types — access is
  denied unless the policy explicitly permits it.
- Modes: `Enforcing` (policy actively blocks disallowed actions), `Permissive` (violations are
  logged but not blocked — useful for testing a policy), `Disabled` (SELinux inactive entirely).
- Check current mode: `getenforce`. Change it temporarily: `setenforce 0` (permissive) /
  `setenforce 1` (enforcing).

**AppArmor** — path-based, profile-driven:

- A profile is attached directly to a specific executable by file path (e.g.
  `/usr/sbin/nginx`), listing exactly which files, capabilities, and network access that program
  may use.
- Modes: `enforce` (violations blocked), `complain` (violations logged but not blocked — the
  AppArmor equivalent of SELinux's permissive mode).
- Check status: `aa-status`. Switch a profile's mode: `aa-enforce`/`aa-complain`.

**The practical difference:** SELinux's labeling system is more granular and powerful but has a
steeper learning curve and a reputation (often unfairly, from cases where it was simply disabled
out of frustration) for being hard to troubleshoot. AppArmor's path-based profiles are simpler to
read and write, at the cost of being tied to a program's file path rather than a more abstract
security context.

## Real-world example

A compromised web server process on a RHEL-family system attempts to read `/etc/shadow` — a file
its Unix user technically doesn't own but that a misconfigured DAC permission might otherwise
allow. SELinux's policy denies the access anyway, because the web server process's security
context (`httpd_t`) was never granted permission to interact with the shadow file's type, entirely
independent of standard Unix ownership. The same class of protection on a Debian/Ubuntu system
would come from an AppArmor profile explicitly scoping what the web server binary can touch.

## Syntax

No single syntax spans both systems — see each system's own commands above.

## Commands

No canonical command pages exist for SELinux's (`getenforce`, `setenforce`, `sestatus`,
`semanage`) or AppArmor's (`aa-status`, `aa-enforce`, `aa-complain`) commands — this roadmap
bullet is scoped as a concept, not a set of named commands (see this module's
[intentional gaps](index.md)); the commands above are shown illustratively.

## Production example

```
$ getenforce
Enforcing

$ aa-status
apparmor module is loaded.
15 profiles are loaded.
12 profiles are in enforce mode.
3 profiles are in complain mode.
```

Two quick status checks — only one of these commands will exist on any given system, depending
on which MAC implementation that distro defaults to.

## Do / Don't

| Do | Don't |
|---|---|
| Check which MAC system (if any) is active before assuming a system's security posture | Assume every Linux system has MAC enabled and enforcing by default |
| Use permissive/complain mode to test a new policy safely | Set enforcing/enforce mode blind, without reviewing what it will block first |
| Investigate and fix a denial with policy tools | Disable SELinux/AppArmor entirely to make an inconvenient denial go away |

## Common mistakes

- Disabling SELinux or AppArmor entirely the moment it blocks something inconvenient, instead of
  diagnosing and fixing the specific policy/profile issue — the single most common way MAC's real
  protection gets silently lost in production.
- Assuming a RHEL-family system's SELinux configuration transfers to a Debian/Ubuntu system, not
  realizing they use entirely different mechanisms (and vice versa for AppArmor).
- Switching straight to enforcing/enforce mode for a new policy without first testing in
  permissive/complain mode to catch unintended denials.

## Best practices

- Never disable SELinux/AppArmor as a first response to a denial — use permissive/complain mode
  and logs to diagnose the specific rule causing it, then adjust the policy.
- Know which MAC system (if either) a target distro defaults to before assuming its behavior.
- Treat MAC as a real, additional layer worth preserving — the earlier
  [Linux Security Model Overview](linux-security-model-overview.md) example (contained blast
  radius from a compromised web server) is exactly the protection lost when it's disabled out of
  convenience.

## Exercises

1. Check whether SELinux or AppArmor (or neither) is active on a system you have access to.
2. Explain the core difference between SELinux's label-based approach and AppArmor's path-based
   approach.
3. Describe why disabling MAC to resolve an inconvenient denial is considered a bad practice.

## Quiz

**Q: What's the fundamental difference between how SELinux and AppArmor apply their policies?**
<details><summary>Show answer</summary>
SELinux labels processes and files with security contexts and enforces policy between those
labels; AppArmor attaches profiles directly to programs by file path, with no labeling of files
themselves.
</details>

**Q: What does SELinux's permissive mode do?**
<details><summary>Show answer</summary>
It logs policy violations without actually blocking them — used to test a new or modified policy
safely before switching to enforcing mode.
</details>

**Q: Why is disabling SELinux/AppArmor to resolve a denial considered bad practice?**
<details><summary>Show answer</summary>
It removes an entire protective layer (Mandatory Access Control) rather than fixing the specific
policy issue — losing the contained-blast-radius protection MAC provides against a compromised
process.
</details>

## Interview questions

- What's the difference between SELinux's enforcing and permissive modes, and when would you use
  each? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- SELinux (RHEL-family default) is label/policy-based; AppArmor (Debian/Ubuntu-family default) is
  path/profile-based — both implement Mandatory Access Control.
- Modes exist to test safely: permissive (SELinux) / complain (AppArmor) log without blocking.
- Disabling MAC to resolve an inconvenient denial is a common, costly mistake — diagnose and fix
  the policy instead.
- Neither system is installed by default on the other family's distro — know which applies to a
  given target system.

## Related topics

- [Linux Security Model Overview](linux-security-model-overview.md)
- [Fail2ban](fail2ban.md)
