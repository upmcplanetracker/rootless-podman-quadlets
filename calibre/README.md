# Calibre‑GUI + Calibre‑Web

Two‑container Calibre stack:  
- **Calibre GUI** (web‑based desktop via KasmVNC) for library management.  
- **Calibre‑Web** for a modern reading and browsing frontend.

## Access
- Calibre GUI: `http://<your-ip>:8090`  
- Calibre Web: `http://<your-ip>:8091`

## Dependencies
- `calibre‑web.service` binds to `calibre‑gui.service` and starts after it.  
- Stopping the GUI also stops the web frontend (`PropagatesStopTo`).

## Volumes
- **Calibre library:** `~/calibre` – both containers share this.  
- **Calibre GUI config:** `~/.config/calibre-gui`  
- **Calibre‑Web config:** `~/.config/calibre-web`

## Setup
1. Copy `calibre-gui.env.example` to `calibre-gui.env` and set a strong password.
2. In both `.container` files, uncomment `PUID`/`PGID` and set them to your host user (usually `1000:1000`).  
   **Do not use `0:0`** – that runs as root inside the container and can cause permission issues.
3. Start the GUI: `systemctl --user enable --now calibre-gui.service`
4. Once the GUI is up, start the web frontend: `systemctl --user enable --now calibre-web.service`
5. Open Calibre‑Web (`http://<your-ip>:8091`) and follow the setup wizard; point it to the database at `/books`.

## GPU acceleration (optional)
The GUI can use hardware acceleration for the VNC session.  
Uncomment the `AddDevice` and `PodmanArgs` lines in `calibre-gui.container` if you have an Intel GPU.

## Hardening
All security directives are commented out. Uncomment them together with the tmpfs mounts if you need a locked‑down container.  
The tmpfs sizes are generous for browsing and caching; adjust if disk usage is a concern.
