# [Plex Media Server](https://github.com/plexinc) + [Tautulli](https://github.com/tautulli/tautulli)

Plex with hardware transcoding and the Tautulli monitoring companion, both running rootless under Podman.  
Tautulli shares Plex’s network namespace, so it can access Plex at `localhost` without separate port publishing.

## Access
- **Plex web UI:** `http://<your-ip>:32400`
- **Tautulli web UI:** Because Tautulli uses `Network=container:plex`, it listens on the same IP as Plex.  
  The default Tautulli port is `8181`. If you need to reach it from outside the host, you can either:
  - Publish port `8181` from the **Plex** container (add `PublishPort=8181:8181` in `plex.container`), or
  - Access Tautulli via a reverse proxy that connects through Plex’s IP.
  
  By default, no extra port is published for Tautulli.

## Dependencies
- Plex `PropagatesStopTo=tautulli.service` → stopping Plex also stops Tautulli.

## Volumes
- **Plex config:** `~/plex/config`
- **Tautulli config:** `~/plex/tautulli-config`
- **Media folders:** `~/Videos/…` (adjust the container paths if your media is elsewhere)

## Hardware transcoding
`AddDevice=/dev/dri` enables Intel QuickSync / VAAPI.  
If you have an NVIDIA GPU, replace it with the appropriate CDI device.

## Hardening
All hardening directives are **commented out**. Uncomment the blocks marked “Hardening” and the corresponding tmpfs mounts if you want a read‑only root filesystem and reduced capabilities.  
The tmpfs sizes are tuned for transcoding and Plex’s runtime needs.

## First run
1. Start Plex: `systemctl --user enable --now plex.service`
2. Complete the Plex setup wizard at `http://<ip>:32400`.
3. Start Tautulli: `systemctl --user enable --now tautulli.service`
4. Open Tautulli and point it to Plex at `http://localhost:32400` (because they share the same network namespace).
