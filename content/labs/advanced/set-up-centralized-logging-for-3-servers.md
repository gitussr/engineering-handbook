---
title: "Lab: Set Up Centralized Logging for 3 Servers"
description: "An advanced Linux lab, framed as a real infrastructure ticket: forward logs from three separate servers to one central log host so incidents can be investigated from a single place."
type: "lab"
tier: "advanced"
careerRelevance: ["sre", "linux-administrator", "devops"]
updatedAt: "2026-07-30"
keywords: ["centralized logging lab linux", "rsyslog forwarding lab", "syslog server setup exercise", "advanced linux lab logging"]
canonicalUrl: "/labs/advanced/set-up-centralized-logging-for-3-servers"
---

# Lab: Set Up Centralized Logging for 3 Servers

🔴 Advanced · Relevant for: SRE, Linux Administrator, DevOps

## Scenario

**Ticket #LX-302:** "During the last incident, we had to SSH into three different app servers one
at a time to piece together what happened. Set up centralized logging so all three servers'
syslog output lands on one central log host, searchable from one place."

## Environment Setup

- One server designated as the central log host.
- Three servers (or VMs, or simulated with separate log directories if you're on one machine)
  acting as log sources.
- `rsyslog` installed on all of them (default on most distros).

## Tasks

1. Configure the central log host to accept incoming syslog messages over the network.
2. Configure each of the three source servers to forward their logs to the central host.
3. Confirm log messages generated on a source server actually arrive on the central host,
   labeled clearly enough to tell which server they came from.
4. Explain, in one sentence for the ticket, what this setup does and does not protect against
   (e.g. does it help if the central host itself goes down?).

## Hints

<details><summary>Show hint</summary>
On the receiving side, `rsyslog` needs its network listener modules enabled (`imudp`/`imtcp`,
often commented out by default) and a rule to write incoming remote logs to their own file rather
than mixing them into the local system's own `/var/log/syslog`. On the sending side, a single
forwarding line at the end of `rsyslog.conf` pointing at the central host is enough for a basic setup.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# --- On the central log host ---
sudo tee /etc/rsyslog.d/10-remote-listener.conf > /dev/null <<'EOF'
module(load="imudp")
input(type="imudp" port="514")

template(name="RemoteLogs" type="string" string="/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log")
if $fromhost-ip != '127.0.0.1' then -?RemoteLogs
& stop
EOF
sudo mkdir -p /var/log/remote
sudo systemctl restart rsyslog

# --- On each of the three source servers ---
echo '*.* @central-host-ip:514' | sudo tee -a /etc/rsyslog.conf
sudo systemctl restart rsyslog

# --- Verify, from a source server ---
logger "test message from source server"

# --- Back on the central host ---
find /var/log/remote -type f
cat /var/log/remote/{source-hostname}/logger.log
```

**One-sentence answer for the ticket:** this centralizes *visibility* into one place, but it does
not provide redundancy — if the central host itself is down, source servers still log locally
(nothing is lost), but nothing new is being aggregated centrally until it recovers.
</details>

## What You Learned

- How to configure `rsyslog` as both a network log receiver and a forwarding sender.
- How to template incoming remote logs by hostname so they don't get mixed together.
- Why "centralized" and "redundant" are different guarantees — this setup gives you one, not the
  other, and that distinction matters when explaining the setup's limits.

## Related Modules

- [Logs](../../docs/16-logs/index.md)
- [Networking](../../docs/12-networking/index.md)

## Related topics

- [Centralized Log Viewer and Rotation Pipeline Project](../../projects/intermediate/centralized-log-viewer-and-rotation-pipeline.md)
- [Centralized Logging Concepts](../../docs/16-logs/centralized-logging-concepts.md)
- [Labs Hub](../index.md)
