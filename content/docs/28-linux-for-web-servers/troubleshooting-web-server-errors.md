---
title: "Troubleshooting Web Server Errors"
description: "The standard HTTP 4xx/5xx error codes, what each one actually indicates about which layer failed, and where to look first — Nginx/Apache's own error logs, before assuming the application is at fault."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["28-linux-for-web-servers/deploying-php-python-node-apps-on-linux"]
relatedTopics: ["deploying-php-python-node-apps-on-linux", "hosting-a-website-end-to-end"]
relatedCommands: ["journalctl"]
careerRelevance: ["devops", "sre", "backend", "wordpress-web-hosting", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#502-vs-504-vs-500-difference"]
relatedCheatsheet: ""
furtherReading: [{"label": "Nginx Error Log Documentation", "url": "https://nginx.org/en/docs/ngx_core_module.html#error_log"}]
nextTopic: "33-troubleshooting/troubleshooting-methodology"
prevTopic: "28-linux-for-web-servers/deploying-php-python-node-apps-on-linux"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["502 bad gateway meaning", "504 gateway timeout meaning", "nginx error log location", "web server troubleshooting checklist", "500 vs 502 vs 503 vs 504"]
canonicalUrl: "/docs/linux-for-web-servers/troubleshooting-web-server-errors"
---

# Troubleshooting Web Server Errors

🟢 Must Know · Relevant for: DevOps · SRE · Backend · WordPress/Web Hosting · Platform

> **TL;DR:** A 502 means the proxy couldn't get a response at all (the backend is down or
> unreachable); a 504 means it got no response *in time* (the backend is slow or hung); a 500 means
> the backend responded, but with an error — three different failures, and the error log at
> `/var/log/nginx/error.log` almost always says which one immediately.

## What is it?

A systematic way to interpret the standard HTTP error codes a web server returns, and know
immediately which layer — the proxy, the backend application, or the client's request itself — is
actually responsible, closing this module by tying every piece (Nginx, the reverse proxy, the
backend application) back together under real failure conditions.

## Why does it exist?

The HTTP status code returned during a failure is a direct, specific diagnostic signal, not a
generic "something broke" indicator — treating every error the same wastes time investigating the
wrong layer. Knowing what each code specifically means turns "the site is down" into "the backend
process has crashed," a concrete, actionable starting point.

## Where is it used?

Every production incident involving a web application — routine, expected during deployments and
outages, and one of the most common categories of on-call page for any team running a web-facing
service.

## How it works

> 📊 Diagram: a request flowing through Nginx to a backend, with each layer's specific failure
> mode labeled with its resulting status code — backend process down or unreachable → 502; backend
> too slow to respond within the proxy's timeout → 504; backend responded but threw an internal
> error → 500; client sent something the backend explicitly rejected → 400/404 — each pointing to
> a different specific investigation.

| Code | Meaning | Where the problem actually is |
|---|---|---|
| `400 Bad Request` | The request itself is malformed | Client or a misbehaving upstream request |
| `404 Not Found` | The requested resource doesn't exist | Application routing, or a genuinely missing resource |
| `500 Internal Server Error` | The backend received the request and threw an unhandled error | The application itself |
| `502 Bad Gateway` | The proxy couldn't get *any* response from the backend | Backend is down, crashed, or unreachable |
| `503 Service Unavailable` | The server is deliberately not accepting requests (overloaded, maintenance) | Capacity, or an explicit maintenance mode |
| `504 Gateway Timeout` | The proxy got no response *within its timeout window* | Backend is running but too slow |

Nginx's own error log (`/var/log/nginx/error.log`) is the first place to check for any 5xx — it
directly states what Nginx itself observed, independent of whatever the backend's own logs (see
[Module 16](../16-logs/index.md)) might separately show:

```bash
$ sudo tail -f /var/log/nginx/error.log
```

## Real-world example

A team gets paged for a spike in 502 errors and initially checks their application's own logs,
finding nothing unusual there — because the application had actually crashed and wasn't running at
all, so it had no logs to produce for that window. Checking Nginx's error log directly shows
`connect() failed (111: Connection refused)`, immediately confirming the backend process itself was
down rather than merely slow or erroring — the exact distinction that separates a 502 from a 504,
and the reason checking the proxy's own log first, not just the application's, matters.

## Syntax

No new syntax — this page interprets standard HTTP status codes and existing log locations rather
than introducing new commands.

## Commands

See [`journalctl`](../../commands/journalctl.md) (Module 11/16) for reading the backend
application's own systemd-managed logs, alongside Nginx's file-based error log shown above.

## Production example

```
$ sudo tail -20 /var/log/nginx/error.log
2026/07/29 14:02:11 [error] 8821#8821: *42 connect() failed (111: Connection refused) while
connecting to upstream, client: 203.0.113.5, server: example.com,
upstream: "http://127.0.0.1:3000/", host: "example.com"
```

`Connection refused` confirms nothing is listening on port 3000 at all — the backend process is
down, not merely slow — immediately narrowing the investigation to why the application isn't
running (check `systemctl status myapp.service` next).

## Do / Don't

| Do | Don't |
|---|---|
| Check the specific status code before investigating | Treat every error as a generic "site is down" |
| Check Nginx's error log first for any 5xx | Assume the application's own logs will show everything |
| Distinguish "backend unreachable" (502) from "backend slow" (504) | Treat 502 and 504 as interchangeable |

## Common mistakes

- Treating every error alike instead of using the specific status code to narrow down which layer
  actually failed.
- Checking only the application's own logs for a 502, missing that a fully-down application
  produces no logs of its own for that window — Nginx's error log is the one that actually
  captured the failure.
- Confusing a 502 (backend completely unreachable) with a 504 (backend reachable but too slow),
  which point to very different root causes and fixes.

## Best practices

- Always check the specific HTTP status code first — it's a direct, specific diagnostic signal,
  not a generic failure indicator.
- Check the reverse proxy's own error log before assuming the application's logs will show
  everything, especially for 502/504 errors where the application may not be running at all.
- Build monitoring around status code categories (Module 20's alerting concepts), not just overall
  uptime, so a spike specifically in 502s versus 504s is immediately distinguishable.

## Exercises

1. Deliberately stop a backend application fronted by Nginx, request its URL, and confirm Nginx
   returns a 502 with a corresponding error log entry.
2. Explain the difference between a 500, 502, 503, and 504 in your own words.
3. Given only "the site returned a 504," describe your first three troubleshooting steps.

## Quiz

**Q: What's the key difference between a 502 and a 504 error?**
<details><summary>Show answer</summary>
A 502 means the proxy got no response at all (backend down/unreachable); a 504 means the proxy got
no response within its timeout window (backend reachable but too slow).
</details>

**Q: Why might an application's own logs show nothing useful during a 502 error?**
<details><summary>Show answer</summary>
A 502 often means the backend process isn't running at all, so it never received the request and
has no log entry for it — the reverse proxy's own error log is what actually captured the failure.
</details>

**Q: What does a 500 status code specifically indicate that a 502 doesn't?**
<details><summary>Show answer</summary>
A 500 means the backend received the request and ran, but threw an unhandled error while
processing it — distinct from a 502, where the backend never responded at all.
</details>

## Interview questions

- Explain the difference between a 500, 502, 503, and 504 HTTP error, and how you'd troubleshoot
  each. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Each HTTP status code is a specific diagnostic signal pointing at a specific layer's failure —
  not a generic "something's wrong."
- 502 (unreachable) and 504 (too slow) are commonly confused but point to different root causes.
- The reverse proxy's own error log is often the first and most informative place to check for any
  5xx error, especially when the backend itself produced no logs.
- This closes Module 28 and the final career-branch module — Module 33's Troubleshooting module
  continues with a general, repeatable troubleshooting methodology applicable beyond web servers
  specifically.

## Further Reading

- [Nginx Error Log Documentation](https://nginx.org/en/docs/ngx_core_module.html#error_log)

## Related topics

- [Deploying PHP/Python/Node Apps on Linux](deploying-php-python-node-apps-on-linux.md)
- [Hosting a Website End-to-End](hosting-a-website-end-to-end.md)
