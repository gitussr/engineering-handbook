---
title: "What is Linux?"
description: "What Linux actually is, why it exists, and why it runs most of the servers, cloud, and infrastructure you'll work on professionally."
module: "01-linux-basics"
moduleTitle: "Linux Basics"
stage: "beginner"
difficulty: "must-know"
foundational: true
type: "concept"
prerequisites: []
relatedTopics: ["kernel-vs-os-vs-distro", "linux-distributions", "linux-in-industry"]
relatedCommands: ["uname"]
careerRelevance: []
relatedLabs: []
relatedInterviewQuestions: ["beginner#what-is-linux"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "man uname(1)", "url": "https://man7.org/linux/man-pages/man1/uname.1.html"}]
nextTopic: "01-linux-basics/linux-distributions"
prevTopic: ""
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["what is linux", "linux operating system", "linux kernel", "open source os"]
canonicalUrl: "/docs/linux-basics/what-is-linux"
---

# What is Linux?

🟢 Must Know · Relevant for: All career paths

> **TL;DR:** Linux is a free, open-source operating system kernel — the program that manages a
> machine's hardware — and, by extension, the family of operating systems built around it. It
> runs most of the world's servers, cloud infrastructure, and Android devices.

## What is it?

Linux is a free, open-source operating system kernel, and by extension the family of operating
systems built around that kernel. It manages hardware (CPU, memory, disks, network) and lets
every other program on the machine run on top of it.

## Why does it exist?

In 1991, commercial Unix systems were expensive and closed. Linus Torvalds released a kernel
that anyone could use, modify, and redistribute for free. Combined with GNU tools, it became a
complete, freely available Unix-like operating system. That "free to run anywhere, on anything"
property is why it won the server and cloud market.

## Where is it used?

- Public cloud infrastructure (AWS, GCP, Azure — the overwhelming majority of VMs)
- Web servers (Nginx, Apache almost always run on Linux in production)
- Container platforms (Docker and Kubernetes are built on Linux kernel features)
- Mobile (Android's kernel is Linux)
- Embedded systems, routers, IoT devices
- Supercomputers (nearly all of the top 500 run Linux)
- Developer workstations, especially in DevOps/SRE/backend roles

Full breakdown of where each of these fits in the job market:
[Linux in Industry](linux-in-industry.md).

## How it works

The kernel handles the low-level work — scheduling processes, managing memory, talking to
hardware, enforcing permissions — and every application, including the shell, runs on top of it.
That's a one-line summary; the full kernel/OS/distro breakdown, including the diagram, lives on
[Kernel vs OS vs Distro](kernel-vs-os-vs-distro.md) — this page doesn't repeat it.

## Real-world example

A production incident: an API server stops responding. The on-call engineer's first instinct is
to check the application logs — but the actual cause is the kernel's OOM (out-of-memory) killer
terminating the process because the box ran out of RAM. Nothing about this is visible without
understanding that the kernel, not the application, made that call. This is the practical reason
"what is Linux" isn't trivia — it shapes where you look when something breaks.

## Commands

- [`uname`](../../commands/uname.md) — print kernel name, version, and architecture

## Production example

```
$ uname -a
Linux web-prod-03 6.8.0-31-generic #31-Ubuntu SMP x86_64 GNU/Linux
```

Reading this line is a basic production skill: it tells you the kernel name (`Linux`), hostname
(`web-prod-03`), kernel version, and architecture (`x86_64`) — useful the moment you SSH into an
unfamiliar server and need to know what you're working with.

## Do / Don't

| Do | Don't |
|---|---|
| Say "Linux" for the kernel, name the distro when precision matters | Use "Linux" and a distro name interchangeably in technical writing |
| Check `uname -a` on an unfamiliar server before assuming anything | Assume every server behaves like your dev laptop |

## Common mistakes

- Saying "Linux" when you mean a specific distribution ("Ubuntu" or "CentOS") — precision matters
  once you're troubleshooting version-specific behavior.
- Assuming Linux is a GUI-first system because desktop Linux exists — production Linux is
  overwhelmingly CLI-driven (see [GUI vs CLI](gui-vs-cli.md)).
- Treating "open source" as "unsupported" — major distros have paid enterprise support (Red Hat,
  Canonical) used at large companies.

## Best practices

- Learn to identify what's kernel-level vs. distro-level vs. application-level when debugging —
  it tells you where to look for the fix.
- Get comfortable with `uname -a`, `/etc/os-release`, and `hostnamectl` as the first commands you
  run on any unfamiliar server (covered in later modules).

## Exercises

1. Run `uname -a` on any Linux system (or a free cloud shell) and identify the kernel version and
   architecture in the output.
2. Look up which Linux kernel version your target cloud provider's default VM image uses.
3. Name three products or platforms you use daily that run on Linux somewhere in their stack.

## Quiz

**Q: Is "Linux" the whole operating system or just the kernel?**
<details><summary>Show answer</summary>
Strictly, "Linux" refers to the kernel. What most people call "a Linux system" is the kernel plus
a large set of GNU tools, libraries, and (in a distro) a package manager and additional software.
</details>

**Q: Why does Linux dominate cloud and server infrastructure?**
<details><summary>Show answer</summary>
It's free, open-source, highly stable, runs on nearly any hardware architecture, and has decades
of production hardening plus strong vendor and community support.
</details>

## Interview questions

- What is the difference between Linux and an operating system built on Linux? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)
- Why do most servers run Linux instead of Windows? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Linux is a kernel; a distro is that kernel plus a full set of software around it.
- It exists because it's free, open, and modifiable — which is exactly why it won infrastructure.
- It runs the majority of servers, cloud VMs, containers, and mobile devices worldwide.
- Production Linux work is CLI-first, not GUI-first.
- Every career path in this documentation assumes daily working knowledge of Linux.

## Further Reading

- [`man uname(1)`](https://man7.org/linux/man-pages/man1/uname.1.html)

## Related topics

- [Kernel vs OS vs Distro](kernel-vs-os-vs-distro.md)
- [Linux Distributions](linux-distributions.md)
- [Linux in Industry](linux-in-industry.md)
- Cheat sheet: [Linux Commands](../../cheatsheets/linux-commands.md)
