---
title: "uname — Print System Information"
description: "Print the kernel name, version, and machine architecture — the fastest way to confirm exactly what kernel and hardware platform you're actually running on."
relatedConcepts: ["01-linux-basics/what-is-linux", "01-linux-basics/kernel-vs-os-vs-distro"]
relatedCommands: []
careerRelevance: ["linux-administrator", "devops", "sre"]
difficulty: "must-know"
compatibility: [{"context": "GNU coreutils (Linux)", "note": "supports the full flag set below, including -o for the operating system name"}, {"context": "BSD/macOS", "note": "supports the same core flags (-a, -s, -r, -m) but has no -o flag"}]
updatedAt: "2026-07-30"
keywords: ["uname command linux", "uname -a", "check kernel version linux", "uname -r"]
canonicalUrl: "/commands/uname"
---

# uname

🟢 Must Know · Relevant for: Linux Administrator · DevOps · SRE

> **TL;DR:** `uname -a` prints everything — kernel name, version, and architecture in one line.
> `uname -r` prints just the kernel version, the single most commonly needed piece.

## Purpose

`uname` prints system identification information — the kernel name, version, and machine hardware
architecture — directly from the running kernel itself, rather than from a config file that could
be stale. See [What is Linux](../docs/01-linux-basics/what-is-linux.md) and
[Kernel vs OS vs Distro](../docs/01-linux-basics/kernel-vs-os-vs-distro.md) for why this
distinction (kernel vs. the rest of the system) matters.

## Syntax

```
uname [OPTIONS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| *(none)* | `uname` takes no positional arguments — behavior is controlled entirely by flags | — |

## Options

| Flag | Meaning |
|---|---|
| `-a` | All information — kernel name, hostname, kernel release, version, machine, and OS |
| `-s` | Kernel name only (e.g. `Linux`) — the default with no flags |
| `-r` | Kernel release/version only (e.g. `6.8.0-45-generic`) |
| `-m` | Machine hardware architecture (e.g. `x86_64`, `aarch64`) |
| `-o` | Operating system name (GNU/Linux-specific, not on BSD/macOS) |

## Examples

```
$ uname -r
```
Print just the kernel version — the most common single use of this command.

```
$ uname -m
```
Print the machine architecture, useful before downloading an architecture-specific binary.

```
$ uname -a
```
Print everything at once — the standard first command when reporting a bug or confirming what
you're actually running on.

## Expected Output

```
$ uname -a
Linux web-prod-07 6.8.0-45-generic #45-Ubuntu SMP x86_64 x86_64 x86_64 GNU/Linux
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Success |
| Non-zero | An invalid option was given — `uname` itself essentially cannot fail otherwise, since it reads information the running kernel always has |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `-o` flag not recognized | Running on a BSD/macOS system, not GNU/Linux | Use `-s` instead, or check `uname` without `-o` |
| Architecture looks unexpected (`i686` instead of `x86_64`) | A 32-bit userland running on 64-bit hardware, or a container/VM with different arch emulation | Confirm with `uname -m` and cross-check the actual hardware/hypervisor configuration |

## Security Considerations

Read-only and safe to run as any user — `uname` makes no changes and requires no privileges. Kernel
version disclosure itself is a minor information-exposure consideration on systems where hiding
exact patch levels from untrusted users matters (e.g. shared hosting), but this is a minor,
situational concern, not a general risk.

## Performance Considerations

Effectively instantaneous — `uname` reads data the kernel already holds in memory, with no disk or
network access involved.

## Compatibility Notes

GNU `uname` (standard on Linux) supports `-o` for the OS name; BSD-derived `uname` (macOS, BSD)
does not have this flag but supports the same core `-a`/`-s`/-r`/`-m` set — scripts targeting both
should avoid `-o` and use `-s` instead.

## Production Usage

`uname -r` is the fastest way to confirm exactly which kernel a server is running, useful when
diagnosing whether a bug or feature is kernel-version-dependent, or confirming a kernel update
actually took effect after a reboot. `uname -m` matters before downloading any architecture-specific
binary or container image, since running an `x86_64` binary on an `aarch64` host (or vice versa)
fails immediately.

## Related Commands

- [`hostnamectl`](hostnamectl.md) — shows the same kernel information alongside the hostname and OS
  pretty name, on systemd-based systems

## Related Concepts

- [What is Linux](../docs/01-linux-basics/what-is-linux.md)
- [Kernel vs OS vs Distro](../docs/01-linux-basics/kernel-vs-os-vs-distro.md)
