---
title: "Linux Security Model Overview"
description: "How Linux's layered defenses fit together — DAC permissions, users/groups, network filtering, and Mandatory Access Control — and why this module builds on nearly every prior one."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/real-automation-scripts"]
relatedTopics: ["server-hardening-checklist", "selinux-and-apparmor"]
relatedCommands: []
careerRelevance: ["cybersecurity", "devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#dac-vs-mac"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "19-security/server-hardening-checklist"
prevTopic: "18-shell-scripting/real-automation-scripts"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["linux security model", "dac vs mac linux", "defense in depth linux", "linux security layers"]
canonicalUrl: "/docs/security/linux-security-model-overview"
---

# Linux Security Model Overview

🟢 Must Know · Relevant for: Cybersecurity · DevOps · Linux Administrator · SRE

> **TL;DR:** Linux security is layered defense, not one mechanism: discretionary permissions
> (Module 07), user/group boundaries (Modules 08-09), network filtering (Module 12), and
> optionally Mandatory Access Control (SELinux/AppArmor) on top, constraining even root.

## What is it?

The combination of mechanisms Linux uses to control what a user, process, or piece of network
traffic is allowed to do — not a single feature, but several independent layers that this module
organizes into one picture.

## Why does it exist?

No single control is sufficient on its own: file permissions don't stop network attacks, a
firewall doesn't stop a compromised process from reading files it owns, and neither stops a
misconfigured service running as root from doing anything it wants. Defense in depth — several
independent, overlapping layers — means a failure or bypass of one layer doesn't mean total
compromise.

## Where is it used?

Every production system, but the stakes are highest for anything internet-facing: a public web
server, an SSH bastion host, a database accepting connections from application servers — anywhere
an attacker has a way in and the layers between that entry point and real damage matter.

## How it works

> 📊 Diagram: four concentric rings, outermost to innermost — network filtering (firewall rules,
> [Module 12](../12-networking/index.md)), authentication/access boundaries (SSH, users/groups,
> [Modules 08-09](../08-users/index.md)), discretionary file permissions
> ([Module 07](../07-permissions/index.md)), and innermost, Mandatory Access Control (SELinux/
> AppArmor) constraining even a process running as root — each ring labeled with what it stops and
> what it doesn't.

**The layers, from outside in:**

| Layer | Covered in | What it controls | What it doesn't control |
|---|---|---|---|
| Network filtering | [Module 12](../12-networking/index.md), deepened in [this module](firewall-configuration-in-depth.md) | Which traffic reaches a service at all | What a process does once traffic is let through |
| Authentication & user/group boundaries | [Module 08](../08-users/index.md), [Module 09](../09-groups/index.md), [Module 13](../13-ssh/index.md) | Who can log in, and as which identity | What that identity is allowed to do to specific files |
| Discretionary Access Control (DAC) | [Module 07: Permissions](../07-permissions/index.md) | File/directory owner-group-other permissions | Nothing — root bypasses DAC entirely by design |
| Mandatory Access Control (MAC) | [SELinux and AppArmor](selinux-and-apparmor.md), this module | Constrains what a process can do regardless of ownership — even root | Requires deliberate configuration; not enabled/enforced everywhere by default |

**DAC vs. MAC — the distinction that matters most:** Standard Linux permissions (Module 07) are
Discretionary Access Control — the file *owner* decides who can access it, and root can override
any of it. Mandatory Access Control (SELinux, AppArmor) is a separate, kernel-enforced policy layer
that constrains what a process can do based on a security policy, not file ownership — meaning
even a process running as root can be blocked from doing something the MAC policy doesn't allow,
which DAC alone can never achieve.

## Real-world example

A web server is compromised through a vulnerable application, giving an attacker code execution as
the `www-data` user. Without MAC in place, DAC permissions alone determine the blast radius —
whatever `www-data` can read/write, the attacker can too. With SELinux enforcing a policy that
confines the web server process to only the specific files and network actions it legitimately
needs, the same compromise is contained to a much smaller blast radius, even though the attacker
still has code execution as `www-data`. This is precisely why layered defense matters more than
any single strong layer.

## Syntax

No single syntax — this page is an organizing overview of layers taught individually elsewhere.

## Commands

No command example on this page — see [Server Hardening Checklist](server-hardening-checklist.md)
for the first one in this module.

## Production example

```
$ id www-data
uid=33(www-data) gid=33(www-data) groups=33(www-data)

$ getenforce
Enforcing
```

Two quick checks from two different layers: confirming a service account's identity (DAC/user
layer) and confirming whether MAC (SELinux) is actively enforcing on this system.

## Do / Don't

| Do | Don't |
|---|---|
| Treat network filtering, permissions, and MAC as independent, complementary layers | Rely on any single layer (a firewall, or file permissions alone) as sufficient protection |
| Assume a compromise will eventually happen, and design for contained blast radius | Design only for prevention, with no plan for what happens if one layer fails |
| Know which layer stops which class of problem | Assume all security controls overlap in coverage |

## Common mistakes

- Treating a firewall as sufficient security on its own, without considering what happens once
  traffic legitimately reaches an application.
- Forgetting that root bypasses DAC entirely — believing file permissions alone constrain a
  process running with root privileges.
- Not knowing whether MAC (SELinux/AppArmor) is even enabled/enforcing on a system, and therefore
  not accounting for it (or its absence) in a threat model.

## Best practices

- Design and audit security layer by layer — network, authentication, DAC, MAC — rather than
  treating "security" as one undifferentiated concern.
- Assume any single layer can fail or be bypassed, and evaluate what the next layer in would
  contain.
- Check `getenforce`/`aa-status` (see [SELinux and AppArmor](selinux-and-apparmor.md)) early when
  assessing an unfamiliar system's security posture — don't assume MAC is or isn't active.

## Exercises

1. List the four layers this page describes, in order from outermost to innermost.
2. Explain in one sentence why DAC alone doesn't stop a compromised root process from doing
   anything it wants.
3. Check whether SELinux or AppArmor is active on a system you have access to.

## Quiz

**Q: What's the key difference between DAC and MAC?**
<details><summary>Show answer</summary>
DAC (standard file permissions) is controlled by the file's owner, and root can override it
entirely. MAC (SELinux/AppArmor) is a separate, kernel-enforced policy that constrains what a
process can do regardless of ownership, even for root.
</details>

**Q: Why is defense in depth (multiple layers) more effective than one strong layer?**
<details><summary>Show answer</summary>
Because any single layer can fail or be bypassed — multiple independent layers mean a failure in
one doesn't automatically mean total compromise, since the next layer can still contain the
damage.
</details>

**Q: Does a firewall protect against a compromised application that legitimately accepts traffic on an open port?**
<details><summary>Show answer</summary>
No — a firewall controls which traffic reaches a service at all, not what that service (or an
attacker who compromises it) does once traffic is let through; that's the job of other layers like
permissions and MAC.
</details>

## Interview questions

- Explain the difference between DAC and MAC in the context of Linux security. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Linux security is layered: network filtering, authentication/user boundaries, DAC permissions,
  and optionally MAC — each stopping a different class of problem.
- DAC is owner-controlled and always bypassed by root; MAC is policy-enforced and constrains even
  root.
- No single layer is sufficient — design assuming any one layer can fail.
- This module deepens each layer in turn: firewalls, then MAC, then intrusion prevention and
  auditing.

## Related topics

- [Server Hardening Checklist](server-hardening-checklist.md)
- [SELinux and AppArmor](selinux-and-apparmor.md)
