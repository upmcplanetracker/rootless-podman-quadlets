# Vert

Self‑hosted file converter with a clean interface.

## Access
`http://<your-ip>:3123`

## Environment
- `PUB_HOSTNAME` is set to `pihole1.lan:3123`. Change it to your own domain or IP.
- The Stripe key is the Vert project’s live public key (not a secret); you can leave it if you want to allow donations.

## Volumes
This container is stateless – no persistent volumes needed.

## Hardening
Uncomment the hardening block and all `Mount=type=tmpfs` lines to run with a read‑only rootfs and dropped capabilities.
