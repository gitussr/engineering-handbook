---
title: "Lab: On-Call Simulation — Service Down at 2am"
description: "A production-tier Linux lab: you're paged at 2am for a service outage and have to diagnose it live using journalctl, systemctl, and ss, under the same time pressure as a real page."
type: "lab"
tier: "production"
careerRelevance: ["sre", "devops", "linux-administrator"]
updatedAt: "2026-07-30"
keywords: ["on call simulation lab linux", "2am incident lab", "production outage lab exercise", "sre on-call practice lab"]
canonicalUrl: "/labs/production/on-call-simulation-service-down-at-2am"
---

# Lab: On-Call Simulation — Service Down at 2am

🔴 Production · Relevant for: SRE, DevOps, Linux Administrator

## Scenario

**PagerDuty alert, 02:14:** "`checkout-api` health check failing — 3 consecutive checks, 0%
success rate. Customer-facing. Acknowledge and investigate." You have no other context yet — this
is the entire page.

## Environment Setup

```bash
sudo tee /usr/local/bin/checkout-api.sh > /dev/null <<'EOF'
#!/bin/sh
exec nc -l -p 8080 -q 1 -c 'echo -e "HTTP/1.1 200 OK\r\n\r\nok"'
EOF
sudo chmod +x /usr/local/bin/checkout-api.sh
sudo tee /etc/systemd/system/checkout-api.service > /dev/null <<'EOF'
[Unit]
Description=Checkout API
[Service]
ExecStart=/usr/local/bin/checkout-api.sh
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable --now checkout-api

# Simulate the outage
sudo systemctl stop checkout-api
sudo iptables -A INPUT -p tcp --dport 8080 -j DROP 2>/dev/null || true
```

## Tasks

1. Confirm whether the service process is even running before assuming anything more complex.
2. Check its recent logs for a specific failure reason.
3. Confirm whether the port it should be listening on is actually reachable.
4. Restore service.
5. Write a two-sentence incident summary: what was down, what was actually wrong, and what you did.

## Hints

<details><summary>Show hint</summary>
Work outside-in: is the process running at all (`systemctl status`)? If it's running, is it
actually listening on the expected port (`ss -tlnp`)? Only after both check out does the
investigation move to application-level logs. Don't skip straight to log-diving before confirming
the basics.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Step 1 — is the process even running?
systemctl status checkout-api
# Active: inactive (dead)  <- immediately explains the health check failures

# Step 2 — why did it stop / what does the log say?
journalctl -u checkout-api -n 50 --no-pager
# Shows it was cleanly stopped, not crashed — points toward a manual/deploy action, not a bug

# Step 3 — confirm nothing else is also blocking it (defense in depth check)
ss -tlnp | grep 8080
# (nothing listed yet, consistent with the service being down)

sudo iptables -L INPUT -n | grep 8080
# DROP  tcp  --  0.0.0.0/0  0.0.0.0/0  tcp dpt:8080   <- a second, independent problem

# Step 4 — restore service: fix both issues, not just the first one you find
sudo iptables -D INPUT -p tcp --dport 8080 -j DROP
sudo systemctl start checkout-api
ss -tlnp | grep 8080
systemctl status checkout-api
curl http://127.0.0.1:8080

# Step 5 — incident summary
# "checkout-api was down due to the service process being stopped, compounded by a leftover
# firewall rule blocking port 8080 even after restart. Both were identified via systemctl/ss and
# resolved; service confirmed healthy via direct curl check."
```

The realistic trap in this lab: fixing only the first problem you find (the stopped service)
would have looked like a fix — `systemctl start` succeeds — but the health check would keep
failing because of the second, independent firewall issue. Real incidents often stack like this.
</details>

## What You Learned

- The outside-in triage order: process running? → port listening? → application logs — cheapest
  checks first.
- Why confirming a fix with an actual request (`curl`), not just "the command didn't error," is
  the only real confirmation.
- That an incident can have more than one contributing cause, and stopping at the first one you
  find can leave the actual symptom unresolved.

## Related Modules

- [Services](../../docs/11-services/index.md)
- [Networking](../../docs/12-networking/index.md)
- [Logs](../../docs/16-logs/index.md)

## Related topics

- [systemctl Cheat Sheet](../../cheatsheets/systemctl.md)
- [Networking Cheat Sheet](../../cheatsheets/networking.md)
- [Labs Hub](../index.md)
