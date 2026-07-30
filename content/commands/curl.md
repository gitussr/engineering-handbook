---
title: "curl — Transfer Data To or From a URL"
description: "Inspect and interact with HTTP(S) requests and responses from the command line — headers, status codes, methods, bodies."
relatedConcepts: ["12-networking/curl-wget"]
relatedCommands: ["wget"]
careerRelevance: ["devops", "backend", "sre", "cloud"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["curl command examples", "curl -I", "curl post request", "curl status code"]
canonicalUrl: "/commands/curl"
---

# curl

🟢 Must Know · Relevant for: DevOps · Backend · SRE · Cloud

> **TL;DR:** `curl -I url` shows response headers and status without a full body. `curl -X POST
> -d 'data' url` sends a POST request. `curl -s -o /dev/null -w "%{http_code}"` is the standard
> scripted health-check pattern.

## Purpose

`curl` sends and inspects HTTP(S) (and other protocol) requests from the command line — see
[curl, wget](../docs/12-networking/curl-wget.md) for when to reach for it over `wget`.

## Syntax

```
curl [OPTIONS] URL
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `URL` | The address to request | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-I` | HEAD request — headers and status only, no body |
| `-v` | Verbose — show the full request and response, including headers |
| `-X METHOD` | Set the HTTP method (`POST`, `PUT`, `DELETE`, etc.) |
| `-d DATA` | Send data as the request body (implies POST unless `-X` overrides it) |
| `-H "Header: value"` | Add a custom request header |
| `-L` | Follow redirects automatically |
| `-o FILE` | Write output to FILE instead of stdout |
| `-s` | Silent — suppress progress meter |
| `-w FORMAT` | Print custom output after the transfer (e.g. `%{http_code}`) |

## Examples

```
$ curl -I https://example.com
```
Show response headers and status only.

```
$ curl -X POST -d '{"key":"value"}' -H "Content-Type: application/json" https://api.example.com/resource
```
Send a JSON POST request.

```
$ curl -L https://example.com
```
Follow redirects automatically instead of stopping at the redirect response.

```
$ curl -s -o /dev/null -w "%{http_code}\n" https://example.com/health
```
Print only the numeric HTTP status code — the standard scripted health-check pattern.

## Expected Output

```
$ curl -I https://example.com
HTTP/2 200
content-type: text/html; charset=UTF-8
content-length: 1256
```

## Exit Status

`0` on a successful transfer, non-zero on a connection failure, timeout, or invalid URL. Note:
`curl`'s own exit status reflects the *transfer*, not the HTTP status code — a `404` response is
still a successful transfer from `curl`'s point of view unless `-f` (`--fail`) is used.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Could not resolve host` | DNS resolution failed for the hostname | Check DNS — see [DNS Troubleshooting](../docs/12-networking/dns-troubleshooting-dig-nslookup.md) |
| `Connection refused` | Nothing listening on the target port | Check with [`ss`](ss.md) on the target, or that the port/protocol (http vs. https) is correct |
| Redirect response shown instead of final content | `-L` not used | Add `-L` to follow redirects |
| Script treats a 404/500 as success | `curl`'s exit code doesn't reflect HTTP status by default | Add `-f` to make `curl` fail on HTTP error responses, or check `-w "%{http_code}"` explicitly |

## Security Considerations

Avoid passing secrets (API tokens, passwords) as part of a URL or `-d` value visible in shell
history or process listings (`ps`); prefer environment variables, `--netrc`, or a header read from
a file with restricted permissions.

## Performance Considerations

`curl -I` (HEAD) avoids transferring a full response body when only status/headers are needed —
meaningfully faster for repeated health checks against large responses.

## Production Usage

`curl -s -o /dev/null -w "%{http_code}"` is the standard building block for scripted health
checks and deployment verification — clean, parseable, single-line output.

## Related Commands

- [`wget`](wget.md) — simpler, download-focused alternative

## Related Concepts

- [curl, wget](../docs/12-networking/curl-wget.md)
