---
title: "Linux for Web Servers"
description: "Module 28 of the Linux roadmap — the fourth and final career-branch module, on Nginx/Apache install and config, reverse proxying, Let's Encrypt/certbot TLS, hosting a site end-to-end, deploying PHP/Python/Node apps, and troubleshooting web server errors."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
type: "module-index"
nextTopic: "28-linux-for-web-servers/nginx-install-and-config"
updatedAt: "2026-07-29"
canonicalUrl: "/docs/linux-for-web-servers"
---

# Linux for Web Servers

Module 28 of 34 · Stage: Production Engineer · Previous: [27 Linux for Cybersecurity](../27-linux-for-cybersecurity/index.md)

The fourth and final career-branch module, buildable in any order relative to Modules 25-27. This
module covers Nginx and Apache installation and configuration, the reverse proxy pattern
underlying nearly every production web deployment, free automated TLS via Let's Encrypt/`certbot`,
assembling DNS + firewall + web server + TLS into one working end-to-end site, deploying real
PHP/Python/Node applications behind that setup, and interpreting HTTP error codes to troubleshoot
web server failures precisely. This closes the four career-branch modules (25-28); Module 29
onward is cross-cutting reference material (interview questions, cheat sheets, labs, projects)
plus Module 33's general troubleshooting methodology and Module 34's final revision.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Nginx Install and Config](nginx-install-and-config.md) | 🟢 Must Know |
| [Apache Install and Config](apache-install-and-config.md) | 🟢 Must Know |
| [Reverse Proxy Basics](reverse-proxy-basics.md) | 🟡 Good to Know |
| [SSL/TLS with Let's Encrypt/certbot](ssl-tls-with-lets-encrypt-certbot.md) | 🟡 Good to Know |
| [Hosting a Website End-to-End](hosting-a-website-end-to-end.md) | 🟢 Must Know |
| [Deploying PHP/Python/Node Apps on Linux](deploying-php-python-node-apps-on-linux.md) | 🟡 Good to Know |
| [Troubleshooting Web Server Errors](troubleshooting-web-server-errors.md) | 🟢 Must Know |

## What you should be able to do after this module

- Install and configure both Nginx and Apache, and explain their key architectural differences.
- Configure a reverse proxy in front of a backend application.
- Set up free, auto-renewing TLS with Let's Encrypt/`certbot`, and monitor that renewal actually
  keeps working.
- Stand up a complete website end to end: DNS, firewall, web server, and TLS.
- Deploy a real PHP, Python, or Node.js application as a properly supervised, restart-on-failure
  systemd service.
- Diagnose a web server error (500/502/503/504) to the specific layer actually responsible, using
  the reverse proxy's own error log.

## Known, intentional gaps in this module

- No canonical command pages were created in this module — no roadmap bullet names a specific
  command in backticks. `nginx`, `apache2ctl`/`apachectl`, and `certbot` are shown illustratively
  throughout, the same treatment given to Ansible/Terraform in Module 26.
- WordPress itself (as a specific CMS/application) is out of scope for this module — the roadmap
  bullets teach the underlying web server, proxy, TLS, and deployment mechanics that any
  application, including WordPress, runs on top of, not WordPress-specific administration.
- This module's last topic forward-references
  [`33-troubleshooting/troubleshooting-methodology`](../33-troubleshooting/troubleshooting-methodology.md)
  rather than a Module 29 page — Modules 29-32 are cross-cutting hub sections (interview
  questions, cheat sheets, projects, labs) reached through their own hub URLs, not sequential
  per-topic pages with a comparable prevTopic/nextTopic chain, per this roadmap's own note that
  "Modules 29-34 are cross-cutting... not a linear read-last block." Module 33 is the next module
  using this documentation's standard per-topic template, and its "Troubleshooting methodology"
  opening topic is a natural sequel to this module's closing troubleshooting topic. Modules 29-32
  remain fully linked from every topic page in this roadmap via `relatedInterviewQuestions`,
  `relatedCheatsheet`, and `relatedLabs` frontmatter, per Rule 9's knowledge-graph design — they
  are not skipped, just reached differently than the sequential chain.

**Previous module:** [27 Linux for Cybersecurity](../27-linux-for-cybersecurity/index.md)
**Next:** [33 Troubleshooting →](../33-troubleshooting/index.md) (see note above on why this module
skips ahead in the sequential chain; Modules 29-32 are cross-cutting hubs linked from throughout
this roadmap rather than a next-in-sequence read)
