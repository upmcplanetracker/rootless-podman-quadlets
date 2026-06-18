# Omni‑tools

Collection of simple web tools (nginx‑based).

## Access
`http://<your-ip>:8999`

## Configuration
Place your custom `default.conf` and `.htpasswd` (basic auth) files in `~/omnitools/`.  
The container mounts them directly.

## Hardening
Lock down by uncommenting the `NoNewPrivileges`, `ReadOnly`, capability controls, and all `tmpfs` mounts.
