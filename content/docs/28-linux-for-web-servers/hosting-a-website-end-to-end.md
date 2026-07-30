---
title: "Hosting a Website End-to-End"
description: "Assembling every piece from this module into one working, HTTPS-served website — DNS pointing at a server, Nginx serving it, and certbot securing it, with each step traced back to the Linux fundamental underneath."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["28-linux-for-web-servers/ssl-tls-with-lets-encrypt-certbot"]
relatedTopics: ["ssl-tls-with-lets-encrypt-certbot", "deploying-php-python-node-apps-on-linux"]
relatedCommands: ["dig", "curl"]
careerRelevance: ["wordpress-web-hosting", "devops", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#dns-to-https-request-flow"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "28-linux-for-web-servers/deploying-php-python-node-apps-on-linux"
prevTopic: "28-linux-for-web-servers/ssl-tls-with-lets-encrypt-certbot"
estimatedReadingTime: 9
updatedAt: "2026-07-29"
keywords: ["host a website from scratch linux", "dns to website flow", "deploy static site nginx", "end to end website hosting checklist"]
canonicalUrl: "/docs/linux-for-web-servers/hosting-a-website-end-to-end"
---

# Hosting a Website End-to-End

🟢 Must Know · Relevant for: WordPress/Web Hosting · DevOps · Backend · Software Engineering

> **TL;DR:** Hosting a website end to end is DNS (Module 12) pointing a domain at a server, a
> firewall (Module 12/19) allowing the right ports, Nginx (this module) serving the content, and
> `certbot` (this module) securing it with HTTPS — five pieces already covered individually,
> assembled here into one working flow.

## What is it?

A complete walkthrough connecting a domain name to a live, HTTPS-served website: DNS resolution,
firewall rules, the web server itself, and TLS — tracing every step back to the specific earlier
module that actually teaches it.

## Why does it exist?

Each individual piece (DNS, firewalls, Nginx, TLS) has already been taught, but seeing them
connected end to end — in the order a request actually flows — is what turns individually-learned
skills into the ability to actually stand up a working website from nothing.

## Where is it used?

Launching any new website or web application on a self-managed server — the exact sequence every
WordPress/web hosting professional, and most DevOps/backend engineers, perform at least once when
setting up a new production site.

## How it works

> 📊 Diagram: a numbered end-to-end flow — (1) DNS A record pointing a domain at a server's public
> IP, (2) firewall rules allowing ports 80/443, (3) Nginx serving content on that IP, (4) `certbot`
> obtaining and installing a TLS certificate — each numbered step linked to the specific earlier
> module that teaches it, converging on a browser successfully loading `https://example.com`.

**1. DNS** ([Module 12](../12-networking/etc-hosts-resolv-conf.md)) — point the domain at the
server's public IP with an A record:

```
$ dig example.com +short
203.0.113.42
```

**2. Firewall** ([Module 12](../12-networking/firewalls-iptables-firewalld-ufw.md)/
[Module 19](../19-security/firewall-configuration-in-depth.md)) — allow inbound HTTP/HTTPS:

```bash
$ sudo ufw allow 80/tcp
$ sudo ufw allow 443/tcp
```

**3. Web server** ([Nginx Install and Config](nginx-install-and-config.md)) — serve the content:

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/example.com;
    index index.html;
}
```

**4. TLS** ([SSL/TLS with Let's Encrypt/certbot](ssl-tls-with-lets-encrypt-certbot.md)) — secure it:

```bash
$ sudo certbot --nginx -d example.com
```

## Real-world example

Someone hosting their first website follows a tutorial's Nginx configuration exactly, but the site
doesn't load — after checking Nginx's config (fine) and confirming the process is running (fine),
the actual problem turns out to be DNS: the domain's A record was still pointing at the domain
registrar's parking page, never updated to the new server's IP. This is a common first-time mistake
precisely because it sits outside the web server's own configuration entirely — a reminder that
"the site doesn't load" can fail at any of several independent layers, not just the one most
recently configured.

## Syntax

No new syntax — this page assembles DNS, firewall, Nginx, and `certbot` configuration already
covered individually across this module and Module 12.

## Commands

See [`dig`](../../commands/dig.md) (Module 12) for confirming DNS resolution and
[`curl`](../../commands/curl.md) (Module 12) for confirming the site actually responds — both
reused here as end-to-end verification steps.

## Production example

```
$ dig example.com +short
203.0.113.42

$ curl -I https://example.com
HTTP/2 200
server: nginx/1.24.0
```

Confirming DNS resolves to the expected IP, then confirming the site actually responds over HTTPS
with the expected server header — a two-command sanity check covering the full chain from domain
name to running web server.

## Do / Don't

| Do | Don't |
|---|---|
| Verify each layer independently (DNS, firewall, server, TLS) when troubleshooting | Assume "site doesn't load" is automatically a web server config problem |
| Confirm DNS propagation before debugging server config | Debug Nginx config first when DNS hasn't been checked yet |
| Test with `curl`/`dig` before assuming success | Trust a browser's cached result over a fresh command-line check |

## Common mistakes

- Assuming a non-loading site is automatically an Nginx configuration problem, without first
  ruling out DNS or firewall issues that sit entirely outside the web server's own config.
- Forgetting to open the firewall for port 443 after adding HTTPS via `certbot`, having only opened
  port 80 initially.
- Testing exclusively through a browser, which caches DNS and connection results, instead of using
  `dig`/`curl` for a fresh, unambiguous check at each layer.

## Best practices

- Verify each layer of the chain independently when troubleshooting — DNS resolution, firewall
  rules, server response, and TLS certificate validity are all separate potential failure points.
- Use `dig` and `curl` for unambiguous, uncached verification rather than relying on browser
  behavior alone.
- Document the full chain (DNS provider, server IP, firewall rules, certificate renewal) for any
  site you stand up, so troubleshooting later doesn't require re-deriving the setup from scratch.

## Exercises

1. Walk through hosting a simple static page end to end: point DNS at a server, open the firewall,
   configure Nginx, and secure it with `certbot`.
2. Use `dig` and `curl` to independently verify each layer of a website you have access to.
3. Explain why "the site doesn't load" can't be diagnosed by looking at Nginx's config alone.

## Quiz

**Q: What are the four layers this page connects, in order, for a website to actually load?**
<details><summary>Show answer</summary>
DNS resolution, firewall rules allowing the right ports, the web server serving content, and TLS
securing the connection.
</details>

**Q: Why might a newly-configured HTTPS site still fail to load even with a valid `certbot` certificate?**
<details><summary>Show answer</summary>
The firewall may still be blocking port 443, which is a separate layer from the certificate and
Nginx configuration itself.
</details>

**Q: Why is `dig`/`curl` a more reliable troubleshooting tool than a browser for verifying a site's setup?**
<details><summary>Show answer</summary>
A browser caches DNS results and connections, potentially masking a genuine change; `dig`/`curl`
give a fresh, unambiguous check of each specific layer.
</details>

## Interview questions

- Walk through everything that has to happen, in order, for `https://example.com` to load in a
  browser for the first time. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Hosting a website end to end is DNS, firewall, web server, and TLS — four independently
  verifiable layers, not one monolithic step.
- A "site doesn't load" symptom can originate at any of these layers, not just the most recently
  configured one.
- `dig` and `curl` provide fresh, uncached verification at each layer, more reliable than browser
  testing alone during setup or troubleshooting.
- This page connects skills from Module 12, Module 19, and this module into one practical flow.

## Related topics

- [SSL/TLS with Let's Encrypt/certbot](ssl-tls-with-lets-encrypt-certbot.md)
- [Deploying PHP/Python/Node Apps on Linux](deploying-php-python-node-apps-on-linux.md)
