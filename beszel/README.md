[Beszel](https://github.com/henrygd/beszel) (Hub & Agent)
--------------------

A lightweight server resource monitoring platform consisting of a central dashboard (**Hub**) and lightweight metric collectors (**Agents**). For complete details, configuration options, and upstream updates, visit the official [Beszel GitHub Repository](https://github.com/henrygd/beszel).

### Beszel Hub (Dashboard)

Central web console and historical database.

#### Access

*   After starting the container, open: `http://<your-ip>:8090`

#### Hardening

*   The Hub is an excellent candidate for tight security wrapping. The Quadlet file includes active configurations for `NoNewPrivileges=yes`, `ReadOnly=yes`, and `DropCapability=all`.
*   To support the read-only root filesystem, memory-backed paths (`tmpfs`) are pre-configured for `/tmp`, `/run`, and `/var/log`.
*   Persistent metrics are safely isolated to your mapped `%h/beszel_data` volume.

* * *

### Beszel Agent (Collector)

The stateless system and container metric collector deployed on every host node you want to monitor.

#### Connection & Setup

*   **Requires a cryptographic signature:** You must pull an authentication key (starting with `ssh-ed25519`) from your live Beszel Hub UI and add it to the `KEY=` environment variable in the agent's Quadlet file before starting the service.
*   **Podman API Integration:** Requires the rootless Podman systemd socket to be active on the host machine (`systemctl --user enable --now podman.socket`). The Quadlet maps this live socket securely as read-only via `%t/podman/podman.sock`.

#### Hardening

*   Utilizes `NoNewPrivileges=yes` to enforce rootless process constraints.
*   Unlike the Hub, the Agent **cannot** run with `ReadOnly=yes` or `DropCapability=all`, as it requires direct, low-level access to the host's `/proc` and `/sys` filesystems to gather accurate kernel metrics, alongside `Network=host` to report true physical network interface throughput.
*   Optional `tmpfs` mounts are pre-staged in the file for users wishing to eliminate standard internal container scratch writes to NVMe storage.
