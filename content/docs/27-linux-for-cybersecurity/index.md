---
title: "Linux for Cybersecurity"
description: "Module 27 of the Linux roadmap — a career-branch module on Linux for SOC analysts, Kali/penetration testing, security log analysis, privilege escalation (defensive framing), nmap/tcpdump/tshark, and CIS Benchmark compliance hardening."
module: "27-linux-for-cybersecurity"
moduleTitle: "Linux for Cybersecurity"
stage: "production-engineer"
type: "module-index"
nextTopic: "27-linux-for-cybersecurity/linux-for-soc-analysts"
updatedAt: "2026-07-29"
canonicalUrl: "/docs/linux-for-cybersecurity"
---

# Linux for Cybersecurity

Module 27 of 34 · Stage: Production Engineer · Previous: [26 Linux for DevOps](../26-linux-for-devops/index.md)

The third career-branch module, buildable in any order relative to Modules 25, 26, and 28. This
module applies Linux fundamentals to security work: a SOC analyst's daily investigative use of
tools from Modules 10-16, what Kali Linux actually is (an ordinary Debian derivative, not special
hacking magic), reconstructing timelines from logs during a security incident, the common
Linux privilege-escalation vectors framed defensively, `nmap`/`tcpdump`/`tshark` for network
security work, and CIS Benchmarks for auditable compliance hardening.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Linux for SOC Analysts](linux-for-soc-analysts.md) | 🟢 Must Know |
| [Linux for Penetration Testing (Kali Overview)](linux-for-penetration-testing-kali-overview.md) | 🟡 Good to Know |
| [Log Analysis for Security Incidents](log-analysis-for-security-incidents.md) | 🟡 Good to Know |
| [Privilege Escalation Basics (Defensive Framing)](privilege-escalation-basics-defensive-framing.md) | 🔴 Expert |
| [Network Security Tools: nmap, tcpdump, Wireshark (CLI)](network-security-tools-nmap-tcpdump-wireshark-cli.md) | 🟡 Good to Know |
| [Hardening for Compliance: CIS Benchmarks](hardening-for-compliance-cis-benchmarks.md) | 🔴 Expert |

## What you should be able to do after this module

- Investigate a security alert using the same Linux log and process tools from Modules 10-16,
  correlating multiple sources into a timeline.
- Explain what Kali Linux actually is, and why authorization matters more than which distro a tool
  runs on.
- Recognize common Linux privilege-escalation vectors (sudoers, SUID, cron) well enough to audit
  and close them.
- Use `nmap`, `tcpdump`, and `tshark` for authorized network discovery, capture, and analysis.
- Explain why CIS Benchmarks exist and how they turn general hardening into auditable, scored
  compliance evidence.

## Known, intentional gaps in this module

- Canonical command pages exist for [`nmap`](../../commands/nmap.md),
  [`tcpdump`](../../commands/tcpdump.md), and [`tshark`](../../commands/tshark.md) — the three
  commands this module's roadmap bullet names, with `wireshark`'s backtick resolved specifically
  to `tshark`, since Wireshark's graphical application has no meaningful CLI mode of its own; a
  full explanation of this naming choice lives on the
  [network security tools topic page](network-security-tools-nmap-tcpdump-wireshark-cli.md).
- Kali Linux itself gets no canonical distro-specific treatment beyond this module's overview
  page — it's documented as an ordinary Debian derivative (Module 01), not a separate distro
  family requiring its own installation/administration modules.
- CIS Benchmark scanning tools (`oscap`/OpenSCAP) are shown illustratively without a canonical
  command page, since the roadmap bullet names a compliance concept ("CIS benchmarks"), not a
  specific command in backticks — the same treatment `lynis` received in Module 19.
- `last`/`who` (SOC analyst login-history tools) and GTFOBins (an external reference resource, not
  a command) are shown illustratively without canonical pages in this module.

**Previous module:** [26 Linux for DevOps](../26-linux-for-devops/index.md)
**Next module:** [28 Linux for Web Servers →](../28-linux-for-web-servers/index.md)
