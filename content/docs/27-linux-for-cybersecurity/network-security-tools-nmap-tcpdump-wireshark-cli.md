---
title: "Network Security Tools: nmap, tcpdump, Wireshark (CLI)"
description: "Discovering what's actually listening on a network with nmap, capturing traffic with tcpdump, and analyzing captures in depth with tshark — Wireshark's command-line companion."
module: "27-linux-for-cybersecurity"
moduleTitle: "Linux for Cybersecurity"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["27-linux-for-cybersecurity/privilege-escalation-basics-defensive-framing"]
relatedTopics: ["linux-for-penetration-testing-kali-overview", "hardening-for-compliance-cis-benchmarks"]
relatedCommands: ["nmap", "tcpdump", "tshark"]
careerRelevance: ["cybersecurity", "devops", "sre", "platform", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#nmap-vs-tcpdump-use-cases"]
relatedCheatsheet: "networking"
furtherReading: [{"label": "Nmap Reference Guide", "url": "https://nmap.org/book/man.html"}, {"label": "Wireshark/tshark Documentation", "url": "https://www.wireshark.org/docs/"}]
nextTopic: "27-linux-for-cybersecurity/hardening-for-compliance-cis-benchmarks"
prevTopic: "27-linux-for-cybersecurity/privilege-escalation-basics-defensive-framing"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["nmap vs tcpdump", "network security tools linux", "port scanning with nmap", "packet capture tcpdump", "tshark cli wireshark"]
canonicalUrl: "/docs/linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli"
---

# Network Security Tools: nmap, tcpdump, Wireshark (CLI)

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · SRE · Platform · Linux Administrator

> **TL;DR:** `nmap` answers "what's listening, and what is it?" from outside a host; `tcpdump`
> captures the actual traffic crossing an interface; `tshark` (Wireshark's command-line component)
> analyzes a capture in depth — three different layers of the same investigation, all extending
> [Module 12's](../12-networking/index.md) networking fundamentals.

## What is it?

Three complementary command-line tools for network investigation and security assessment: `nmap`
for discovering hosts and open ports, `tcpdump` for capturing live traffic, and `tshark` for
deep packet analysis — the terminal-based companion to the graphical Wireshark application the
roadmap bullet refers to as "wireshark (CLI)."

## Why does it exist?

Confirming what's actually reachable and running on a network — versus what's merely documented or
assumed — requires directly probing it, and confirming what traffic is actually crossing the wire
during an incident requires capturing it rather than inferring from logs alone. These tools answer
those two different but related questions.

## Where is it used?

Security assessments (with authorization — see
[Linux for Penetration Testing](linux-for-penetration-testing-kali-overview.md)) to discover
exposed services, incident response to capture and analyze suspicious traffic, and everyday network
troubleshooting by DevOps/SRE roles confirming whether a service is actually reachable and what its
traffic looks like on the wire.

## How it works

> 📊 Diagram: a target host shown with `nmap` probing it from outside (discovering open ports and
> service banners), `tcpdump` capturing traffic directly on an interface, and that same capture
> piped into `tshark` for detailed protocol-level analysis — each tool labeled with the specific
> layer of investigation it addresses.

**`nmap`** — discover what's listening on a host or network, from outside:

```bash
$ nmap -sV 10.0.1.15
```

**`tcpdump`** — capture actual traffic on an interface, useful when logs alone don't show what's
really happening on the wire:

```bash
$ sudo tcpdump -i eth0 port 443 -w capture.pcap
```

**`tshark`** — analyze a capture (live or from a `.pcap` file) at the protocol level, in more depth
than a quick `tcpdump` read:

```bash
$ tshark -r capture.pcap -Y "http.request"
```

These build directly on [Module 12's](../12-networking/ports-and-sockets-explained.md) ports and
sockets fundamentals — `nmap` is answering exactly the "what's listening on this port" question
that page introduced, just from a remote vantage point instead of locally via `ss`.

## Real-world example

An incident responder suspects a compromised host is exfiltrating data but isn't sure over what
protocol or to where. Running `tcpdump` on the host's interface captures the actual outbound
traffic, and piping the resulting `.pcap` file into `tshark` filtered for unusual destination IPs
reveals a steady stream of DNS queries to an unfamiliar domain, each query encoding small chunks of
data in its subdomain — a DNS tunneling exfiltration technique that would have been invisible in
ordinary application logs, but immediately visible once the actual wire traffic was captured and
inspected.

## Syntax

```
nmap [OPTIONS] TARGET
tcpdump [OPTIONS]
tshark [OPTIONS]
```

## Commands

See [`nmap`](../../commands/nmap.md), [`tcpdump`](../../commands/tcpdump.md), and
[`tshark`](../../commands/tshark.md) — all three created as canonical pages on this page.

## Production example

```
$ nmap -sV 10.0.1.15
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9
443/tcp  open  ssl/http nginx 1.24.0
4444/tcp open  unknown

$ sudo tcpdump -i eth0 port 4444 -c 5
14:32:01.221 IP 10.0.1.15.4444 > 198.51.100.77.51201: Flags [P.], length 96
```

Discovering an unexpected open port with `nmap`, then confirming with `tcpdump` that it's actively
sending traffic to an external IP — exactly the kind of finding that escalates from "unusual" to
"actively investigate."

## Do / Don't

| Do | Don't |
|---|---|
| Get authorization before scanning any network you don't own | Run `nmap` against systems without explicit permission |
| Capture only the traffic relevant to an investigation | Capture and store more traffic than necessary (privacy/compliance risk) |
| Use `tshark` filters to narrow a large capture | Manually scroll through an unfiltered multi-gigabyte capture |

## Common mistakes

- Running `nmap` against a network without authorization, which is the same unauthorized-access
  issue covered in [Linux for Penetration Testing](linux-for-penetration-testing-kali-overview.md).
- Capturing far more traffic than an investigation actually needs, creating unnecessary privacy and
  data-retention risk.
- Trying to manually read a large, unfiltered `tcpdump` capture instead of using `tshark`'s display
  filters to narrow it down first.

## Best practices

- Always confirm authorization before scanning or capturing traffic on any network you don't
  personally own or administer.
- Scope captures narrowly (specific host, port, or protocol) rather than capturing everything on an
  interface indiscriminately.
- Use `tshark` display filters to work efficiently with large captures instead of reading them
  linearly.

## Exercises

1. Run `nmap -sV` against a host you own or control, and interpret the service/version detection
   output.
2. Capture a short burst of traffic with `tcpdump -c 10` on a loopback or test interface, and
   describe what each field in the output means.
3. Filter a `.pcap` capture with `tshark -Y` for a specific protocol (e.g. `dns` or `http`).

## Quiz

**Q: What question does `nmap` answer that `tcpdump` doesn't, and vice versa?**
<details><summary>Show answer</summary>
`nmap` discovers what's listening on a host from outside; `tcpdump` captures actual traffic
crossing an interface — discovery versus observation of real traffic.
</details>

**Q: What is `tshark`, in relation to Wireshark?**
<details><summary>Show answer</summary>
Wireshark's command-line component — the same packet analysis engine, used from the terminal
instead of a graphical interface.
</details>

**Q: Why should captures during an investigation be scoped narrowly rather than capturing everything?**
<details><summary>Show answer</summary>
Capturing indiscriminately creates unnecessary privacy and data-retention risk, and produces far
more data than the investigation actually needs to analyze.
</details>

## Interview questions

- When would you reach for `nmap` versus `tcpdump` during a security investigation? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `nmap`, `tcpdump`, and `tshark` address three different layers of network investigation:
  discovery, capture, and deep analysis.
- All three extend Module 12's networking fundamentals rather than introducing new networking
  concepts.
- Authorization is required before scanning or capturing traffic on any network you don't own.
- `tshark` is Wireshark's CLI component — the terminal-appropriate way to teach Wireshark's
  analysis capability in this documentation.

## Further Reading

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Wireshark/tshark Documentation](https://www.wireshark.org/docs/)

## Related topics

- [Linux for Penetration Testing (Kali Overview)](linux-for-penetration-testing-kali-overview.md)
- [Hardening for Compliance: CIS Benchmarks](hardening-for-compliance-cis-benchmarks.md)
