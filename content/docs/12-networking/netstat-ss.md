---
title: "netstat, ss"
description: "Inspecting active connections and listening ports — ss is the modern, faster replacement for the deprecated netstat."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/ping-traceroute-mtr"]
relatedTopics: ["ports-and-sockets-explained"]
relatedCommands: ["ss"]
careerRelevance: ["devops", "sre", "linux-administrator", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ss-vs-netstat"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/curl-wget"
prevTopic: "12-networking/ping-traceroute-mtr"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["ss command linux", "netstat deprecated", "ss -tuln", "check listening ports linux"]
canonicalUrl: "/docs/networking/netstat-ss"
---

# netstat, ss

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator · Cybersecurity

> **TL;DR:** `ss -tuln` lists listening TCP/UDP ports and is the modern, faster replacement for
> the deprecated `netstat -tuln` — same question ("what's listening, and on which port?"), same
> flag pattern, different underlying tool.

## What is it?

Tools for inspecting active network connections and listening ports on the local machine. `ss`
("socket statistics") is the current standard; `netstat` is the older tool it replaced, still
widely referenced in documentation and muscle memory.

## Why does it exist?

Knowing what a machine is actually listening on — and what's currently connected to what — is a
constant need: confirming a service actually bound to the port it's supposed to, checking for
unexpected listeners during a security review, or diagnosing a "connection refused" error.
`netstat` read this information in a way that scaled poorly on systems with very large numbers of
connections; `ss` reads the same kernel data more directly and is significantly faster.

## Where is it used?

Confirming a service is actually listening after starting it (a direct companion check to
[Module 11's `systemctl status`](../11-services/systemctl-start-stop-enable-disable-status.md)),
diagnosing "connection refused" errors, and reviewing what's listening on a server as part of a
security audit.

## How it works

> 📊 Diagram: a server process shown bound to a port, with `ss -tuln` output annotated column by
> column (protocol, state, local address:port, peer address:port) mapped back to that same
> process.

| Flag | Meaning |
|---|---|
| `-t` | TCP sockets |
| `-u` | UDP sockets |
| `-l` | Listening sockets only |
| `-n` | Numeric output (don't resolve port names/hostnames) |
| `-p` | Show the owning process (requires root for other users' processes) |

`ss -tuln` is the standard "what's listening" combination, direct equivalent of the older
`netstat -tuln`. Port numbers themselves and what they mean at a conceptual level are covered in
[Ports and Sockets Explained](ports-and-sockets-explained.md), this module's later topic — this
page focuses on reading the command output, not the concept underneath it.

## Real-world example

A newly deployed web application returns "connection refused" on port 8080. `ss -tuln | grep 8080`
shows nothing listening on that port at all — the application isn't bound where it's expected to
be, immediately ruling out a firewall issue (covered later in this module) and pointing back at
the application's own configuration or startup failure instead.

## Commands

- [`ss`](../../commands/ss.md) — full syntax and examples

`netstat` is deprecated in favor of `ss` on current distros (sometimes not installed by default at
all) but still appears constantly in older documentation and scripts — the flag combinations
translate almost directly (`netstat -tuln` → `ss -tuln`), so recognizing it is enough; it doesn't
get a separate canonical page here.

## Production example

```
$ ss -tuln | grep 8080
$ echo $?
1
```

An empty result and a non-zero exit status from `grep` confirm nothing is listening on port 8080
— the immediate next step is checking the application's own startup logs
([`journalctl`](../../commands/journalctl.md)), not the firewall.

## Do / Don't

| Do | Don't |
|---|---|
| Use `ss -tuln` as the default check for "what's listening" | Reach for `netstat` on a current system just out of habit |
| Confirm a service is actually listening before investigating the firewall | Assume "connection refused" always means a firewall problem |
| Add `-p` (with sudo) to identify which process owns a listening port | Guess which process owns a port from the port number alone |

## Common mistakes

- Assuming a "connection refused" error is always a firewall problem — if nothing is listening on
  the port at all, the firewall was never in the picture (see the real-world example above).
- Reaching for `netstat` on a modern system where it may not even be installed, instead of `ss`.
- Forgetting `-n` and waiting on slow reverse-DNS/service-name lookups for every row of output.

## Best practices

- Default to `ss -tuln` (add `-p` with sudo to see the owning process) as the first check when a
  service seems unreachable.
- Confirm a service is listening before troubleshooting anything further downstream — firewall
  rules, DNS, or client-side configuration.
- Recognize `netstat` syntax in older docs/scripts and translate it directly to `ss` rather than
  installing `net-tools` just to keep old commands working.

## Exercises

1. Run `ss -tuln` on a system you have access to and identify at least one listening service.
2. Explain in one sentence why `ss` replaced `netstat` on modern distros.
3. Describe what an empty `ss -tuln | grep PORT` result tells you about a "connection refused"
   error.

## Quiz

**Q: What does `ss -tuln` show?**
<details><summary>Show answer</summary>
All listening (`-l`) TCP (`-t`) and UDP (`-u`) sockets, with numeric (`-n`) addresses and ports
instead of resolved names.
</details>

**Q: If `ss -tuln | grep 8080` returns nothing, what does that tell you about a "connection
refused" error on port 8080?**
<details><summary>Show answer</summary>
Nothing is listening on that port at all — the problem is the application not binding to the
expected port, not a firewall rule blocking access to it.
</details>

## Interview questions

- Why did `ss` replace `netstat` as the standard tool? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `ss -tuln` lists listening TCP/UDP sockets and is the modern replacement for `netstat -tuln`.
- Confirming a service is actually listening is the first step before blaming the firewall.
- `netstat`'s flag syntax translates almost directly to `ss`.
- `-p` (with sudo) identifies which process owns a listening port.

## Related topics

- [Ports and Sockets Explained](ports-and-sockets-explained.md)
- [curl, wget](curl-wget.md)
