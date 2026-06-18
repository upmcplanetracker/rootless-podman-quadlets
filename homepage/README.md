# Homepage Dashboard

A highly customizable application dashboard.

## Access
After starting: `http://<your-ip>:3000`

## First steps
- The dashboard configuration lives in `~/homepage/config/` (bookmarks, widgets, etc.)
- Custom icons can be placed in `~/homepage/icons/`

## Allowed hosts
Uncomment the `HOMEPAGE_ALLOWED_HOSTS` line in the `.container` file and set your domain(s) to avoid “host not allowed” errors.

## Docker socket
The container uses Podman’s socket to show container status.  
If you don’t need this feature, remove the `DOCKER_HOST` environment and the socket volume.

## Hardening
All hardening options are commented out. Uncomment the `NoNewPrivileges`, `ReadOnly`, and the corresponding `tmpfs` mounts together.
