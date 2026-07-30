---
title: "SSL/TLS with Let's Encrypt / certbot"
description: "Free, automatically-renewing TLS certificates from Let's Encrypt via certbot — how domain validation works, why certificates expire quickly on purpose, and how auto-renewal is wired into the system."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["28-linux-for-web-servers/reverse-proxy-basics"]
relatedTopics: ["reverse-proxy-basics", "hosting-a-website-end-to-end"]
relatedCommands: []
careerRelevance: ["cybersecurity", "devops", "wordpress-web-hosting", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#why-do-tls-certs-expire-so-fast-now"]
relatedCheatsheet: ""
furtherReading: [{"label": "Let's Encrypt Documentation", "url": "https://letsencrypt.org/docs/"}, {"label": "Certbot Instructions", "url": "https://certbot.eff.org/"}]
nextTopic: "28-linux-for-web-servers/hosting-a-website-end-to-end"
prevTopic: "28-linux-for-web-servers/reverse-proxy-basics"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["lets encrypt certbot explained", "free ssl certificate linux", "certbot auto renew", "domain validation acme", "certbot nginx setup"]
canonicalUrl: "/docs/linux-for-web-servers/ssl-tls-with-lets-encrypt-certbot"
---

# SSL/TLS with Let's Encrypt / certbot

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · WordPress/Web Hosting · Backend

> **TL;DR:** Let's Encrypt issues free, automated TLS certificates via the ACME protocol; `certbot`
> is the standard client that proves domain ownership, obtains the certificate, configures the web
> server, and — critically — sets up automatic renewal, since Let's Encrypt certificates
> deliberately expire every 90 days.

## What is it?

Let's Encrypt is a free, automated Certificate Authority; `certbot` is the standard client tool
that requests, installs, and renews certificates from it, integrating directly with
[Nginx](nginx-install-and-config.md) or [Apache](apache-install-and-config.md) configuration.

## Why does it exist?

TLS certificates historically required a manual, often paid process — generating a key, submitting
a request, waiting for a certificate authority, then manually installing and remembering to renew
it, often once a year. Let's Encrypt automated the entire process end to end and made it free,
directly enabling HTTPS to become the near-universal default it is today rather than a costly
add-on.

## Where is it used?

Any website needing TLS (which is to say, nearly every production website) that doesn't need the
specific features of a paid, extended-validation certificate — the overwhelming majority of
real-world HTTPS deployments.

## How it works

> 📊 Diagram: the ACME protocol flow — `certbot` requesting a certificate for a domain, Let's
> Encrypt's server responding with a challenge (prove you control this domain by serving a specific
> file at a specific path, or by DNS TXT record), `certbot` satisfying the challenge, and Let's
> Encrypt issuing the certificate — followed by a scheduled renewal job running automatically well
> before the certificate's 90-day expiration.

```bash
$ sudo certbot --nginx -d example.com -d www.example.com
```

`certbot` automatically detects the Nginx `server` block for the domain, obtains a certificate
via HTTP domain validation (proving control by serving a file Let's Encrypt requests), and edits
the Nginx config to add the `listen 443 ssl` directive and certificate paths — no manual Nginx
config editing required.

Let's Encrypt certificates are deliberately short-lived (90 days), a design decision that
encourages automated renewal rather than long-lived, easily-forgotten manual certificates:

```bash
$ sudo certbot renew --dry-run
```

`certbot` installs a scheduled renewal job (typically a systemd timer, the modern equivalent
covered in [Module 17](../17-cron/systemd-timers.md)) automatically during installation, so
renewal happens without any manual intervention as long as the job itself remains healthy.

## Real-world example

A site's certificate expires unexpectedly, causing browsers to show a security warning to every
visitor — investigation reveals `certbot`'s renewal timer had been silently failing for months
because the server's disk was full, and no one was monitoring the renewal job's actual success or
failure. The fix isn't just renewing the certificate manually — it's adding monitoring for the
renewal job itself (the same monitoring discipline from [Module 20](../20-monitoring/index.md)),
since automatic renewal is only as reliable as the visibility into whether it's actually
succeeding.

## Syntax

```
certbot --nginx -d DOMAIN
certbot renew
```

## Commands

No new canonical command page — `certbot` doesn't earn one since this module's roadmap bullet
doesn't name it in backticks; its subcommands are shown illustratively above.

## Production example

```
$ sudo certbot certificates
Certificate Name: example.com
  Expiry Date: 2026-10-27 (VALID: 89 days)
  Certificate Path: /etc/letsencrypt/live/example.com/fullchain.pem
```

Checking a certificate's actual expiration and remaining validity — the first thing to verify when
investigating a TLS-related incident or auditing a fleet's certificate health.

## Do / Don't

| Do | Don't |
|---|---|
| Monitor the renewal job's actual success, not just assume it works | Set up auto-renewal once and never check on it again |
| Use `certbot renew --dry-run` to test renewal safely | Wait until a certificate actually expires to discover a renewal problem |
| Let `certbot` manage web server config integration | Manually edit certificate paths without understanding what `certbot` already automated |

## Common mistakes

- Setting up automatic renewal once and never monitoring whether it's actually succeeding,
  discovering a failure only when a certificate has already expired and users see a browser warning.
- Manually running certificate issuance repeatedly instead of trusting and monitoring the automated
  renewal job `certbot` already sets up.
- Not understanding that Let's Encrypt's 90-day expiration is deliberate, and treating a
  90-day-only certificate as unusually short rather than the expected, automation-encouraging norm.

## Best practices

- Monitor certificate expiration and renewal job health explicitly, not just trust that automation
  is silently working.
- Use `certbot renew --dry-run` periodically to verify the renewal process still works without
  waiting for an actual near-expiration renewal to find out.
- Let `certbot` manage the web server config integration rather than manually duplicating what it
  already automates.

## Exercises

1. Run `certbot certificates` on a system with an existing certificate and interpret its expiration
   information.
2. Explain why Let's Encrypt certificates expire every 90 days instead of the traditional one year.
3. Describe how you'd monitor whether `certbot`'s automatic renewal is actually succeeding over
   time.

## Quiz

**Q: Why do Let's Encrypt certificates deliberately expire every 90 days instead of longer?**
<details><summary>Show answer</summary>
The short lifespan encourages automated renewal rather than long-lived, easily-forgotten manual
certificates, limiting the damage window if a certificate or key is ever compromised.
</details>

**Q: What does `certbot --nginx` do beyond just obtaining a certificate?**
<details><summary>Show answer</summary>
It also automatically edits the relevant Nginx `server` block configuration to add the `listen 443
ssl` directive and certificate paths, without requiring manual config editing.
</details>

**Q: Why is monitoring the renewal job itself important, even with automatic renewal set up?**
<details><summary>Show answer</summary>
Automation can silently fail (e.g. due to disk space) — without monitoring, the first sign of a
problem may be an already-expired certificate showing a warning to real users.
</details>

## Interview questions

- Why do modern TLS certificates from Let's Encrypt expire so much faster than traditional
  certificates used to? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Let's Encrypt and `certbot` automate the entire TLS certificate lifecycle for free.
- 90-day expiration is a deliberate design choice encouraging automated renewal.
- `certbot` integrates directly with Nginx/Apache configuration, handling the config changes
  automatically.
- Automated renewal still needs monitoring — automation failing silently is how expired
  certificates reach production.

## Further Reading

- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)
- [Certbot Instructions](https://certbot.eff.org/)

## Related topics

- [Reverse Proxy Basics](reverse-proxy-basics.md)
- [Hosting a Website End-to-End](hosting-a-website-end-to-end.md)
