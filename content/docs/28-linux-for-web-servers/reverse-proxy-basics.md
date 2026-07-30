---
title: "Reverse Proxy Basics"
description: "A reverse proxy sits in front of one or more backend applications, forwarding client requests to them — the mechanism behind load balancing, TLS termination, and running multiple apps behind one public IP."
module: "28-linux-for-web-servers"
moduleTitle: "Linux for Web Servers"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["28-linux-for-web-servers/apache-install-and-config"]
relatedTopics: ["nginx-install-and-config", "ssl-tls-with-lets-encrypt-certbot"]
relatedCommands: []
careerRelevance: ["devops", "backend", "sre", "platform"]
relatedLabs: ["intermediate/deploy-and-reverse-proxy-a-node-app-with-nginx"]
relatedInterviewQuestions: ["intermediate#reverse-proxy-vs-forward-proxy"]
relatedCheatsheet: "networking"
furtherReading: [{"label": "Nginx Reverse Proxy Guide", "url": "https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/"}]
nextTopic: "28-linux-for-web-servers/ssl-tls-with-lets-encrypt-certbot"
prevTopic: "28-linux-for-web-servers/apache-install-and-config"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["reverse proxy explained", "nginx reverse proxy config", "reverse proxy vs load balancer", "proxy_pass nginx", "reverse proxy vs forward proxy"]
canonicalUrl: "/docs/linux-for-web-servers/reverse-proxy-basics"
---

# Reverse Proxy Basics

🟡 Good to Know · Relevant for: DevOps · Backend · SRE · Platform

> **TL;DR:** A reverse proxy sits between clients and one or more backend applications, forwarding
> requests on their behalf — it's how one public IP and port 443 can front multiple applications,
> terminate TLS in one place, and distribute load across several backend instances.

## What is it?

A server (commonly Nginx or Apache, configured per
[Nginx Install and Config](nginx-install-and-config.md)) that receives client requests and forwards
them to one or more backend servers, returning the backend's response to the client as if the proxy
had handled the request itself.

## Why does it exist?

Application servers (Node.js, Python/Gunicorn, Java) are rarely designed to face the internet
directly — a reverse proxy adds a battle-tested layer in front of them handling TLS termination,
static file serving, request buffering, and distributing traffic across multiple backend instances,
none of which the application itself needs to implement.

## Where is it used?

Nearly every production web application: a reverse proxy in front of an app server, in front of
multiple app instances for load distribution, or in front of several different applications sharing
one domain (routing `/api` to one backend and `/` to another, for instance).

## How it works

> 📊 Diagram: a client request hitting a reverse proxy on port 443, which terminates TLS,
> inspects the request path, and forwards it over plain HTTP to one of several backend application
> instances on a private network — with the response flowing back through the proxy to the client,
> which never talks to the backend directly.

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    location /api/ {
        proxy_pass http://127.0.0.1:5000/;
    }

    location / {
        proxy_pass http://127.0.0.1:3000/;
    }
}
```

`proxy_pass` forwards matching requests to a backend address — here, requests to `/api/` go to one
backend (perhaps a Python API on port 5000) while everything else goes to another (a Node.js app on
port 3000), both fronted by a single Nginx instance handling TLS and routing.

## Real-world example

A team runs their Node.js application directly exposed to the internet on port 3000, and discovers
during a traffic spike that a single slow client connection can tie up one of the application's
limited worker threads for its entire duration. Putting Nginx in front of it as a reverse proxy —
buffering slow client connections itself and only handing the application server complete, ready
requests — removes that entire class of problem from the application's responsibility, letting the
purpose-built reverse proxy handle the messiness of real-world client connections.

## Syntax

```
location PATH {
    proxy_pass http://BACKEND_ADDRESS;
}
```

## Commands

No new command on this page — reverse proxy configuration extends
[Nginx Install and Config's](nginx-install-and-config.md) `location` block syntax rather than
introducing a new command.

## Production example

```
$ curl -I https://example.com/api/status
HTTP/2 200
server: nginx/1.24.0

$ curl -I http://127.0.0.1:5000/status
HTTP/1.1 200 OK
Werkzeug/2.3.0 Python/3.11.4
```

Confirming from outside that the public-facing response reports the proxy (`nginx`), while the
backend, reachable directly only from the server itself, reports its actual application server —
exactly the separation a reverse proxy is meant to provide.

## Do / Don't

| Do | Don't |
|---|---|
| Terminate TLS at the reverse proxy | Implement TLS termination separately in every backend app |
| Keep backend applications on a private, non-internet-facing address | Expose application servers directly to the internet |
| Route by path (`/api/`, `/`) for multiple apps behind one domain | Run separate public IPs for every small backend service |

## Common mistakes

- Exposing an application server directly to the internet instead of putting a reverse proxy in
  front of it, losing TLS termination, buffering, and routing flexibility.
- Forgetting to pass through headers the backend needs (like the original client IP via
  `X-Forwarded-For`), leaving the backend unable to see real client information.
- Not distinguishing a reverse proxy (fronting servers, transparent to clients) from a forward
  proxy (fronting clients, transparent to servers) — the two solve entirely different problems.

## Best practices

- Terminate TLS at the reverse proxy layer, keeping backend-to-proxy traffic on a private network
  segment.
- Pass through standard forwarding headers (`X-Forwarded-For`, `X-Forwarded-Proto`) so backend
  applications can still see genuine client information.
- Use path-based or domain-based routing to consolidate multiple applications behind a single
  reverse proxy where it makes sense operationally.

## Exercises

1. Configure Nginx as a reverse proxy in front of a simple local application (any language,
   listening on a local port), and confirm requests flow through correctly.
2. Explain the difference between a reverse proxy and a forward proxy.
3. Research what `X-Forwarded-For` is for and why a backend application would need it.

## Quiz

**Q: What does a reverse proxy do, in one sentence?**
<details><summary>Show answer</summary>
It receives client requests and forwards them to one or more backend servers, returning the
backend's response as if it had handled the request itself.
</details>

**Q: Why is TLS typically terminated at the reverse proxy rather than in each backend application?**
<details><summary>Show answer</summary>
It centralizes certificate management and TLS configuration in one place instead of duplicating it
across every backend application.
</details>

**Q: What's the key difference between a reverse proxy and a forward proxy?**
<details><summary>Show answer</summary>
A reverse proxy fronts servers (clients don't know which backend actually handled their request); a
forward proxy fronts clients (servers don't see the original client directly) — they solve opposite
problems.
</details>

## Interview questions

- Explain the difference between a reverse proxy and a forward proxy, with a real example of each. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A reverse proxy sits between clients and backend applications, forwarding requests transparently.
- It centralizes TLS termination, routing, and buffering so backend applications don't need to
  implement them.
- Backend applications should stay on a private network, never exposed directly to the internet.
- A reverse proxy and a forward proxy solve opposite problems despite the similar name.

## Further Reading

- [Nginx Reverse Proxy Guide](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)

## Related topics

- [Nginx Install and Config](nginx-install-and-config.md)
- [SSL/TLS with Let's Encrypt/certbot](ssl-tls-with-lets-encrypt-certbot.md)
