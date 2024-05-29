# MekaDB Management Server

This public MekaDB distribution is designed to run MekaDB with one command.
Since MekaDB requires a storage provider e.g. Opensearch, this repo provides a binary which handles configuring both MekaDB and starting a storage provider.

This mode can be used in production environments which do not require distributed storage but is not recommended and rather is intended for developers to use to develop applications and integration tests against MekaDB before deploying to production.

For production, you should set `MEKADB_OPENSEARCH_HOST` which points to a production setup Opensearch cluster.

# Environment variables

* MEKADB_OPENSEARCH_HOST

# Docker
In order to perform various actions with Docker, we need to run in `privileged` mode on Linux/Mac.
This mode is not supported on Windows but is also not necessary.

# How to install

Installation method depends on your OS, CPU and preferred method. Below is an overview of various options and their current availability.


|                  |         |                             |                  |                 |                  |
|------------------|---------|-----------------------------|------------------|-----------------|------------------|
|                  | Windows | Linux (Debian based/Ubuntu) | Linux RHEL based | Mac (x86/Intel) | Mac (ARM64/M1-4) |
| Binary ARM64     |    ✅   |              ✅             |         ✅       |        ✅       |         ✅      |
| Binary AMD64     |    ✅   |              ✅             |         ✅       |        ✅       |         ✅      |
| Docker ARM64     |    ✅   |              ✅             |         ✅       |        ✅       |         ✅      |
| Docker AMD64     |    ✅   |              ✅             |         ✅       |        ✅       |         ✅      |
| .deb ARM64       |    ‒    |              🚧             |         ‒        |        ‒        |         ‒        |
| .deb AMD64       |    ‒    |              🚧             |         ‒        |        ‒        |         ‒        |
| .rpm ARM64       |    ‒    |              ‒              |         🚧       |        ‒        |         ‒        |
| .rpm AMD64       |    ‒    |              ‒              |         🚧       |        ‒        |         ‒        |
| .pkg ARM64       |    ‒    |              ‒              |         ‒        |        🚧       |         🚧       |
| .pkg ARM64       |    ‒    |              ‒              |         ‒        |        🚧       |         🚧       |
| Chocolatey ARM64 |    🚧   |              ‒              |         ‒        |        ‒        |         ‒        |
| Chocolatey AMD64 |    🚧   |              ‒              |         ‒        |        ‒        |         ‒        |
| Homebrew ARM64   |    ‒    |              ‒              |         ‒        |        🚧       |         🚧       |
| Homebrew AMD64   |    ‒    |              ‒              |         ‒        |        🚧       |         🚧       |
| MacPorts ARM64   |    ‒    |              ‒              |         ‒        |        🚧       |         🚧       |
| MacPorts AMD64   |    ‒    |              ‒              |         ‒        |        🚧       |         🚧       |

✅ = live/available now
🚧 = planned/in progress
‒  = not applicable to this OS

