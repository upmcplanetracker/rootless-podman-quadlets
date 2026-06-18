# Tdarr Remote Node

A separate transcoding node that connects to a Tdarr server.

## Access
The node binds to port **8267**. No web UI — it communicates with the server specified in `serverURL`.

## Configuration
- Set `serverURL` to your Tdarr server’s address (e.g. `http://pihole1.lan:8266`).
- `nodeName` and `nodeID` identify this node in the Tdarr dashboard.

## GPU
`AddDevice=/dev/dri` enables Intel QuickSync.  
If using NVIDIA, you’ll need the appropriate CDI device.

## Volumes
- Media paths: `/media/Videos/…` (adjust to your mount points).
- Temporary transcode output: `/temp` must exist on the host and be writable.

## Hardening
Uncomment `NoNewPrivileges` and the four `Tmpfs=` lines together.  
The node uses tmpfs for caching and temporary configuration files.
