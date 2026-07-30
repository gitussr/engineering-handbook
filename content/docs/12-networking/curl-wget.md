---
title: "curl, wget"
description: "Testing and downloading over HTTP(S) from the command line — curl for inspecting requests and responses, wget for straightforward downloads."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/netstat-ss"]
relatedTopics: []
relatedCommands: ["curl", "wget"]
careerRelevance: ["devops", "backend", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#curl-vs-wget"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/etc-hosts-resolv-conf"
prevTopic: "12-networking/netstat-ss"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["curl command examples", "wget command examples", "curl vs wget", "test http endpoint linux"]
canonicalUrl: "/docs/networking/curl-wget"
---

# curl, wget

🟢 Must Know · Relevant for: DevOps · Backend · SRE · Cloud

> **TL;DR:** `curl` inspects and interacts with HTTP(S) (and other protocol) requests and
> responses — headers, status codes, methods, bodies. `wget` is a simpler tool built specifically
> for downloading files, including recursively. Reach for `curl` to test/debug an endpoint,
> `wget` to just fetch a file.

## What is it?

Two command-line tools for interacting with network resources over HTTP(S): `curl`, built for
flexible request/response inspection and scripting, and `wget`, built specifically for downloading
files reliably.

## Why does it exist?

Testing whether an API endpoint is actually responding correctly, checking response headers and
status codes, and downloading files as part of automation or provisioning are constant needs on
any Linux system — long before reaching for a browser or a dedicated HTTP client library. `curl`
and `wget` cover this without leaving the terminal.

## Where is it used?

Health-checking a web service or API endpoint during deployment, debugging why an HTTP request is
failing (wrong status code, missing header, redirect loop), downloading installer scripts or
release artifacts as part of provisioning, and scripted health checks in automation and CI/CD
pipelines.

## How it works

> 📊 Diagram: a terminal on the left running `curl -I` against a server, with the raw HTTP
> response headers shown annotated (status line, `Content-Type`, `Content-Length`); a terminal on
> the right running `wget` against the same server, showing a progress bar and the file saved to
> disk — same server, two different tools answering two different questions.

| Task | Tool |
|---|---|
| Inspect status code, headers, redirects | `curl -I` / `curl -v` |
| Send a POST request with a JSON body | `curl -X POST -d '...'` |
| Download a single file to disk | `wget URL` or `curl -O URL` |
| Download a whole directory tree recursively | `wget -r` |
| Script an API call and parse the response | `curl` (output is easy to pipe into `jq` etc.) |

`curl` defaults to printing the response body to standard output — useful for inspection and
piping into other tools. `wget` defaults to saving the response to a file — useful for downloads.
Both can technically do either task; the defaults reflect what each tool was actually built for.

## Real-world example

A deployment script needs to confirm a newly deployed API is actually healthy before proceeding.
`curl -s -o /dev/null -w "%{http_code}" https://api.example.com/health` returns just the numeric
HTTP status code with no other output — exactly the single piece of information a script needs to
branch on, without parsing a full response body.

## Commands

- [`curl`](../../commands/curl.md) — full syntax and examples
- [`wget`](../../commands/wget.md) — full syntax and examples

## Production example

```
$ curl -I https://example.com
HTTP/2 200
content-type: text/html; charset=UTF-8

$ curl -s -o /dev/null -w "%{http_code}\n" https://example.com/health
200

$ wget https://example.com/release-1.2.0.tar.gz
```

The second command's `-w "%{http_code}"` pattern is the standard way to check just a status code
in a script, without downloading or printing the full response body.

## Do / Don't

| Do | Don't |
|---|---|
| Use `curl -I` to check headers/status without downloading a body | Download a full response just to check its status code |
| Use `wget` for straightforward file downloads, including recursive ones | Reach for `curl` and hand-write download-and-save logic `wget` already does |
| Use `curl -s -o /dev/null -w "%{http_code}"` in health-check scripts | Parse full HTML/JSON output just to extract a status code |

## Common mistakes

- Downloading and parsing a full response body in a script just to check a status code, instead
  of using `curl`'s `-o /dev/null -w "%{http_code}"` pattern.
- Not using `-I` (HEAD request) when only headers are needed, wasting bandwidth and time fetching
  a full body.
- Forgetting `-L` with `curl` when a URL redirects — without it, `curl` shows the redirect
  response itself instead of following it.

## Best practices

- Use `curl -I` or `curl -v` for debugging — headers and status codes reveal most HTTP-level
  problems immediately.
- Build health checks around `curl -s -o /dev/null -w "%{http_code}"` for a clean, script-friendly
  status code check.
- Default to `wget` for simple or recursive downloads; default to `curl` when you need to inspect,
  script, or send anything beyond a plain GET request.

## Exercises

1. Run `curl -I` against any HTTPS site and read the status line and headers.
2. Write a one-line `curl` command that prints only the HTTP status code of a request.
3. Explain in one sentence when you'd reach for `wget` instead of `curl`.

## Quiz

**Q: What does `curl -I` do?**
<details><summary>Show answer</summary>
Sends a HEAD request and shows just the response headers and status line, without downloading the
response body.
</details>

**Q: Why is `curl -s -o /dev/null -w "%{http_code}"` a common pattern in health-check scripts?**
<details><summary>Show answer</summary>
It returns only the numeric HTTP status code, discarding the response body — exactly the single
piece of information a script needs to branch on.
</details>

## Interview questions

- When would you choose `wget` over `curl`, or vice versa? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `curl` is built for flexible request/response inspection and scripting; `wget` is built for
  straightforward (including recursive) downloads.
- `curl -I` checks headers/status without fetching a full body.
- `curl -s -o /dev/null -w "%{http_code}"` is the standard scripted health-check pattern.
- `curl -L` is needed to follow redirects automatically.

## Related topics

- [/etc/hosts, /etc/resolv.conf](etc-hosts-resolv-conf.md)
- [netstat, ss](netstat-ss.md)
