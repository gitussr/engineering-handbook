---
title: "Nginx Install and Config"
description: "Installing Nginx as an ordinary systemd-managed package and understanding its block-based configuration file structure — server blocks, locations, and the config-test-before-reload discipline."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["27-linux-for-cybersecurity/hardening-for-compliance-cis-benchmarks"]
relatedTopics: ["apache-install-and-config", "reverse-proxy-basics"]
relatedCommands: ["apt", "systemctl"]
careerRelevance: ["devops", "wordpress-web-hosting", "backend", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#nginx-config-test-before-reload"]
relatedCheatsheet: ""
furtherReading: [{"label": "Nginx Documentation", "url": "https://nginx.org/en/docs/"}]
nextTopic: "28-linux-for-web-servers/apache-install-and-config"
prevTopic: "27-linux-for-cybersecurity/hardening-for-compliance-cis-benchmarks"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["nginx install ubuntu", "nginx config file explained", "nginx server block", "nginx test config before reload", "nginx systemd"]
canonicalUrl: "/docs/linux-for-web-servers/nginx-install-and-config"
---

# Nginx Install and Config

🟢 Must Know · Relevant for: DevOps · WordPress/Web Hosting · Backend · Platform

> **TL;DR:** Nginx installs as an ordinary package ([Module 14](../14-package-managers/index.md))
> and runs as an ordinary systemd service ([Module 11](../11-services/index.md)) — the only
> genuinely new skill is its block-based configuration syntax, and the ironclad discipline of
> testing a config before reloading it.

## What is it?

Nginx is a high-performance web server and reverse proxy, installed and managed on Linux exactly
like any other package and service already covered in this roadmap, configured through a
distinctive block-based (`server { }`, `location { }`) configuration syntax.

## Why does it exist?

Serving static files and proxying requests to backend applications is one of the most common
server workloads, and Nginx was built specifically to handle very high numbers of concurrent
connections efficiently — a design goal that shows up in its event-driven architecture, distinct
from Apache's traditionally process/thread-per-connection model (see
[Apache Install and Config](apache-install-and-config.md) for the comparison).

## Where is it used?

Serving static websites, acting as a reverse proxy in front of application servers (see
[Reverse Proxy Basics](reverse-proxy-basics.md)), and terminating TLS (see
[SSL/TLS with Let's Encrypt/certbot](ssl-tls-with-lets-encrypt-certbot.md)) — one of the two most
common web servers in production alongside Apache.

## How it works

> 📊 Diagram: Nginx's configuration hierarchy shown as nested blocks — `http { }` at the top,
> containing one or more `server { }` blocks (one per site/domain), each containing one or more
> `location { }` blocks (one per URL path pattern) — with an arrow showing `nginx -t` validating
> the whole hierarchy before `systemctl reload nginx` applies it live.

Installing Nginx uses the exact package management already covered in
[Module 14](../14-package-managers/apt-dpkg.md):

```bash
$ sudo apt update && sudo apt install nginx
```

Its configuration lives at `/etc/nginx/nginx.conf` and `/etc/nginx/sites-available/`, structured in
nested blocks:

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /var/www/example.com;
        index index.html;
    }
}
```

`server` blocks define one site (identified by `server_name` and/or `listen` port); `location`
blocks define how specific URL paths within that site are handled.

## Real-world example

An engineer edits an Nginx config to add a new `location` block, then immediately runs
`systemctl restart nginx` without checking the syntax first — and a single typo (a missing
semicolon) takes the entire site down, not just the new route, because Nginx refuses to start at
all with an invalid config. Running `sudo nginx -t` before every reload — which validates syntax
without affecting the running service — would have caught the typo and prevented the outage
entirely, which is why this test-before-reload step is treated as non-negotiable in production.

## Syntax

```
nginx -t
sudo systemctl reload nginx
```

## Commands

No new canonical command page — `nginx` itself doesn't earn a canonical page since this module's
roadmap bullet doesn't name it in backticks; see [`systemctl`](../../commands/systemctl.md)
(Module 11) for managing it as a service and [`apt`](../../commands/apt.md) (Module 14) for
installing it.

## Production example

```
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

$ sudo systemctl reload nginx
```

Testing before reloading — `reload` (not `restart`) applies configuration changes without dropping
existing connections, the standard production approach.

## Do / Don't

| Do | Don't |
|---|---|
| Run `nginx -t` before every reload | Reload or restart on an unvalidated config change |
| Use `reload` for config changes | Use `restart`, which drops active connections unnecessarily |
| Keep one `server` block per site in its own file under `sites-available/` | Cram every site's configuration into a single monolithic file |

## Common mistakes

- Reloading or restarting Nginx after a config change without running `nginx -t` first, risking an
  outage from a syntax error that a ten-second test would have caught.
- Using `systemctl restart` instead of `reload` for routine config changes, unnecessarily dropping
  active client connections.
- Editing `sites-enabled/` files directly instead of `sites-available/` with a symlink, losing the
  standard convention that makes enabling/disabling sites a simple symlink operation.

## Best practices

- Make `nginx -t` before every reload an absolute, non-negotiable habit — script it if manual
  discipline isn't reliable enough.
- Use `reload`, not `restart`, for configuration changes that don't require a full process restart.
- Keep each site in its own file under `sites-available/`, symlinked into `sites-enabled/` — the
  standard Debian/Ubuntu Nginx convention for enabling/disabling sites cleanly.

## Exercises

1. Install Nginx and confirm it's running via `systemctl status nginx`.
2. Create a new `server` block for a test site, intentionally introduce a syntax error, and confirm
   `nginx -t` catches it before reloading.
3. Explain the difference between `location /` and a more specific `location /api/` block.

## Quiz

**Q: What command should always run before reloading Nginx after a config change?**
<details><summary>Show answer</summary>
`nginx -t`, which validates configuration syntax without affecting the running service.
</details>

**Q: Why is `systemctl reload nginx` generally preferred over `restart` for config changes?**
<details><summary>Show answer</summary>
`reload` applies configuration changes without dropping existing client connections, while
`restart` unnecessarily interrupts them.
</details>

**Q: What's the relationship between a `server` block and a `location` block?**
<details><summary>Show answer</summary>
A `server` block defines one site (by domain/port); `location` blocks inside it define how
specific URL paths within that site are handled.
</details>

## Interview questions

- Why is testing an Nginx configuration before reloading considered non-negotiable in production? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Nginx installs and runs as an ordinary package and systemd service — nothing new there.
- Its block-based configuration (`server`, `location`) is the genuinely new syntax this topic
  introduces.
- `nginx -t` before every reload is a non-negotiable production habit.
- `reload`, not `restart`, is the standard way to apply configuration changes without dropping
  connections.

## Further Reading

- [Nginx Documentation](https://nginx.org/en/docs/)

## Related topics

- [Apache Install and Config](apache-install-and-config.md)
- [Reverse Proxy Basics](reverse-proxy-basics.md)
