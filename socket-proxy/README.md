# Socket Proxy

A secure TCP proxy for Podman’s socket, built from the linuxserver/socket-proxy image.
It allows remote tools (like a dashboard or Portainer) to talk to Podman without exposing the raw socket.

## Access
The proxy listens on **port 2375** (plain HTTP, no TLS).  
Use only on a trusted local network, or put it behind a reverse proxy with SSL.

## Socket path
The container mounts `%t/podman/podman.sock` (your user’s runtime dir) as `/var/run/docker.sock` read‑only.  
No hardcoded user IDs.

## Hardening
Uncomment `NoNewPrivileges`, `ReadOnly`, `DropCapability=ALL`, and both `Mount=type=tmpfs` lines.
