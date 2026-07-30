---
title: "Log Analysis for Security Incidents"
description: "The same log files and tools from Module 16, read with an investigator's questions — reconstructing a timeline of what an attacker did from auth logs, system logs, and command history."
module: "27-linux-for-cybersecurity"
moduleTitle: "Linux for Cybersecurity"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview"]
relatedTopics: ["linux-for-soc-analysts", "privilege-escalation-basics-defensive-framing"]
relatedCommands: ["journalctl", "grep"]
careerRelevance: ["cybersecurity", "sre", "devops", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#reconstruct-timeline-from-logs"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "MITRE ATT&CK Framework", "url": "https://attack.mitre.org/"}]
nextTopic: "27-linux-for-cybersecurity/privilege-escalation-basics-defensive-framing"
prevTopic: "27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["log analysis security incident", "reconstruct attack timeline linux", "auth.log analysis", "security log correlation", "incident response logs linux"]
canonicalUrl: "/docs/linux-for-cybersecurity/log-analysis-for-security-incidents"
---

# Log Analysis for Security Incidents

🟡 Good to Know · Relevant for: Cybersecurity · SRE · DevOps · Platform

> **TL;DR:** Security incident log analysis is [Module 16's](../16-logs/index.md) logging skills
> applied with an investigator's specific questions — not "did this work" but "what exactly
> happened, in what order, and who did it" — using `journalctl`/`grep` to reconstruct a timeline
> instead of just confirming service health.

## What is it?

The practice of reading system and application logs to reconstruct what happened during a security
incident: when an attacker gained access, what they did, and what (if anything) they touched —
directly extending [Module 16's](../16-logs/index.md) logging fundamentals and
[Module 16's incident-reading topic](../16-logs/reading-logs-during-a-production-incident.md) with
a security-specific lens.

## Why does it exist?

An operational incident ("why is the service down") and a security incident ("did someone break
in, and what did they do") both start with logs, but a security investigation demands more rigor:
every claim needs to be traceable to a specific log line, because the output may end up supporting
a legal or compliance finding, not just an internal postmortem.

## Where is it used?

Reconstructing a timeline after a suspected compromise — confirming an attacker's initial access
vector, what commands they ran, whether they escalated privileges (see
[Privilege Escalation Basics](privilege-escalation-basics-defensive-framing.md)), and whether they
accessed or exfiltrated data.

## How it works

> 📊 Diagram: a timeline reconstruction combining three log sources side by side — `journalctl`
> authentication events, shell history, and application logs — each entry plotted on a shared
> timeline, with a highlighted sequence showing initial login, a suspicious command, and a
> privilege escalation attempt in chronological order.

Reconstructing a timeline means pulling from multiple log sources and correlating by timestamp:

```bash
# Authentication events around the suspected window
journalctl -u ssh --since "2026-07-29 02:00" --until "2026-07-29 04:00"

# Search across all logs for a specific indicator (an IP, a filename, a username)
journalctl --since "2026-07-29 02:00" | grep "198.51.100.77"

# Command history for the compromised account, if not already cleared
cat /home/deploy/.bash_history
```

`grep` (Module 06) applied against `journalctl` output is the standard way to pull every mention of
a specific indicator — an IP address, filename, or username — across the full breadth of the
journal rather than reading it linearly.

## Real-world example

An incident responder confirms a compromised account via SSH logs, then uses `grep` across the full
journal for that account's username and the source IP to find every subsequent action: a `sudo`
attempt that failed (a privilege escalation attempt, unsuccessful), followed nine minutes later by
a successful one after the attacker apparently found a different path — all reconstructed purely
from correlating timestamps across log entries, without needing any tool beyond what Modules 06
and 16 already taught.

## Syntax

No new syntax — `journalctl` and `grep` syntax are unchanged from Modules 16 and 06 respectively;
this page is about the investigative question, not new command flags.

## Commands

See [`journalctl`](../../commands/journalctl.md) (Modules 11/16) and [`grep`](../../commands/grep.md)
(Module 06) — both reused here for security-timeline reconstruction rather than extended with new
flags.

## Production example

```
$ journalctl --since "2026-07-29 02:00" | grep -i "deploy"
Jul 29 02:47:03 web-03 sshd[9931]: Accepted publickey for deploy from 198.51.100.77 port 51022
Jul 29 02:51:12 web-03 sudo[9955]:   deploy : user NOT in sudoers ; COMMAND=/usr/bin/cat /etc/shadow
Jul 29 03:00:41 web-03 sudo[10021]: deploy : COMMAND=/usr/bin/systemctl restart cron
```

A clear timeline: legitimate-looking login, a failed privilege-escalation attempt against
`/etc/shadow`, then a successful `sudo` action minutes later — the exact kind of sequence a security
timeline needs to establish.

## Do / Don't

| Do | Don't |
|---|---|
| Correlate multiple log sources by timestamp | Rely on a single log source for a full timeline |
| Preserve original log files before analysis | Edit or filter logs in place, losing the original record |
| Trace every claim to a specific log line | Draw conclusions logs don't actually support |

## Common mistakes

- Relying on a single log source (just SSH logs, say) instead of correlating across multiple
  sources for a complete timeline.
- Editing or filtering log files in place during analysis, losing the unaltered original record
  that a security finding may need to reference later.
- Drawing a conclusion that outpaces what the actual log evidence shows, weakening the credibility
  of the investigation's findings.

## Best practices

- Work from copies of original logs, never edit them in place, preserving the unaltered record.
- Correlate by timestamp across every available source before concluding what happened.
- Document exactly which log line supports each claim in a timeline, making the investigation's
  findings independently verifiable.

## Exercises

1. Use `journalctl --since`/`--until` to pull authentication events for a specific time window on a
   system you have access to.
2. Use `grep` across `journalctl` output to find every log entry mentioning a specific username.
3. Practice reconstructing a timeline from at least two different log sources for the same time
   window.

## Quiz

**Q: What's the key difference between operational incident log analysis and security incident log analysis?**
<details><summary>Show answer</summary>
Security analysis demands more rigor — every claim needs to be traceable to a specific log line,
since the findings may support a legal or compliance conclusion, not just an internal postmortem.
</details>

**Q: Why is editing logs in place during a security investigation a mistake?**
<details><summary>Show answer</summary>
It destroys the unaltered original record, which a security finding may need to reference or prove
authenticity of later.
</details>

**Q: How does `grep` help reconstruct a security timeline from `journalctl` output?**
<details><summary>Show answer</summary>
It finds every mention of a specific indicator (an IP, username, or filename) across the full
breadth of the journal, rather than reading entries linearly.
</details>

## Interview questions

- Walk through how you'd reconstruct a timeline of an attacker's actions from Linux logs alone. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Security log analysis reuses Module 16's logging fundamentals, applied with an investigator's
  rigor.
- Correlating multiple log sources by timestamp is what produces a credible, complete timeline.
- Working from unaltered copies of original logs preserves the record's integrity.
- Every claim in a security timeline should trace back to a specific, citable log line.

## Further Reading

- [MITRE ATT&CK Framework](https://attack.mitre.org/)

## Related topics

- [Linux for SOC Analysts](linux-for-soc-analysts.md)
- [Privilege Escalation Basics (Defensive Framing)](privilege-escalation-basics-defensive-framing.md)
