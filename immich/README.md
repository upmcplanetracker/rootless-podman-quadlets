# [Immich](https://github.com/immich-app/immich) (Self-hosted Photo Manager)
==================================

Complete rootless Immich stack using Podman Quadlets. All containers share an internal `immich.network` and run without root privileges.

Required Components
-------------------

*   **PostgreSQL** – database (with pgvector)
*   **Redis** (Valkey) – job queue
*   **Machine Learning** – smart search and facial recognition
*   **Immich Server** – main API and web UI

Optional Components
-------------------

*   **Google Photos Sync** (`immich-googlephotos`) – import your Google Photos library.
*   **Public Proxy** (`immich-public-proxy`) – share albums without exposing your server.

Files for optional containers are in the `optional/` subfolder.

Access
------

*   Immich Web UI: `http://<your-server>:2283`
*   Google Photos Sync dashboard: `http://<your-server>:8087` (if enabled)
*   Public Proxy: `http://<your-server>:3003` (if enabled)

Prerequisites
-------------

1.  **Create the shared network:**
    
        cp network/immich.network ~/.config/containers/systemd/
        systemctl --user daemon-reload
        systemctl --user start immich-network.service
    
2.  **Prepare secrets:**
    
        cp immich-postgres.env.example immich-postgres.env
        cp immich-server.env.example immich-server.env
    
    Edit the files and set your database credentials (make them match).
    
3.  **Adjust media volume:** The server mounts `~/Pictures` as `/data`. Change this in `immich-server.container` if your photos are stored elsewhere.

Setup
-----

1.  Start the database: `systemctl --user enable --now immich-postgres.service`
2.  Start Redis: `systemctl --user enable --now immich-redis.service`
3.  Start Machine Learning: `systemctl --user enable --now immich-machine-learning.service`
4.  Start Immich Server: `systemctl --user enable --now immich-server.service`
5.  Open `http://your-ip:2283` and create your admin account.

Optional Components Setup
-------------------------

### Google Photos Sync

    cp optional/immich-googlephotos.env.example optional/immich-googlephotos.env
    # Edit the env file with your Immich API key and server URL
    systemctl --user enable --now immich-googlephotos.service

### Public Proxy

    # Edit optional/immich-public-proxy.container and set PUBLIC_BASE_URL and IMMICH_URL
    systemctl --user enable --now immich-public-proxy.service

Hardening
---------

All security directives are commented out. Uncomment the blocks labeled “Hardening” and the corresponding tmpfs mounts to lock down each container. Resource limits are also commented out; adjust to your system’s capacity.

Volumes
-------

*   **PostgreSQL data:** `~/immich-app/postgres`
*   **ML model cache:** `~/immich-app/model-cache`
*   **Photos:** `~/Pictures` (default)
*   **Google Photos sync:** `~/immich-app/gphotos2immich/`

GPU Support
-----------

The server and machine-learning containers can use Intel QuickSync. Uncomment the `AddDevice=/dev/dri` and `GroupAdd=keep-groups` lines if you have an Intel GPU. For NVIDIA, use the appropriate CDI device.

Notes
-----

*   The Immich server image expects the `release` tag. You can switch to `release`, `latest`, or a specific version.
*   If you choose not to run the Machine Learning container, remove its dependency from `immich-server.container`.
*   All `.env` files must be placed next to the `.container` files (`~/.config/containers/systemd/`).
