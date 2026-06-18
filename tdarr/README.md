# [Tdarr](https://github.com/haveagitgat/tdarr)

Distributed transcoding automation.

## Access
Web UI: `http://<your-ip>:8265`  
Server: port `8266`  
Internal node: port `8267`

## GPU acceleration
The container accesses `/dev/dri` for Intel QuickSync.  
If you use NVIDIA, replace `AddDevice` with the appropriate `--device nvidia.com/gpu=all` (or use CDI).

## Volumes
- Media libraries: `~/Videos/Movies`, `~/Videos/tvshows`, etc.
- Temporary transcode output: `/temp` (a local directory on the host; make sure it exists and is writable).

## Hardening
Only `NoNewPrivileges` was present. Uncomment it and the `Tmpfs=` lines if you want to lock down.  
Note: Tdarr’s `Tmpfs=` syntax is native Quadlet; it creates tmpfs mounts without the `Mount=` keyword.
