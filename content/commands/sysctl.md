---
title: "sysctl — Read and Change Kernel Runtime Parameters"
description: "Inspect and modify kernel behavior at runtime — networking, virtual memory, filesystem limits — without a reboot, and understand why persisting a change is a separate step."
relatedConcepts: ["21-performance/kernel-tuning-sysctl"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["sysctl command", "sysctl -w", "sysctl.conf", "sysctl -p persist"]
canonicalUrl: "/commands/sysctl"
---

# sysctl

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `sysctl -w PARAMETER=VALUE` changes a kernel parameter immediately, but only at
> runtime — it's lost on reboot unless also written to `/etc/sysctl.d/` and applied with
> `sysctl -p`.

## Purpose

`sysctl` reads and modifies kernel runtime parameters — see
[Kernel Tuning: sysctl](../docs/21-performance/kernel-tuning-sysctl.md) for the full concept,
common performance-relevant parameters, and the runtime-vs-persistent distinction.

## Syntax

```
sysctl [OPTIONS] [PARAMETER[=VALUE]]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PARAMETER` | The dotted kernel parameter name (e.g. `vm.swappiness`) | Yes, for reading/writing a specific value |

## Options

| Flag | Meaning |
|---|---|
| `-w PARAMETER=VALUE` | Write a new value at runtime |
| `-a` | List every current parameter and its value |
| `-p [FILE]` | Load and apply settings from a config file (default `/etc/sysctl.conf`) |
| `-n` | Suppress the parameter name when printing a value (value only) |

## Examples

```
$ sysctl vm.swappiness
```
Read the current value of a specific parameter.

```
$ sudo sysctl -w vm.swappiness=10
```
Change a parameter's value immediately, at runtime only.

```
$ sysctl -a | grep somaxconn
```
List every current parameter, filtered to ones matching a keyword.

```
$ sudo sysctl -p /etc/sysctl.d/99-custom.conf
```
Apply every setting from a specific config file immediately.

## Expected Output

```
$ sysctl vm.swappiness
vm.swappiness = 60

$ sudo sysctl -w vm.swappiness=10
vm.swappiness = 10
```

## Exit Status

`0` on success, non-zero if a parameter doesn't exist or the syntax is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Change disappears after reboot | `sysctl -w` is runtime-only | Persist the value in a file under `/etc/sysctl.d/`, then `sysctl -p` |
| `sysctl: permission denied on key` | Not running as root | Use `sudo` |
| `No such file or directory` reading a parameter | The parameter doesn't exist on this kernel/version | Confirm the exact parameter name with `sysctl -a \| grep KEYWORD` |

## Security Considerations

Some kernel parameters have direct security implications (e.g. network parameters controlling
SYN flood protection or IP forwarding) — review the security consequences of any parameter before
changing it, not just its performance effect.

## Performance Considerations

`sysctl` itself is near-instantaneous; the performance impact comes entirely from the parameter
value being changed, which is why changes should be tested and measured
([Benchmarking Tools](../docs/21-performance/benchmarking-tools.md)) rather than applied blindly.

## Production Usage

Production systems typically manage `sysctl` settings declaratively through configuration
management (a version-controlled file under `/etc/sysctl.d/`, applied consistently across a
fleet) rather than ad hoc `sysctl -w` commands run manually per host.

## Related Commands

None specific to this documentation — `sysctl` is generally used in combination with the
diagnostic tools earlier in [Module 21](../docs/21-performance/index.md) that confirm which
parameter is actually worth tuning.

## Related Concepts

- [Kernel Tuning: sysctl](../docs/21-performance/kernel-tuning-sysctl.md)
