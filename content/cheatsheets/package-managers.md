---
title: "Package Managers Cheat Sheet"
description: "A single-page quick reference for Linux package management: apt/dpkg on Debian/Ubuntu, dnf/yum/rpm on RHEL-based distros, snap/flatpak, and building from source."
type: "cheatsheet"
module: "package-managers"
updatedAt: "2026-07-30"
keywords: ["linux package manager cheat sheet", "apt cheat sheet", "dnf cheat sheet", "yum cheat sheet", "rpm vs deb cheat sheet"]
canonicalUrl: "/cheatsheets/package-managers"
---

# Package Managers Cheat Sheet

The install/update/remove commands for each major Linux package family — pick the section for
your distro.

## Debian / Ubuntu — apt and dpkg

| Command | Description |
|---|---|
| [`apt`](../commands/apt.md) `update` | Refresh the local package index from repositories |
| `apt upgrade` | Upgrade all installed packages to their latest available version |
| `apt install {package}` | Install a package (and its dependencies) |
| `apt remove {package}` | Remove a package, keeping its config files |
| `apt purge {package}` | Remove a package and its config files |
| `apt search {term}` | Search available packages by name/description |
| [`dpkg`](../commands/dpkg.md) `-i package.deb` | Install a local `.deb` file directly |
| `dpkg -l` | List all installed packages |

Full explanation → [apt and dpkg](../docs/14-package-managers/apt-dpkg.md)

## RHEL / CentOS / Rocky — dnf, yum, rpm

| Command | Description |
|---|---|
| [`dnf`](../commands/dnf.md) `install {package}` | Install a package (modern default, replaces `yum`) |
| `dnf update` | Update all installed packages |
| `dnf remove {package}` | Remove a package |
| `dnf search {term}` | Search available packages |
| `yum install {package}` | Legacy equivalent of `dnf install`, still common on older systems |
| [`rpm`](../commands/rpm.md) `-ivh package.rpm` | Install a local `.rpm` file directly |
| `rpm -qa` | List all installed packages |

Full explanation → [yum, dnf, rpm](../docs/14-package-managers/yum-dnf-rpm.md)

## Universal package formats

| Command | Description |
|---|---|
| [`snap`](../commands/snap.md) `install {package}` | Install a self-contained Snap package (Canonical, sandboxed) |
| `snap list` | List installed snaps |
| `flatpak install {package}` | Install a self-contained Flatpak package (desktop-app focused) |
| `flatpak list` | List installed flatpaks |

Full explanation → [snap, flatpak](../docs/14-package-managers/snap-flatpak.md)

## Building from source

| Command | Description |
|---|---|
| `./configure` | Detect the system and generate a Makefile for the current environment |
| [`make`](../commands/make.md) | Compile the source according to the generated Makefile |
| `make install` | Install the compiled binaries system-wide (usually needs `sudo`) |

Full explanation → [Building from Source (make, ./configure)](../docs/14-package-managers/building-from-source-make-configure.md)

## Repositories and signing

| Concept | Description |
|---|---|
| `/etc/apt/sources.list` / `/etc/apt/sources.list.d/` | Where apt repositories are declared |
| `/etc/yum.repos.d/` | Where dnf/yum repositories are declared |
| GPG package signing | Repositories sign packages so the package manager can verify authenticity before install |

Full explanation → [Repository Management](../docs/14-package-managers/repository-management.md) · [Package Signing and Verification](../docs/14-package-managers/package-signing-and-verification.md)

## Related topics

- [Docker Basics Cheat Sheet](docker-basics.md)
- [Package Managers Module](../docs/14-package-managers/index.md)
- [Package Management Overview](../docs/14-package-managers/package-management-overview.md)
- [Cheat Sheets Hub](index.md)
