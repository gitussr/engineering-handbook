---
title: "Linux for SOC Analysts"
description: "A Security Operations Center analyst's daily work is largely Linux work — reading logs, checking process and network state, and triaging alerts on Linux hosts using tools this roadmap already covers."
module: "27-linux-for-cybersecurity"
moduleTitle: "Linux for Cybersecurity"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["26-linux-for-devops/building-a-cicd-pipeline-on-linux-servers"]
relatedTopics: ["log-analysis-for-security-incidents", "linux-for-penetration-testing-kali-overview"]
relatedCommands: ["journalctl", "ps", "ss"]
careerRelevance: ["cybersecurity", "sre", "platform", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#soc-analyst-linux-skills"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "SANS: Linux Security Fundamentals", "url": "https://www.sans.org/"}]
nextTopic: "27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview"
prevTopic: "26-linux-for-devops/building-a-cicd-pipeline-on-linux-servers"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["soc analyst linux skills", "security operations center linux", "linux for security analysts", "soc analyst daily tasks linux"]
canonicalUrl: "/docs/linux-for-cybersecurity/linux-for-soc-analysts"
---

# Linux for SOC Analysts

🟢 Must Know · Relevant for: Cybersecurity · SRE · Platform · Linux Administrator

> **TL;DR:** A SOC (Security Operations Center) analyst's job is mostly asking Linux questions
> under time pressure — who logged in, what process is running, what connection was made — using
> the exact same commands and log files from Modules 10-16, applied with an investigator's
> mindset instead of an administrator's.

## What is it?

The Linux-specific skill set a SOC analyst uses day to day: reading authentication and system logs
for suspicious activity, checking running processes and network connections for signs of
compromise, and correlating timestamps across multiple sources during an active investigation.

## Why does it exist?

Security alerts (from a SIEM, an EDR agent, or a manual tip) almost always point at a specific
Linux host and a specific window of time — the analyst's job is confirming what actually happened
on that host, which means reading the same logs and system state any Linux administrator would,
but with a different question in mind: not "is this working?" but "is this expected, or is this an
attacker?"

## Where is it used?

Triaging an alert about an unusual login, confirming whether a suspicious process is malware or a
false positive, and reconstructing a timeline of what an attacker did on a compromised host —
routine daily work in any SOC, and a skill nearly every cybersecurity, platform, and SRE role
touches at some point during an incident.

## How it works

> 📊 Diagram: a SOC analyst's investigation workflow shown as a funnel — a SIEM alert narrowing to
> a specific host and time window, then the analyst pulling `journalctl`/`/var/log/auth.log`
> (Module 16), `ps`/`ss` (Modules 10, 12) for current state, and `last`/`who` for login history,
> converging on a timeline of what actually happened.

An analyst investigating a suspicious login alert reaches for tools already covered in this
roadmap, just aimed at a security question:

```bash
# Who has logged in recently, and from where?
last -a

# Is anyone logged in right now?
who

# What does the authentication log actually show around that time?
journalctl -u ssh --since "2026-07-29 02:00" --until "2026-07-29 03:00"

# Any unexpected listening ports or established connections?
ss -tulpn
```

None of these are new commands — `journalctl` (Module 11/16) and `ss` (Module 12) are already
canonical pages; `last` and `who` are shown illustratively here the same way other login-history
tools have been introduced ahead of a dedicated page elsewhere in this roadmap.

## Real-world example

A SIEM fires an alert for a successful SSH login from an unfamiliar IP address at 2:47 AM. A SOC
analyst pulls `journalctl -u ssh` around that timestamp and finds the login used a valid key, not a
guessed password — ruling out brute force, but not ruling out a stolen key. Cross-referencing with
`last -a` shows the same IP has never logged in before, and `ss -tulpn` immediately after that login
shows a new listening port that wasn't there in the previous day's baseline — evidence that
escalates the alert from "unusual but maybe fine" to "active investigation," entirely from
commands already covered in Modules 10-16.

## Syntax

No new syntax — this page applies existing commands with an investigative framing rather than
introducing new ones.

## Commands

See [`journalctl`](../../commands/journalctl.md) (Modules 11/16) and [`ss`](../../commands/ss.md)
(Module 12) — both extended here conceptually with security-investigation use cases rather than
new flags; `last`/`who` are shown illustratively above.

## Production example

```
$ journalctl -u ssh --since "2026-07-29 02:00" --until "2026-07-29 03:00"
Jul 29 02:47:03 web-03 sshd[9931]: Accepted publickey for deploy from 198.51.100.77 port 51022

$ ss -tulpn | grep LISTEN
tcp   LISTEN  0  128  0.0.0.0:4444   0.0.0.0:*  users:(("nc",pid=9944,fd=3))
```

A successful key-based login from an unrecognized IP, followed immediately by an unexpected
listening port on a common reverse-shell port — exactly the kind of correlation that turns a single
alert into a confirmed incident.

## Do / Don't

| Do | Don't |
|---|---|
| Correlate multiple sources (logs, processes, connections) before concluding | Treat a single alert in isolation without checking current system state |
| Establish what "normal" looks like on a host before investigating | Assume any unfamiliar process or port is automatically malicious |
| Preserve evidence (logs, process state) before remediating | Kill a suspicious process before capturing what it was doing |

## Common mistakes

- Reacting to a single alert without correlating it against other Linux-level evidence (logs,
  processes, connections), missing the fuller picture an investigation needs.
- Killing a suspicious process or restarting a service immediately, destroying evidence needed to
  understand what actually happened before remediating.
- Not knowing what "normal" looks like on a given host, making it hard to recognize what's actually
  anomalous versus routine.

## Best practices

- Build familiarity with a host's normal baseline (typical logged-in users, typical listening
  ports, typical process list) before an incident happens, not during one.
- Preserve logs and system state (a process list snapshot, a copy of relevant log entries) before
  taking remediation action that might destroy evidence.
- Correlate across multiple sources — authentication logs, process state, network connections —
  rather than concluding from any single one alone.

## Exercises

1. Run `last -a` and `who` on a Linux system and explain what each reports.
2. Use `journalctl -u ssh` to find the most recent successful login on a system you have access to.
3. Explain why killing a suspicious process immediately, before investigating, can hurt an
   incident response.

## Quiz

**Q: What's the core difference between a SOC analyst's use of Linux commands and a sysadmin's?**
<details><summary>Show answer</summary>
The commands are the same, but the question being asked differs — an administrator asks "is this
working?" while an analyst asks "is this expected, or is this an attacker?"
</details>

**Q: Why is preserving evidence before remediating important during an investigation?**
<details><summary>Show answer</summary>
Killing a process or restarting a service immediately can destroy the exact evidence (process
state, open connections, logs) needed to understand what an attacker actually did.
</details>

**Q: Why does knowing a host's "normal" baseline matter for a SOC analyst?**
<details><summary>Show answer</summary>
Without a baseline, it's hard to tell what's genuinely anomalous versus routine — a listening port
or logged-in user that's completely normal on one host might be a strong indicator of compromise
on another.
</details>

## Interview questions

- What Linux commands would you use to investigate a suspicious login alert, and in what order? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- SOC analyst work reuses the exact Linux commands from Modules 10-16, applied with an
  investigative mindset.
- Correlating multiple sources (logs, processes, connections) is what turns a single alert into a
  confirmed finding.
- Preserving evidence before remediating is critical — killing a process too early can destroy
  what an investigation needs.
- Knowing a host's normal baseline in advance makes anomalies far easier to recognize during an
  actual incident.

## Further Reading

- [SANS: Linux Security Fundamentals](https://www.sans.org/)

## Related topics

- [Log Analysis for Security Incidents](log-analysis-for-security-incidents.md)
- [Linux for Penetration Testing (Kali Overview)](linux-for-penetration-testing-kali-overview.md)
