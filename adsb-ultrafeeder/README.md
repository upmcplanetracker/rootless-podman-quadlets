# [ADSB Ultrafeeder](https://github.com/sdr-enthusiasts Stack) (Rootless Podman + Quadlet)

Complete, secure deployment of the SDR‑Enthusiasts ADSB feeder stack using Podman Quadlets.  
All containers run **rootless**, share an internal `adsb` network, and auto‑update from registries.

## Architecture
- **Hardware receivers (optional):** `airspy` (Airspy ADSB) and `dump978` (UAT).  
  *Skip these if you don’t have the hardware.*
- **Ultrafeeder:** core aggregator, Tar1090 web interface on port `8081`.
- **Feeders:** push data to Flightradar24, FR24, OpenSky, PlaneFinder, PlaneWatch, RadarBox, ADSBHub, RadarVirtuel.
- **Analytics:** PlaneFence (web on `8088`, config on `9999`).

## Prerequisites
- Podman rootless with linger enabled (see main repo README).
- The `adsb` network must be created: `cp network/adsb.network ~/.config/containers/systemd/` and `systemctl --user daemon-reload`.
- Decide if you’ll use hardware receivers. If not, you must adjust `READSB_NET_CONNECTOR` in the ultrafeeder container.

## Quick setup
1. **Copy all `.container` files** to `~/.config/containers/systemd/`.
2. **Copy every `.env.example` to `.env`** (in the same directory) and fill in your secrets:
   - Coordinates, UUIDs, API keys, sharing keys.
3. **Generate your `ULTRAFEEDER_CONFIG`** using [SDR‑Enthusiasts config tool](https://sdr-enthusiasts.github.io/) and paste it into `ultrafeeder.env`.
4. **Enable the network** (only once):  
   `systemctl --user daemon-reload`  
   `systemctl --user start adsb-network.service` (Quadlet will create this from `adsb.network`).
5. **Start the core:**  
   `systemctl --user enable --now ultrafeeder.service`
6. **Start optional receivers** (if hardware present):  
   `systemctl --user enable --now airspy.service dump978.service`
7. **Start feeders:**  
   Enable the services you need, e.g.:  
   `systemctl --user enable --now fr24.service opensky.service pfclient.service ...`

## Hardening
All containers have hardening options **commented out**.  
Uncomment the blocks labelled “Hardening” and “Temporary filesystems” when you’re ready to lock down each service.

## Notes
- If you don’t use Airspy or dump978, edit `ultrafeeder.container` and remove the `READSB_NET_CONNECTOR` line or change it to match your data source.
- Logs: `journalctl --user -u ultrafeeder`
