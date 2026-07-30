---
title: "SSH Config File: ~/.ssh/config"
description: "Naming connections so you never have to remember a hostname, port, key file, or username combination again."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["13-ssh/scp-sftp-rsync"]
relatedTopics: ["ssh-tunneling-and-port-forwarding"]
relatedCommands: ["ssh"]
careerRelevance: ["devops", "cloud", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ssh-config-file"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "13-ssh/ssh-tunneling-and-port-forwarding"
prevTopic: "13-ssh/scp-sftp-rsync"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["ssh config file example", "ssh config host alias", "ssh identityfile config", "ssh config port"]
canonicalUrl: "/docs/ssh/ssh-config-file"
---

# SSH Config File: ~/.ssh/config

🟡 Good to Know · Relevant for: DevOps · Cloud · SRE

> **TL;DR:** `~/.ssh/config` lets you define a short alias (`Host web1`) for a full connection —
> hostname, port, username, and key file — so `ssh web1` replaces
> `ssh -i ~/.ssh/prod_key -p 2222 deploy@203.0.113.5` every time.

## What is it?

A per-user configuration file that defines connection shortcuts and default options for `ssh`,
`scp`, `sftp`, and `rsync` (all of which use the same underlying SSH connection settings), so a
long, easy-to-forget connection string can be replaced with a short, memorable alias.

## Why does it exist?

Real infrastructure involves non-default ports, specific key files per environment, and
usernames that aren't your local username — remembering (or worse, retyping) the full correct
combination for every server, every time, doesn't scale and invites mistakes (like a typo in an
IP address for a production server).

## Where is it used?

Anyone regularly connecting to more than a couple of remote servers — the config file turns a
memorized (or looked-up) connection string into a short, typo-resistant name, and centralizes
per-server settings in one file instead of scattering them across shell history and scripts.

## How it works

> 📊 Diagram: a `~/.ssh/config` file with two `Host` blocks shown, each expanding into a full
> connection (hostname, port, user, identity file) — with `ssh web1` shown resolving to the first
> block's full settings automatically.

```
Host web1
    HostName 203.0.113.5
    User deploy
    Port 2222
    IdentityFile ~/.ssh/prod_key

Host jumpbox
    HostName bastion.example.com
    User admin
```

| Directive | Meaning |
|---|---|
| `Host` | The alias you'll actually type |
| `HostName` | The real hostname or IP |
| `User` | Username to connect as |
| `Port` | Non-default port, if applicable |
| `IdentityFile` | Which private key to use for this host |

Once defined, `ssh web1` (or `scp file.txt web1:/path/`) expands to the full configuration
automatically — no need to type or remember any of the underlying detail again.

## Real-world example

An engineer manages production, staging, and a bastion host, each with a different port, key
file, and username. Without a config file, every connection risks a typo in a rarely-typed
combination of flags. With `~/.ssh/config` defining `prod`, `staging`, and `jumpbox` as aliases,
connecting is just `ssh prod` — no chance of connecting to the wrong environment because of a
mistyped IP address or port.

## Commands

No new command — this file changes how [`ssh`](../../commands/ssh.md) (and `scp`/`sftp`/`rsync`,
already covered) resolve their connection settings, rather than introducing a new command.

## Production example

```
$ cat ~/.ssh/config
Host prod
    HostName 203.0.113.5
    User deploy
    Port 2222
    IdentityFile ~/.ssh/prod_key

$ ssh prod
```

`ssh prod` connects using every setting from the matching `Host` block, with no flags typed at
the command line at all.

## Do / Don't

| Do | Don't |
|---|---|
| Give production and non-production hosts clearly distinct alias names | Use ambiguous aliases (`server1`, `server2`) that don't indicate environment |
| Set restrictive permissions on `~/.ssh/config` and key files | Leave SSH config or key files world-readable |
| Use `IdentityFile` per host to keep the right key mapped to the right environment | Rely on trying every key manually until one happens to work |

## Common mistakes

- Using vague or easily-confused alias names, recreating the same "which environment am I
  connecting to" risk the config file was meant to eliminate.
- Not setting `IdentityFile` explicitly per host when different environments use different keys,
  leading to authentication failures or, worse, trying the wrong key against the wrong
  environment.
- Leaving `~/.ssh/config` or private key files with overly permissive file permissions —
  `ssh` will refuse to use a private key with permissions that are too open.

## Best practices

- Name aliases clearly and specifically (`prod-web-01`, not `server1`) to reduce the chance of
  connecting to the wrong environment.
- Set `IdentityFile` explicitly for every host block rather than relying on `ssh` trying keys in
  order.
- Keep `~/.ssh/config` and all private keys at restrictive permissions (`600`), consistent with
  [Module 07](../07-permissions/permission-model.md).

## Exercises

1. Create a `~/.ssh/config` entry for a host you regularly connect to, including `HostName`,
   `User`, and `Port` if non-default.
2. Connect using the new alias and confirm it resolves to the correct settings.
3. Explain in one sentence how a config file alias reduces the risk of connecting to the wrong
   server.

## Quiz

**Q: What does the `IdentityFile` directive in `~/.ssh/config` do?**
<details><summary>Show answer</summary>
Specifies which private key file to use when connecting to that specific `Host` alias, ensuring
the correct key is used automatically for that environment.
</details>

**Q: Besides `ssh` itself, what other tools benefit from `~/.ssh/config` aliases?**
<details><summary>Show answer</summary>
`scp`, `sftp`, and `rsync` — they all use the same underlying SSH connection settings and
recognize the same host aliases.
</details>

## Interview questions

- Why would you use an SSH config file instead of typing full connection strings every time? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `~/.ssh/config` defines named aliases (`Host` blocks) for full connection settings.
- `HostName`, `User`, `Port`, and `IdentityFile` are the most commonly used directives.
- `scp`, `sftp`, and `rsync` all recognize the same host aliases as `ssh`.
- Clear, specific alias names reduce the risk of connecting to the wrong environment.

## Related topics

- [SSH Tunneling and Port Forwarding](ssh-tunneling-and-port-forwarding.md)
- [scp, sftp, rsync](scp-sftp-rsync.md)
