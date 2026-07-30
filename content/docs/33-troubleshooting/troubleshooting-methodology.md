---
title: "Troubleshooting Methodology"
description: "A repeatable framework for diagnosing any Linux problem — define the symptom, gather evidence, form a hypothesis, test narrowly, fix, and verify — instead of guessing and changing things at random."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/reading-logs-during-a-production-incident"]
relatedTopics: ["33-troubleshooting/disk-full-issues", "33-troubleshooting/service-wont-start"]
relatedCommands: []
careerRelevance: ["sre", "devops", "linux-administrator", "platform"]
relatedLabs: ["production/on-call-simulation-service-down-at-2am"]
relatedInterviewQuestions: []
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "33-troubleshooting/disk-full-issues"
prevTopic: "28-linux-for-web-servers/troubleshooting-web-server-errors"
estimatedReadingTime: 8
updatedAt: "2026-07-30"
keywords: ["linux troubleshooting methodology", "systematic debugging framework", "how to troubleshoot linux server", "incident diagnosis framework"]
canonicalUrl: "/docs/troubleshooting/troubleshooting-methodology"
---

# Troubleshooting Methodology

🟢 Must Know · Relevant for: SRE · DevOps · Linux Administrator · Platform

> **TL;DR:** Diagnose before you fix — define the symptom precisely, gather evidence, form one
> testable hypothesis, test it narrowly, then fix and verify. Every specific-symptom page in this
> module (disk full, service won't start, and the rest) is this same framework applied.

## What is it?

A repeatable, six-step framework for approaching any Linux problem: Define the symptom → Gather
evidence → Form a hypothesis → Test narrowly → Fix → Verify. It's the methodology every other
topic in this module applies to a specific symptom.

## Why does it exist?

Under pressure, the natural instinct is to start changing things — restart the service, `chmod
777` the directory, reboot the box — and hope something works. That approach sometimes resolves
the symptom while leaving the actual cause in place, and it produces no reliable account of what
actually happened, which matters the next time the same symptom appears. A methodology forces
evidence before action.

## Where is it used?

Every troubleshooting scenario, from a beginner's first "permission denied" to a senior engineer's
2am production page — the specific commands change per symptom, but the order of operations
(evidence before hypothesis, hypothesis before fix) doesn't.

## How it works

> 📊 Diagram: a linear flowchart — "Symptom reported" → "Define it precisely" → "Gather evidence
> (logs, status, recent changes)" → "Form one testable hypothesis" → "Test narrowly" → branching
> to "Hypothesis confirmed → Fix → Verify" or "Hypothesis wrong → back to Gather evidence" — a loop,
> not a straight line, since most real incidents take more than one pass.

1. **Define the symptom precisely.** "The site is down" is not a symptom you can diagnose;
   "requests to `/api/orders` return 502" is. Vague symptoms lead to vague, scattershot fixes.
2. **Gather evidence before touching anything.** Check logs (`journalctl`, application logs),
   current state (`systemctl status`, `ps`, `ss`), and — critically — what changed recently (a
   deploy, a config edit, a package update). Most production incidents trace back to a recent
   change, not spontaneous failure.
3. **Form one testable hypothesis.** Not "something's wrong with networking" — something specific
   enough that a single command can confirm or rule it out, like "the app isn't listening on the
   expected port."
4. **Test narrowly.** Change or check exactly one thing implied by the hypothesis. Changing
   several things at once means that even when the symptom resolves, you don't know which change
   actually fixed it — and you can't rule out a fix that only appeared to work.
5. **Fix the actual cause**, not the symptom. A broader, looser fix ("just open all firewall
   ports", "just run it as root") often resolves the symptom while leaving the real cause
   unaddressed — see [Permission Denied Issues](permission-denied-issues.md) for the canonical
   example of this trap.
6. **Verify with a real check**, not just the absence of an error. Confirm the fix worked by
   reproducing the original request/action successfully, not by assuming a command that didn't
   error means the underlying problem is resolved.

## Real-world example

An on-call engineer gets paged for a failing health check. Reflexively restarting the service
"fixes" it — the health check passes again five minutes later. Without gathering evidence first
(what did the logs say? what changed before the failure started?), the engineer never learns the
service was silently leaking memory and will fail the same way again in six hours. The restart
bought time; it didn't diagnose anything.

## Commands

No new command example on this page — the value here is the order of operations. See
[reading logs during a production incident](../16-logs/reading-logs-during-a-production-incident.md),
[ps, top, htop](../10-processes/ps-top-htop.md), and [netstat, ss](../12-networking/netstat-ss.md)
for the evidence-gathering commands this framework relies on.

## Production example

```
$ systemctl status checkout-api
● checkout-api.service - Checkout API
   Active: failed (Result: exit-code) since Wed 2026-07-30 02:14:03 UTC; 2min ago

$ journalctl -u checkout-api -n 20 --no-pager
Jul 30 02:14:03 checkout-api[8821]: FATAL: could not connect to database at db-prod-02:5432

$ ss -tan | grep 5432
(no output — nothing listening or reachable on that port from here)
```

The symptom ("checkout-api is down") and the hypothesis it points to ("the database it depends on
is unreachable") are two different things — the evidence (the log line, the empty `ss` output)
is what connects them, rather than guessing that the API itself is broken and restarting it
repeatedly.

## Do / Don't

| Do | Don't |
|---|---|
| Define the symptom in specific, checkable terms | Diagnose against a vague description like "it's broken" |
| Gather evidence (logs, status, recent changes) before acting | Start changing configuration before you know what's wrong |
| Change one thing at a time and verify | Change several things at once and hope one of them helps |
| Fix the actual cause, even if a broader fix is faster | Apply a broad workaround that masks the real cause |

## Common mistakes

- Skipping straight to a fix ("just restart it") without gathering evidence first — sometimes
  works, teaches you nothing, and leaves the real cause free to recur.
- Changing multiple things simultaneously, so that even a successful fix doesn't tell you which
  change actually mattered.
- Treating "the error went away" as verification, instead of confirming the original
  request/action actually succeeds now.
- Not checking what changed recently — most incidents trace back to a deploy, config change, or
  package update, not spontaneous failure.

## Best practices

- Ask "what changed recently?" early — it's the highest-value question in most real incidents.
- Write your hypothesis down (even just in a chat message) before testing it — it forces
  precision and gives you an audit trail.
- Verify a fix by reproducing the original failing action, not by trusting a clean command exit.
- Keep a written record of what you found and did — the next person debugging the same symptom
  (possibly future you) benefits from it.

## Exercises

1. Take a vague problem statement ("the app is slow") and rewrite it as a specific, checkable
   symptom.
2. Given a hypothetical symptom, write one testable hypothesis and the single command that would
   confirm or rule it out.
3. Explain in one sentence why changing multiple things at once undermines a fix, even when the
   symptom resolves.

## Quiz

**Q: Why gather evidence before forming a hypothesis, rather than fixing a suspected cause immediately?**
<details><summary>Show answer</summary>
Because a fix applied on a guess sometimes resolves the symptom by coincidence while leaving the
actual cause in place — evidence-first diagnosis is what actually connects the symptom to its
real cause.
</details>

**Q: Why change only one thing at a time when testing a hypothesis?**
<details><summary>Show answer</summary>
Changing several things at once means that even a successful fix doesn't tell you which change
was responsible — you lose the ability to confirm your hypothesis and to avoid the same problem
next time.
</details>

## Interview questions

No interview question bank entries reference this framework page directly yet — its ideas are
applied throughout the [Scenario-Based Interview Questions](../../interview-questions/scenario-based.md),
each of which is this same methodology worked through for one specific symptom.

## Key Takeaways

- Troubleshooting is a repeatable framework, not an ad hoc process: define, gather evidence,
  hypothesize, test narrowly, fix, verify.
- Evidence comes before hypothesis; hypothesis comes before any fix.
- "What changed recently?" is often the single highest-value diagnostic question.
- Every other topic in this module is this same framework applied to one specific symptom.

## Related topics

- [Disk Full Issues](disk-full-issues.md)
- [Service Won't Start](service-wont-start.md)
- [Reading Logs During a Production Incident](../16-logs/reading-logs-during-a-production-incident.md)
