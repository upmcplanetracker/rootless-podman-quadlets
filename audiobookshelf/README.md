# Audiobookshelf

[Audiobookshelf](https://github.com/advplyr/audiobookshelf) is a self‑hosted audiobook and podcast server.

## First run
- The web interface will be at `http://<your-ip>:13378`
- You’ll be prompted to create an admin account and add your first library
- Point the library folders to `/audiobooks` and `/podcasts` (the internal container paths)

## Volumes
Make sure `~/audiobooks` and `~/podcasts` exist on the host and contain your media, or change the `Volume=` lines in the `.container` file.

## Reverse proxy
The container listens on port 80 internally. I publish it on `13378` to avoid privileged ports. A reverse proxy can map port 443 to `localhost:13378`.

## Hardening
The `.container` file includes commented‑out hardening options. To enable them, read the comments and uncomment both the capabilities block and the tmpfs block.
