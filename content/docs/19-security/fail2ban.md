---
title: "Fail2ban"
description: "Automatically banning repeat offenders by watching log files for failed login patterns — a log-driven complement to ufw limit and firewalld rich rules."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["19-security/selinux-and-apparmor"]
relatedTopics: ["firewall-configuration-in-depth", "auditing-auditd-ausearch"]
relatedCommands: []
careerRelevance: ["cybersecurity", "devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#fail2ban-vs-ufw-limit"]
relatedCheatsheet: ""
furtherReading: [{"label": "Fail2ban Wiki", "url": "https://github.com/fail2ban/fail2ban/wiki"}]
nextTopic: "19-security/auditing-auditd-ausearch"
prevTopic: "19-security/selinux-and-apparmor"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["fail2ban explained", "fail2ban ssh jail", "fail2ban vs ufw limit", "fail2ban-client status"]
canonicalUrl: "/docs/security/fail2ban"
---

# Fail2ban

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · Linux Administrator · SRE

> **TL;DR:** Fail2ban watches log files (like `/var/log/auth.log`) for repeated failed login
> patterns and automatically firewalls-off the offending address for a configurable time —
> broader and more configurable than `ufw limit`'s built-in rate limiting, at the cost of an
> extra service to run.

## What is it?

A service that monitors log files for patterns indicating malicious activity — repeated failed
SSH logins being the most common case — and automatically creates a temporary firewall ban against
the offending source address.

## Why does it exist?

Automated brute-force login attempts against SSH and other exposed services are constant on any
internet-facing server. Manually watching logs and banning addresses doesn't scale; Fail2ban
automates the same detect-and-block logic that
[Firewall Configuration in Depth](firewall-configuration-in-depth.md)'s `ufw limit` does more
simply, but with configurable log patterns ("jails") across many different services, not just a
single connection-rate threshold.

## Where is it used?

Any internet-facing server with SSH (or another authentication-based service — a mail server, a
web application login) exposed to the internet, where automated brute-force attempts are expected
and constant, not hypothetical.

## How it works

> 📊 Diagram: a pipeline — a log file (`/var/log/auth.log`) being tailed by Fail2ban, matched
> against a named "jail" pattern (e.g. `sshd` failed-password lines), a counter tracking repeated
> matches from the same address within a time window, and once a threshold is crossed, an
> automatic firewall rule (via `iptables`/`firewalld`/`ufw` underneath) banning that address for a
> configured duration.

**Core concepts:**

- **Jail** — a named configuration watching a specific log file for a specific failure pattern
  (the `sshd` jail watches for failed SSH login attempts, by default).
- **Filter** — the actual pattern/regex used to recognize a failure line in the log.
- **Ban action** — what happens once the threshold is crossed, typically inserting a temporary
  deny rule into the underlying firewall (`iptables`, `firewalld`, or `ufw` — whichever the system
  uses).
- **Ban time** — how long the ban lasts before the address is automatically unbanned; can be
  configured to increase for repeat offenders.

Configuration lives in `/etc/fail2ban/jail.local` (an override file layered on top of the
package's defaults in `jail.conf`, so upgrades don't overwrite local customization).

## Real-world example

A server without Fail2ban shows thousands of failed SSH login attempts per day in its auth log,
each one an automated bot trying common username/password combinations — mostly harmless
individually, but a constant background risk and a source of log noise that makes real incidents
harder to spot. Enabling Fail2ban's default `sshd` jail means any single address making repeated
failed attempts is automatically banned after a handful of tries, cutting both the ongoing risk
and the log noise dramatically, with no per-incident manual intervention required.

## Syntax

No new syntax — Fail2ban is configured via files (`jail.local`), not a syntax of its own; its
control command (`fail2ban-client status`) is not given a canonical page in this documentation
(see this module's [intentional gaps](index.md)).

## Commands

No canonical command page exists for `fail2ban-client` — this roadmap bullet is scoped as a
concept, not a named command (see this module's [intentional gaps](index.md)); its status/control
commands are shown illustratively below.

## Production example

```
$ fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 3
|  |- Total failed:     847
|  `- Journal matches:  _SYSTEMD_UNIT=sshd.service + _COMM=sshd
`- Actions
   |- Currently banned: 2
   |- Total banned:     56
   `- Banned IP list:   203.0.113.7 198.51.100.4
```

A snapshot showing how much brute-force activity Fail2ban has already absorbed automatically —
847 failed attempts total, 56 addresses banned over the jail's lifetime.

## Do / Don't

| Do | Don't |
|---|---|
| Configure overrides in `jail.local`, not `jail.conf` | Edit `jail.conf` directly, losing changes on the next package upgrade |
| Enable Fail2ban alongside firewall rate-limiting for defense in depth | Treat Fail2ban and `ufw limit` as redundant — they're complementary |
| Review banned-IP lists periodically for patterns | Assume Fail2ban's default jails cover every service you're exposing |

## Common mistakes

- Editing `jail.conf` directly instead of `jail.local`, losing all customization the next time the
  package updates.
- Assuming Fail2ban is watching a service it isn't configured to — jails must be explicitly
  enabled for each service/log source that needs coverage.
- Setting an overly short ban time that provides little real deterrent against sustained automated
  attacks.

## Best practices

- Always customize in `jail.local`, an override layer, never the package-managed `jail.conf`.
- Enable jails for every internet-facing authentication surface, not just SSH — mail, web app
  logins, and anything else with a login log Fail2ban can parse.
- Use Fail2ban and firewall-level rate limiting (`ufw limit`, or an `iptables` custom chain from
  [Firewall Configuration in Depth](firewall-configuration-in-depth.md)) together — Fail2ban's
  configurable log-pattern matching plus the firewall's built-in rate limiting cover slightly
  different attack shapes.

## Exercises

1. Explain the difference between a Fail2ban "jail" and a "filter."
2. Describe why `jail.local` is preferred over editing `jail.conf` directly.
3. Compare Fail2ban to `ufw limit` — when would you want both running simultaneously?

## Quiz

**Q: What is a Fail2ban jail?**
<details><summary>Show answer</summary>
A named configuration that watches a specific log file for a specific failure pattern (e.g. the
`sshd` jail watching for failed SSH login attempts) and triggers a ban once a threshold is
crossed.
</details>

**Q: Why should Fail2ban customization go in jail.local instead of jail.conf?**
<details><summary>Show answer</summary>
`jail.conf` is the package-managed default file, overwritten on upgrades; `jail.local` is an
override layer specifically designed to survive package updates.
</details>

**Q: How does Fail2ban's approach differ from ufw's built-in limit feature?**
<details><summary>Show answer</summary>
Fail2ban watches configurable log patterns across many different services ("jails"), while `ufw
limit` is a simpler, built-in connection-rate threshold for a specific port — Fail2ban is more
configurable but requires running an additional service.
</details>

## Interview questions

- How does Fail2ban work, and how does it differ from a firewall's built-in rate limiting? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Fail2ban watches logs for failure patterns ("jails") and automatically bans offending addresses
  via the underlying firewall.
- Always customize in `jail.local`, never the package-managed `jail.conf`.
- Fail2ban and firewall-level rate limiting (`ufw limit`) are complementary, not redundant.
- Enable jails for every exposed authentication surface, not just SSH.

## Related topics

- [Firewall Configuration in Depth](firewall-configuration-in-depth.md)
- [Auditing: auditd, ausearch](auditing-auditd-ausearch.md)
