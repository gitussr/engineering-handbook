---
title: "Auditing: auditd, ausearch"
description: "Recording security-relevant kernel-level events (file access, syscalls, privileged commands) with the Linux Audit system, and querying that trail with ausearch."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["19-security/fail2ban"]
relatedTopics: ["fail2ban", "vulnerability-scanning-basics"]
relatedCommands: ["auditd", "ausearch"]
careerRelevance: ["cybersecurity", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#auditd-vs-syslog"]
relatedCheatsheet: ""
furtherReading: [{"label": "auditd(8) man page", "url": "https://man7.org/linux/man-pages/man8/auditd.8.html"}]
nextTopic: "19-security/vulnerability-scanning-basics"
prevTopic: "19-security/fail2ban"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["linux audit system", "auditd rules example", "ausearch examples", "auditd vs syslog"]
canonicalUrl: "/docs/security/auditing-auditd-ausearch"
---

# Auditing: auditd, ausearch

🔴 Expert · Relevant for: Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `auditd` records security-relevant kernel-level events — file access, syscalls,
> privileged command execution — according to configured rules. `ausearch` queries that recorded
> trail, answering "who did what, when" in a way ordinary application logs (Module 16) often
> can't.

## What is it?

The Linux Audit system: `auditd`, the daemon that records kernel-level security-relevant events
per configured rules, and `ausearch`, the tool for querying that recorded trail afterward.

## Why does it exist?

Application and service logs ([Module 16](../16-logs/index.md)) capture what an application chose
to log — not necessarily every security-relevant action at the kernel level. `auditd` operates
below the application layer, recording events like "this file was opened by this user" or "this
syscall was invoked" regardless of whether any application logged it — essential for compliance
requirements and forensic investigation after a suspected compromise.

## Where is it used?

Compliance-driven environments (PCI-DSS, HIPAA, and similar frameworks commonly require audit
trails), post-incident forensic investigation ("who accessed this file, and when"), and monitoring
privileged command usage on sensitive systems.

## How it works

> 📊 Diagram: a syscall/file-access event flowing from the kernel through the audit subsystem,
> checked against configured audit rules (watching a specific file path, or a specific syscall),
> written to `/var/log/audit/audit.log` in a raw, structured format, then queried afterward by
> `ausearch` using a simpler, human-friendly filter syntax.

**Configuring what gets audited — rules:**

```bash
# Watch a sensitive file for any write access
auditctl -w /etc/shadow -p wa -k shadow_changes

# Watch a directory
auditctl -w /etc/security -p wa -k security_configs
```

`-w` (watch), `-p` (permissions to watch: `r`ead, `w`rite, `x`ecute, `a`ttribute change), and `-k`
(a searchable key/tag for that rule) are the core building blocks. Persistent rules across
reboots go in `/etc/audit/rules.d/`.

**Querying recorded events — `ausearch`:**

```bash
ausearch -k shadow_changes
ausearch -ua 1000    # events by a specific user ID
ausearch -ts today    # events since today
```

`ausearch` translates the raw `/var/log/audit/audit.log` format into readable, filterable output —
by key, by user, by time range, or by syscall.

## Real-world example

A compliance audit requires proof of exactly who modified `/etc/passwd` and `/etc/shadow` over the
past quarter. Without `auditd`, this information may not exist anywhere — standard file
permissions and DAC don't log access history, only control it. With an `auditd` watch rule already
in place (`auditctl -w /etc/shadow -p wa -k shadow_changes`), `ausearch -k shadow_changes`
produces exactly the audit trail the compliance requirement demands, with user, timestamp, and
process for every recorded change.

## Syntax

```
auditctl -w PATH -p PERMISSIONS -k KEY
ausearch [-k KEY | -ua UID | -ts TIME]
```

## Commands

See [`auditd`](../../commands/auditd.md) and [`ausearch`](../../commands/ausearch.md) for full
references.

## Production example

```
$ sudo auditctl -w /etc/shadow -p wa -k shadow_changes
$ sudo ausearch -k shadow_changes -ts today
----
type=PATH msg=audit(1721990400.123:456): item=0 name="/etc/shadow" ...
type=SYSCALL msg=audit(1721990400.123:456): ... uid=0 ... comm="passwd" ...
```

A rule watching a sensitive file, then a query surfacing exactly which process (`passwd`) and user
(`uid=0`) touched it today.

## Do / Don't

| Do | Don't |
|---|---|
| Use `-k` to tag rules with a searchable key | Write untagged rules that are hard to query later |
| Persist important rules in `/etc/audit/rules.d/` | Add rules only via `auditctl` at runtime, losing them on reboot |
| Watch specific sensitive paths deliberately | Audit everything indiscriminately, generating unmanageable log volume |

## Common mistakes

- Adding audit rules only at runtime with `auditctl`, not realizing they're lost on the next
  reboot without also persisting them in `/etc/audit/rules.d/`.
- Auditing too broadly (every file, every syscall), generating enormous log volume that makes
  genuine investigation harder, not easier.
- Forgetting `-k` when creating a rule, making it much harder to query relevant events later with
  `ausearch`.

## Best practices

- Scope audit rules deliberately to genuinely sensitive paths and syscalls — comprehensive but
  targeted, not blanket coverage.
- Always tag rules with a meaningful `-k` key so `ausearch` queries stay simple and fast.
- Persist critical rules in `/etc/audit/rules.d/` so compliance-required auditing survives
  reboots without manual re-application.

## Exercises

1. Write an `auditctl` rule watching a file of your choice for write access, tagged with a key.
2. Query that rule's events with `ausearch -k KEY`.
3. Explain why application logs (Module 16) aren't a substitute for `auditd` in a compliance
   context.

## Quiz

**Q: What's the key difference between auditd and standard application/syslog logging?**
<details><summary>Show answer</summary>
`auditd` operates at the kernel level, recording events like file access and syscalls regardless
of whether any application chose to log them — application/syslog logging only captures what the
application itself writes.
</details>

**Q: What does the -k flag do when creating an audit rule?**
<details><summary>Show answer</summary>
It tags the rule with a searchable key, letting `ausearch -k KEY` retrieve exactly the events that
rule generated, rather than having to filter through everything.
</details>

**Q: Where should audit rules be placed to survive a reboot?**
<details><summary>Show answer</summary>
`/etc/audit/rules.d/` — rules added only via `auditctl` at runtime are lost on the next reboot.
</details>

## Interview questions

- Why might a compliance requirement specifically call for auditd rather than standard application
  logging? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `auditd` records kernel-level security-relevant events per configured rules — below the
  application logging layer.
- `ausearch` queries the recorded trail by key, user, or time range.
- Rules need `/etc/audit/rules.d/` persistence to survive reboots, and a `-k` tag to stay
  queryable.
- Auditing is essential for compliance and forensic investigation in ways ordinary application
  logs (Module 16) can't fully substitute for.

## Related topics

- [Fail2ban](fail2ban.md)
- [Vulnerability Scanning Basics](vulnerability-scanning-basics.md)
