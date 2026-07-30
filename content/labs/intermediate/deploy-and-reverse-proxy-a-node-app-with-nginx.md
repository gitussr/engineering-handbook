---
title: "Lab: Deploy and Reverse-Proxy a Node App with Nginx"
description: "An intermediate Linux lab, framed as a real deploy ticket: get a Node app running as a managed service and reachable through Nginx as a reverse proxy."
type: "lab"
tier: "intermediate"
careerRelevance: ["devops", "backend", "wordpress-web-hosting"]
updatedAt: "2026-07-30"
keywords: ["nginx reverse proxy lab", "deploy node app linux lab", "systemd node app lab", "intermediate linux lab web server"]
canonicalUrl: "/labs/intermediate/deploy-and-reverse-proxy-a-node-app-with-nginx"
---

# Lab: Deploy and Reverse-Proxy a Node App with Nginx

🟡 Intermediate · Relevant for: DevOps, Backend, WordPress/Web Hosting

## Scenario

**Ticket #LX-202:** "The new internal API (a small Node app listening on port 3000) is only
reachable by SSH-tunneling into the server right now — that's not sustainable. Get it running as
a real service and put Nginx in front of it on port 80 so the team can hit it directly."

## Environment Setup

```bash
# A minimal stand-in Node app — no real Node/npm install required for this lab if you don't
# have it; a simple netcat-based responder simulates "a service listening on port 3000":
sudo tee /usr/local/bin/fake-node-app.sh > /dev/null <<'EOF'
#!/bin/bash
while true; do
  echo -e "HTTP/1.1 200 OK\r\nContent-Length: 11\r\n\r\nHello API!" | nc -l -p 3000 -q 1
done
EOF
sudo chmod +x /usr/local/bin/fake-node-app.sh
```

(If you have a real Node app available, use it instead — the lab's steps are identical either way.)

## Tasks

1. Run the app as a managed systemd service (not a manually-started foreground process) listening
   on `127.0.0.1:3000`.
2. Confirm the app responds locally before touching Nginx.
3. Configure Nginx as a reverse proxy so requests to port 80 are forwarded to the app.
4. Confirm the app is reachable through Nginx on port 80, and confirm port 3000 is not directly
   exposed to the outside network.

## Hints

<details><summary>Show hint</summary>
Bind the app to `127.0.0.1`, not `0.0.0.0` — Nginx on the same host can still reach it, but the
outside world can't reach port 3000 directly, only through Nginx on port 80. `proxy_pass` is the
Nginx directive that does the forwarding.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# systemd unit for the app
sudo tee /etc/systemd/system/internal-api.service > /dev/null <<'EOF'
[Unit]
Description=Internal API
After=network.target

[Service]
ExecStart=/usr/local/bin/fake-node-app.sh
Restart=on-failure
User=www-data

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now internal-api
systemctl status internal-api

# Confirm it responds locally
curl http://127.0.0.1:3000

# Nginx reverse proxy config
sudo tee /etc/nginx/sites-available/internal-api > /dev/null <<'EOF'
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
EOF
sudo ln -sf /etc/nginx/sites-available/internal-api /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx

# Confirm it's reachable through Nginx on port 80
curl http://localhost/
```

The app never needs a public-facing port of its own — Nginx is the only thing exposed, which is
exactly the point: one well-understood, well-hardened front door instead of every app managing
its own external exposure.
</details>

## What You Learned

- Why binding an app to `127.0.0.1` instead of `0.0.0.0` is a real (if partial) security boundary.
- How `proxy_pass` forwards requests from Nginx to a backend app.
- Why running an app under systemd (with `Restart=on-failure`) beats a manually-started process
  that dies the moment your SSH session ends.

## Related Modules

- [Services](../../docs/11-services/index.md)
- [Linux for Web Servers](../../docs/28-linux-for-web-servers/index.md)

## Related topics

- [systemctl Cheat Sheet](../../cheatsheets/systemctl.md)
- [Deploy and Reverse-Proxy a Web App Project](../../projects/intermediate/deploy-and-reverse-proxy-a-web-app-with-nginx.md)
- [Labs Hub](../index.md)
