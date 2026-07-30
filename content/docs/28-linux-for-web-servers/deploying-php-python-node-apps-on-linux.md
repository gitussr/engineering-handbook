---
title: "Deploying PHP/Python/Node Apps on Linux"
description: "A static site is a reverse proxy's simplest case — a real application needs a process to keep running, which is exactly what systemd (or PHP-FPM) is for, fronted by the same Nginx reverse proxy pattern already covered."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["28-linux-for-web-servers/hosting-a-website-end-to-end"]
relatedTopics: ["hosting-a-website-end-to-end", "reverse-proxy-basics"]
relatedCommands: ["systemctl"]
careerRelevance: ["backend", "software-engineering", "devops", "wordpress-web-hosting"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#keep-app-process-running-in-production"]
relatedCheatsheet: ""
furtherReading: [{"label": "PHP-FPM Documentation", "url": "https://www.php.net/manual/en/install.fpm.php"}]
nextTopic: "28-linux-for-web-servers/troubleshooting-web-server-errors"
prevTopic: "28-linux-for-web-servers/hosting-a-website-end-to-end"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["deploy node app linux systemd", "deploy python app gunicorn nginx", "php-fpm explained", "keep node process running production", "deploy app linux server"]
canonicalUrl: "/docs/linux-for-web-servers/deploying-php-python-node-apps-on-linux"
---

# Deploying PHP/Python/Node Apps on Linux

🟡 Good to Know · Relevant for: Backend · Software Engineering · DevOps · WordPress/Web Hosting

> **TL;DR:** A real application (unlike a static site) needs its process kept running, restarted
> on crash, and started on boot — exactly what systemd (Module 11) already does for any service;
> PHP is the one exception, handled instead by PHP-FPM, a dedicated process manager Nginx talks to
> directly.

## What is it?

The pattern for running a real backend application (Python, Node.js, or PHP) behind the reverse
proxy setup already covered in this module — specifically, how the application's own process stays
running reliably, distinct from how a static HTML file just sits on disk with nothing to keep
alive.

## Why does it exist?

A static site has no process — Nginx just reads a file from disk. A Python or Node.js application
is a running program that can crash, needs restarting on boot, and needs its own dependency
environment. That's an ordinary [Module 11](../11-services/index.md) systemd-service problem, not
a new one specific to web development.

## Where is it used?

Deploying any dynamic backend application — a Flask/Django API, an Express.js app, a WordPress
site (PHP) — onto a Linux server for the first time, the deployment pattern nearly every backend
and full-stack engineer eventually performs directly.

## How it works

> 📊 Diagram: three deployment patterns side by side — a Node.js/Python app run as a systemd
> service on a local port, fronted by Nginx as a reverse proxy (this module); and PHP handled
> differently, with Nginx passing requests directly to PHP-FPM's process pool via a socket, rather
> than through a locally-listening port — labeled clearly as PHP's distinct architecture.

**Python/Node.js** — run as an ordinary systemd service, fronted by the reverse proxy pattern from
[Reverse Proxy Basics](reverse-proxy-basics.md):

```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=My Node App
After=network.target

[Service]
ExecStart=/usr/bin/node /srv/myapp/server.js
Restart=on-failure
User=www-data

[Install]
WantedBy=multi-user.target
```

```bash
$ sudo systemctl enable --now myapp.service
```

Nginx then proxies to it exactly as shown in
[Reverse Proxy Basics](reverse-proxy-basics.md#how-it-works) — `proxy_pass http://127.0.0.1:3000/;`.

**PHP** — architecturally different: rather than listening on its own port, PHP-FPM (FastCGI
Process Manager) runs a pool of PHP worker processes that Nginx communicates with directly via a
Unix socket:

```nginx
location ~ \.php$ {
    fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```

## Real-world example

A developer deploys a Node.js app by SSHing in and running `node server.js` directly in a terminal
session, and the app goes down the moment they log out, because the process was tied to that
terminal session the entire time. Wrapping it in a systemd unit with `Restart=on-failure` fixes both
problems at once: the process survives the SSH session ending, and systemd automatically restarts
it if it ever crashes — exactly the process-supervision guarantee [Module 11](../11-services/index.md)
already covers for any service, applied here to a custom application instead of a system daemon.

## Syntax

```
systemctl enable --now myapp.service
```

## Commands

See [`systemctl`](../../commands/systemctl.md) (Module 11) — reused here to manage a custom
application's process exactly as it manages any other service.

## Production example

```
$ sudo systemctl status myapp.service
● myapp.service - My Node App
     Loaded: loaded (/etc/systemd/system/myapp.service; enabled)
     Active: active (running) since Wed 2026-07-29 10:15:03 UTC

$ curl -I http://127.0.0.1:3000
HTTP/1.1 200 OK
```

Confirming the application's systemd-managed process is running, then confirming it actually
responds on its local port before Nginx ever gets involved — isolating whether a problem is in the
application or in the reverse proxy layer.

## Do / Don't

| Do | Don't |
|---|---|
| Run application processes as systemd services | Run them in a detached terminal session (`nohup`, screen) |
| Set `Restart=on-failure` for automatic recovery | Leave a crashed process down until someone notices |
| Test the backend directly before assuming Nginx is misconfigured | Assume every "site down" symptom is a reverse proxy problem |

## Common mistakes

- Running an application directly in an SSH session instead of as a systemd service, losing it the
  moment the session ends.
- Not setting `Restart=on-failure`, leaving a crashed application down indefinitely until someone
  manually notices and restarts it.
- Assuming PHP works the same way as Node.js/Python deployment (a locally-listening port), missing
  that PHP-FPM uses a fundamentally different socket-based architecture.

## Best practices

- Always run a real application as a systemd service, never in a bare terminal session.
- Set `Restart=on-failure` (and consider `RestartSec` for backoff) so transient crashes recover
  automatically without manual intervention.
- Test the application directly on its local port before assuming a symptom is in the Nginx
  reverse proxy layer — isolate which layer actually has the problem first.

## Exercises

1. Write a systemd unit file for a simple script or application and enable it as a service.
2. Explain why PHP's deployment architecture (PHP-FPM) differs from how Node.js or Python
   applications are typically run.
3. Given a "502 Bad Gateway" error from Nginx, describe how you'd determine whether the problem is
   in Nginx or in the backend application itself.

## Quiz

**Q: Why should a real application be run as a systemd service instead of directly in an SSH session?**
<details><summary>Show answer</summary>
A bare SSH-session process dies when the session ends; a systemd service keeps running
independently and can be configured to restart automatically on failure.
</details>

**Q: How does PHP's deployment architecture differ from a typical Node.js/Python deployment?**
<details><summary>Show answer</summary>
PHP-FPM runs a pool of worker processes that Nginx communicates with via a Unix socket, rather than
the application listening on its own local port the way Node.js/Python apps typically do.
</details>

**Q: What's the first troubleshooting step when Nginx returns a "502 Bad Gateway" for a proxied application?**
<details><summary>Show answer</summary>
Test the backend application directly on its local port/socket to determine whether the problem is
in the application itself or in the Nginx proxy configuration.
</details>

## Interview questions

- How do you ensure a deployed Node.js or Python application keeps running reliably in production? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A real application needs process supervision that a static site doesn't — systemd (Module 11)
  already provides this.
- PHP's PHP-FPM architecture is a genuine exception, using a socket rather than a listening port.
- Testing the backend application directly, independent of Nginx, isolates which layer actually has
  a problem.
- Running an application in a bare terminal session instead of as a service is a common,
  easily-avoided deployment mistake.

## Further Reading

- [PHP-FPM Documentation](https://www.php.net/manual/en/install.fpm.php)

## Related topics

- [Hosting a Website End-to-End](hosting-a-website-end-to-end.md)
- [Reverse Proxy Basics](reverse-proxy-basics.md)
