Podman Rootless Containers
==========================

* A collection of Podman my rootless container stacks, tested and running on **Ubuntu 26.04** with **Podman 5.7.0**, with optional security additions that do not break the container.  
* All services use Quadlet files (`~/.config/containers/systemd/`) so they integrate natively with systemd and start automatically under your user.
* Please visit the individual Github repos to find out more about how to configure them, recent changes and issues, and to give them stars.
* *You will need to edit these .container and .env files to fit your situation. These work on my setup, which is an Intel 13i7 with 2.0 tb NVME, 64 gb memory, and a Intel GPU. These are not meant to be "drop in and go" but rather a starting point for those of you trying to get a rootless container spun up from a template that was working on my system. You will find changes I made based on my filesystem layout, open ports, and GPU.*

* * *

Why this repo?
--------------

*   **Rootless by default** – every container runs without any root privileges, even during setup.
*   **Arguably safer than Docker** – Podman’s daemonless architecture and user‑namespace isolation reduce the attack surface. No privileged `dockerd` socket, no “root equivalent” container escapes.
*   **Quadlet for systemd** – no need for `podman-compose`; just drop a `.container` file and enable with `systemctl --user`.
*   Podman is gaining traction, and rootless mode is finally production‑ready for most workloads.
*   There are good arguments on both sides for rootless vs. rootful mode. I am not advocating one position over the other. I offered these rootless Podmans for those interested in exploring rootless, as I have spent many hours ironing out the network and user id issues that come with rootless.

   
* * *

Quick start (for any container in this repo)
--------------------------------------------

### 1\. Install Podman (Ubuntu 26.04)

    sudo apt update
    sudo apt install podman

No Docker or `docker-compose` needed. Podman includes all the tools.

### 2\. Enable linger for your user _(critical for autostart at boot)_

This allows your user’s systemd session to run even when you are not logged in, so containers start automatically at system boot.

    sudo loginctl enable-linger $USER

Verify with `loginctl show-user $USER | grep Linger` – should show `Linger=yes`.

### 3\. Secure your environment files (critical)

This repo uses separate `.env` files for secrets (passwords, API keys). **Never commit the real `.env` files** — only the `.env.example` templates are stored here.

After you copy an `.env.example` to `.env` and fill it in, lock it down immediately so that if a container is compromised, the secrets remain unreadable:

    sudo chmod 600 ~/.config/containers/systemd/*.env

This ensures only your user can read the files — not the container’s processes, and not other users on the system.

### 4\. Set up the Quadlet directory

    mkdir -p ~/.config/containers/systemd

_If the directory already exists, that’s fine._

### 5\. Grab a container definition

Navigate to the service directory you want, then copy the `.container` file:

    cp ~/rootless-podman-quadlets/vert/vert.container ~/.config/containers/systemd/

**Edit the file with `nano` (without `sudo` – it’s your user dir)**:

    nano ~/.config/containers/systemd/vert.container

Adjust volumes, environment variables, or uncomment hardening options as needed.

### 6\. Pull the image and generate the systemd unit

Quadlet automatically creates a systemd service from the `.container` file:

    systemctl --user daemon-reload

Then pull the image (optional but gives you immediate feedback):

    podman pull <image-name>   
    # or let systemd pull it on start

### 7\. Edit the container to fit your situation and needs

I have given you the .container files that work on my system.  Everyone has different systems with different processors, memory, file layouts, etc.  If in doubt go back to the canonical source - the GitHub for the specific container - and read through the documentation. These containers will not work on everyone's setup without tweaking.

### 8\. Enable and start the container

    systemctl --user start vert

Check status:

    systemctl --user status vert
    podman ps

The service will survive reboots and log to the journal (`journalctl --user -u vert` or `podman logs vert`).

* * *

Saving / backing up your containers
-----------------------------------

Your persistent data lives in the volumes you defined (e.g., `~/containers/vert/`).  
To back up a container’s state:

    podman stop vert
    tar -czf vert ~/containers/vert/

The Quadlet file itself is your “infrastructure as code” – keep it safe in this git repo.

* * *

Common troubleshooting
----------------------

*   **`systemctl --user` commands fail with `Failed to connect to bus`**  
    Make sure your user session is running `systemd --user`. Usually it’s automatic on login, but if not: `systemctl --user start default.target`.
*   **Linger is not enabled / containers don’t start at boot**  
    Run `sudo loginctl enable-linger $USER` to allow the user manager to run at boot without a login. This is mandatory for unattended servers.
*   **Ports below 1024**  
    Rootless Podman can’t bind to privileged ports by default. Add `net.ipv4.ip_unprivileged_port_start=80` to `/etc/sysctl.d/99-rootless.conf` and reboot, or use a high port with a reverse proxy.
*   **`WARN[0000] Failed to load cached network config`**  
    Harmless; happens when networks are recreated. Ignore unless connectivity breaks.

* * *

Useful resources
----------------

*   [Podman rootless tutorial](https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md)
*   [Quadlet (systemd generator) guide](https://www.redhat.com/sysadmin/quadlet-podman)
*   [Awesome Podman](https://github.com/containers/awesome-podman)

* * *

_These configurations were tested on Ubuntu 26.04 with Podman 5.7.0 (apt installed) on an Intel NUC 13i7 with 64 gb memory and a 2 tb NVME SSD. They should work on any recent systemd distro with a Podman version ≥ 4.4, but your mileage may vary._
