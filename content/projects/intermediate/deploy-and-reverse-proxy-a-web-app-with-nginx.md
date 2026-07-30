---
title: "Project: Deploy and Reverse-Proxy a Node App with Nginx"
description: "An intermediate Linux project: deploy a small web application on a Linux server, put Nginx in front of it as a reverse proxy, and secure it with TLS."
type: "project"
tier: "intermediate"
careerRelevance: ["devops", "wordpress-web-hosting", "backend"]
updatedAt: "2026-07-30"
keywords: ["nginx reverse proxy project", "deploy node app linux project", "lets encrypt certbot project", "web server deployment project"]
canonicalUrl: "/projects/intermediate/deploy-and-reverse-proxy-a-web-app-with-nginx"
---

# Project: Deploy and Reverse-Proxy a Node App with Nginx

🟡 Intermediate · Relevant for: DevOps, WordPress/Web Hosting, Backend

## Goal

Take a small web application (any stack — Node, Python, or PHP) from "runs on my machine" to
"runs as a real service on a server, behind Nginx, over HTTPS" — the deployment shape behind most
real production web apps.

## Requirements

- The application runs as a managed systemd service, not a manually-started foreground process.
- Nginx is configured as a reverse proxy in front of the app, not exposing the app's port directly.
- A real domain or subdomain (or a free dynamic DNS name) points at the server.
- TLS is configured via Let's Encrypt/certbot, with HTTP automatically redirecting to HTTPS.
- The app survives a server reboot without manual intervention (service enabled, Nginx enabled).

## Suggested Approach

1. Get the app running manually on the server first, confirming it works over `curl localhost:{port}`
   before touching Nginx or systemd at all.
2. Write a systemd unit file for the app with `Restart=on-failure`, and confirm `systemctl restart`
   actually brings it back cleanly.
3. Configure Nginx as a reverse proxy to the app's local port, and confirm it works over plain
   HTTP through the domain before adding TLS — isolate one variable at a time.
4. Run certbot to obtain and install a certificate, confirm the automatic HTTP→HTTPS redirect,
   and check the certificate's auto-renewal is actually configured (`certbot renew --dry-run`).
5. Reboot the server and confirm everything comes back on its own — this is the step people skip
   and the one that actually matters in production.

## Stretch Goals

- Add a second app instance and load-balance between them at the Nginx layer.
- Add basic security headers (HSTS, X-Frame-Options) to the Nginx config.
- Set up a simple deploy script that pulls the latest code and restarts the service.
- Add uptime monitoring (even a simple cron + curl check) that alerts if the site goes down.

## Related Modules

- [Services](../../docs/11-services/index.md) — running the app as a systemd service
- [Linux for Web Servers](../../docs/28-linux-for-web-servers/index.md) — Nginx, reverse proxy, TLS, deployment
- [Networking](../../docs/12-networking/index.md) — DNS and firewall basics

## Related topics

- [systemctl Cheat Sheet](../../cheatsheets/systemctl.md)
- [Networking Cheat Sheet](../../cheatsheets/networking.md)
- [Centralized Log Viewer Project](centralized-log-viewer-and-rotation-pipeline.md)
- [Projects Hub](../index.md)
