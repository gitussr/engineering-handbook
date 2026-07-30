---
title: "Setting Up a Basic Monitoring Agent"
description: "Installing and exposing a metrics endpoint with node_exporter — turning a single host from unmonitored into scrapeable by a Prometheus-style stack."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["20-monitoring/monitoring-stacks-overview"]
relatedTopics: ["monitoring-stacks-overview", "alerting-concepts-and-thresholds"]
relatedCommands: ["systemctl", "curl"]
careerRelevance: ["devops", "sre", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#node-exporter-setup"]
furtherReading: [{"label": "node_exporter GitHub", "url": "https://github.com/prometheus/node_exporter"}]
relatedCheatsheet: ""
nextTopic: "20-monitoring/alerting-concepts-and-thresholds"
prevTopic: "20-monitoring/monitoring-stacks-overview"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["node_exporter setup", "install prometheus node exporter", "systemd service for monitoring agent", "expose metrics endpoint linux"]
canonicalUrl: "/docs/monitoring/setting-up-a-basic-monitoring-agent"
---

# Setting Up a Basic Monitoring Agent

🔴 Expert · Relevant for: DevOps · SRE · Cloud · Platform

> **TL;DR:** A monitoring agent (like Prometheus's `node_exporter`) runs as a systemd service on
> each host, exposing an HTTP endpoint with that host's metrics — turning a host from unmonitored
> into scrapeable, using nothing more exotic than a binary, a systemd unit file, and a firewall
> rule.

## What is it?

The practical steps to make a single Linux host visible to a monitoring stack
([Monitoring Stacks Overview](monitoring-stacks-overview.md)) — installing an agent, running it as
a managed service, and exposing its metrics for collection.

## Why does it exist?

The previous page's stacks are useless without data — an agent is what actually gathers a host's
CPU/memory/disk metrics and makes them available for collection. This page connects the abstract
"Prometheus scrapes targets" concept to the concrete steps of making one real host into a valid
target, using the systemd service-management skills from
[Module 11](../11-services/index.md) directly.

## Where is it used?

Every host that needs to appear in a monitoring stack's dashboards — provisioning automation
typically installs and enables a monitoring agent as a standard part of bringing up any new
server, alongside the [hardening checklist](../19-security/server-hardening-checklist.md).

## How it works

> 📊 Diagram: a single host with three labeled components — the `node_exporter` binary running as
> a systemd service (using the unit-file pattern from
> [Module 11](../11-services/index.md)), listening on a local port (default `9100`), with a
> firewall rule ([Module 19](../19-security/firewall-configuration-in-depth.md)) permitting the
> monitoring server's IP specifically to reach that port — and an arrow labeled "scrape" from a
> separate Prometheus server pulling from that endpoint on an interval.

**The setup sequence, using `node_exporter` (Prometheus's standard Linux host agent) as the
concrete example:**

1. **Download and install the binary** — a single self-contained executable, no complex
   dependencies.
2. **Create a systemd unit** — exactly the pattern from
   [Module 11: Services](../11-services/index.md), running the agent as a managed, auto-restarting
   background service rather than a manually-launched foreground process:

```ini
# /etc/systemd/system/node_exporter.service
[Unit]
Description=Prometheus Node Exporter
After=network.target

[Service]
User=node_exporter
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

3. **Enable and start it**, same as any other service:

```bash
sudo systemctl enable --now node_exporter
```

4. **Verify the metrics endpoint locally**:

```bash
curl -s http://localhost:9100/metrics | head
```

5. **Open the port to the monitoring server specifically** (never to the whole internet) — using
   the [Firewall Configuration in Depth](../19-security/firewall-configuration-in-depth.md)
   patterns from Module 19, scoping access to the monitoring server's address only.

## Real-world example

A team provisions a new server and forgets to install the monitoring agent as part of the standard
build — the server runs fine for weeks, invisible to the monitoring dashboard, until it silently
runs out of disk space with no alert ever having been possible, since it was never a scrape target
in the first place. Baking agent installation into the same provisioning automation that already
handles [hardening](../19-security/server-hardening-checklist.md) — so every new server is
monitored from the moment it exists — is exactly the fix, treating monitoring agent setup as a
non-optional step, not an afterthought.

## Syntax

```
systemctl enable --now SERVICE
curl http://localhost:PORT/metrics
```

## Commands

See [`systemctl`](../../commands/systemctl.md) (Module 11) and [`curl`](../../commands/curl.md)
(Module 12), both already covered — no new command page is needed for this topic, since setting up
an agent applies existing service-management and HTTP-request skills rather than introducing new
Linux commands.

## Production example

```
$ sudo systemctl enable --now node_exporter
Created symlink /etc/systemd/system/multi-user.target.wants/node_exporter.service → /etc/systemd/system/node_exporter.service

$ curl -s http://localhost:9100/metrics | grep node_memory_MemAvailable
node_memory_MemAvailable_bytes 2.936012e+09

$ sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="10.0.0.5/32" port port="9100" protocol="tcp" accept' --permanent
$ sudo firewall-cmd --reload
```

Enable the agent as a service, confirm it's actually exposing real metrics locally, then scope
firewall access to the monitoring server's specific address — the complete, minimal setup.

## Do / Don't

| Do | Don't |
|---|---|
| Run the agent as a managed systemd service | Launch it manually in a terminal session that dies on logout |
| Scope the metrics port to the monitoring server's address specifically | Open the metrics port to the entire internet |
| Verify the endpoint locally before troubleshooting the scrape from the monitoring server | Assume the agent is working without confirming its endpoint returns real data |
| Bake agent installation into standard provisioning | Add monitoring only after a server has already caused an unmonitored incident |

## Common mistakes

- Running the agent as a one-off foreground process instead of a systemd service, losing
  monitoring the moment the session that launched it ends or the host reboots.
- Exposing the metrics port to the entire internet instead of scoping it to the monitoring
  server's address, unnecessarily leaking system information.
- Not verifying the metrics endpoint locally first, wasting time debugging the monitoring server's
  side when the actual problem is the agent itself.

## Best practices

- Treat monitoring agent installation as a standard, non-optional part of server provisioning —
  alongside hardening, not after an incident reveals a gap.
- Always scope the metrics endpoint's firewall access to the specific monitoring server, following
  the same least-privilege principle as any other exposed port.
- Verify locally (`curl localhost:PORT/metrics`) before assuming a scrape failure is the monitoring
  server's fault rather than the agent's.

## Exercises

1. Write a minimal systemd unit file for a hypothetical monitoring agent binary at
   `/usr/local/bin/myagent`.
2. Explain why the metrics port should be scoped to the monitoring server's address rather than
   opened broadly.
3. Describe the verification step you'd perform locally before troubleshooting a scrape failure
   from the monitoring server's side.

## Quiz

**Q: Why should a monitoring agent run as a systemd service rather than a manually-launched process?**
<details><summary>Show answer</summary>
A systemd service persists across reboots and logouts and can be configured to auto-restart if it
crashes — a manually-launched foreground process stops the moment its terminal session ends.
</details>

**Q: Should the metrics endpoint port be open to the whole internet?**
<details><summary>Show answer</summary>
No — it should be scoped via firewall rules to the specific monitoring server's address, following
least-privilege access, the same principle from Module 19's hardening checklist.
</details>

**Q: What's the first thing to check if a monitoring server can't scrape a host's metrics?**
<details><summary>Show answer</summary>
Verify the metrics endpoint works locally on the host itself (`curl localhost:PORT/metrics`)
before assuming the problem is on the monitoring server's side.
</details>

## Interview questions

- Walk through the steps to make a new server visible to a Prometheus-based monitoring stack. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- A monitoring agent (e.g. `node_exporter`) runs as a systemd service, exposing an HTTP metrics
  endpoint for a monitoring stack to scrape.
- Setting it up applies existing service-management (Module 11) and firewall (Module 19) skills —
  no new Linux commands are introduced.
- Scope the metrics port to the monitoring server's address specifically, never the whole
  internet.
- Bake agent installation into standard provisioning so every server is monitored from the moment
  it exists.

## Related topics

- [Monitoring Stacks Overview](monitoring-stacks-overview.md)
- [Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md)
