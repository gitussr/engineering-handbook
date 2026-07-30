---
title: "Apache Install and Config"
description: "Apache HTTP Server installs and manages exactly like Nginx at the OS level — the differences that matter are its per-directory .htaccess overrides and process/thread worker model."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["28-linux-for-web-servers/nginx-install-and-config"]
relatedTopics: ["nginx-install-and-config", "reverse-proxy-basics"]
relatedCommands: ["apt", "systemctl"]
careerRelevance: ["devops", "wordpress-web-hosting", "backend", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#apache-vs-nginx-architecture"]
relatedCheatsheet: ""
furtherReading: [{"label": "Apache HTTP Server Documentation", "url": "https://httpd.apache.org/docs/"}]
nextTopic: "28-linux-for-web-servers/reverse-proxy-basics"
prevTopic: "28-linux-for-web-servers/nginx-install-and-config"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["apache install ubuntu", "apache config file explained", "htaccess explained", "apache vs nginx", "apache2ctl configtest"]
canonicalUrl: "/docs/linux-for-web-servers/apache-install-and-config"
---

# Apache Install and Config

🟢 Must Know · Relevant for: DevOps · WordPress/Web Hosting · Backend · Platform

> **TL;DR:** Apache HTTP Server installs and runs exactly like Nginx at the Linux level — the same
> package manager, the same systemd service model — but differs architecturally (process/thread
> workers versus Nginx's event-driven model) and supports per-directory `.htaccess` config
> overrides that Nginx deliberately doesn't.

## What is it?

Apache HTTP Server (often called "Apache" or "httpd") is, alongside Nginx, one of the two dominant
production web servers — installed and managed with the exact same Linux fundamentals
([Module 14](../14-package-managers/index.md) package management,
[Module 11](../11-services/index.md) systemd services) as any other service on this roadmap.

## Why does it exist?

Apache predates Nginx and established the process/thread-per-connection worker model, along with
`.htaccess` — a mechanism letting individual directories override server configuration without
editing the main config or restarting the server, historically valuable for shared hosting
environments where many users needed limited, self-service configuration control.

## Where is it used?

Extremely common in shared hosting, WordPress hosting specifically (`.htaccess` is deeply woven
into WordPress's URL-rewriting conventions), and any environment valuing Apache's mature module
ecosystem or its per-directory override flexibility.

## How it works

> 📊 Diagram: Apache's worker model (a pool of processes/threads, one handling each connection)
> shown side by side with Nginx's single event loop handling many connections asynchronously — and
> a second diagram showing a request hitting a directory with an `.htaccess` file, whose rules are
> applied on top of the main server config for just that directory.

```bash
$ sudo apt update && sudo apt install apache2
```

Its main configuration lives at `/etc/apache2/apache2.conf`, with site-specific config typically
under `/etc/apache2/sites-available/`:

```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/example.com

    <Directory /var/www/example.com>
        AllowOverride All
    </Directory>
</VirtualHost>
```

`AllowOverride All` is what enables `.htaccess` files inside that directory to apply their own
rules — the specific mechanism Nginx has no equivalent to, by design (Nginx reads its full
configuration once at startup/reload, with no per-request directory file lookups, which is part of
why it handles high concurrency more efficiently).

## Real-world example

A WordPress site migrating from Apache to Nginx breaks because its URL rewriting rules, written as
an `.htaccess` file, simply don't work under Nginx — Nginx has no `.htaccess` equivalent at all,
and the rewrite logic has to be translated directly into the main Nginx server block instead. This
is one of the most common real migration surprises between the two servers, directly traceable to
this architectural difference rather than a simple configuration syntax translation.

## Syntax

```
apache2ctl configtest
sudo systemctl reload apache2
```

## Commands

No new canonical command page — `apache2ctl`/`apachectl` don't earn a canonical page since this
module's roadmap bullet doesn't name a command in backticks; see
[`systemctl`](../../commands/systemctl.md) (Module 11) for service management and
[`apt`](../../commands/apt.md) (Module 14) for installation.

## Production example

```
$ sudo apache2ctl configtest
Syntax OK

$ sudo systemctl reload apache2
```

The same test-before-reload discipline as Nginx — `apache2ctl configtest` validates syntax before
`reload` applies it live.

## Do / Don't

| Do | Don't |
|---|---|
| Run `apache2ctl configtest` before every reload | Reload on an unvalidated config change |
| Scope `AllowOverride All` only to directories that genuinely need `.htaccess` | Enable it globally out of convenience |
| Understand the process/thread worker model's memory implications at scale | Assume Apache and Nginx perform identically under high concurrency |

## Common mistakes

- Skipping `apache2ctl configtest` before reloading, the same risk `nginx -t` protects against.
- Enabling `AllowOverride All` broadly instead of scoping it to directories that genuinely need
  `.htaccess`, since every request under an overridden directory pays a filesystem-lookup cost.
- Assuming a WordPress site's `.htaccess` rewrite rules will "just work" after migrating to Nginx,
  without realizing they need to be translated into Nginx's own configuration syntax entirely.

## Best practices

- Apply the same `configtest`-before-reload discipline from Nginx here — it's not
  server-specific, it's a universal production habit.
- Scope `AllowOverride` narrowly; broad `.htaccess` support has a real performance cost at scale.
- When migrating between Apache and Nginx, budget explicit time to translate `.htaccess`-based
  rewrite rules — they don't carry over automatically.

## Exercises

1. Install Apache and confirm it's running via `systemctl status apache2`.
2. Create a directory with `AllowOverride All` and a simple `.htaccess` rule, and confirm it
   applies without a server restart.
3. Explain, in your own words, why Nginx has no `.htaccess` equivalent by design.

## Quiz

**Q: What does `.htaccess` let you do that Nginx has no equivalent for?**
<details><summary>Show answer</summary>
Override server configuration on a per-directory basis without editing the main config or
restarting the server — Nginx reads its full config once and has no per-request directory file
lookup mechanism.
</details>

**Q: What's the Apache equivalent of `nginx -t`?**
<details><summary>Show answer</summary>
`apache2ctl configtest` (or `apachectl configtest`) — validates configuration syntax before a
reload.
</details>

**Q: Why might a WordPress site's URL rewriting break when migrating from Apache to Nginx?**
<details><summary>Show answer</summary>
Its rewrite rules are typically written as `.htaccess` directives, which Nginx doesn't support at
all — they need to be translated directly into Nginx's own configuration syntax.
</details>

## Interview questions

- What are the key architectural differences between Apache and Nginx, and when might each be
  preferred? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Apache installs and manages exactly like Nginx at the Linux OS level.
- `.htaccess` per-directory overrides are Apache's signature feature, with no Nginx equivalent.
- The process/thread worker model versus Nginx's event-driven model is the core architectural
  difference, with real performance implications at high concurrency.
- Always run `apache2ctl configtest` before reloading, the same discipline as `nginx -t`.

## Further Reading

- [Apache HTTP Server Documentation](https://httpd.apache.org/docs/)

## Related topics

- [Nginx Install and Config](nginx-install-and-config.md)
- [Reverse Proxy Basics](reverse-proxy-basics.md)
