# Homelab Umbrel Community App Store

Personal Community App Store for umbrelOS. Currently ships one app:

- **HDHR2XMLTV** (`homelab-hdhr2xmltv`) — reads a fresh `DeviceAuth` token
  from your HDHomeRun tuner and serves SiliconDust's official guide data as
  XMLTV over HTTP, for Jellyfin / Plex / Emby. No Docker-in-Docker needed.

## Add this store to umbrelOS

1. Push this folder to a public GitHub repo (e.g. `homelab-umbrel-store`).
2. In umbrelOS, go to **App Store > ⋯ (menu) > Community App Stores** (or
   **Settings > Community App Stores**, depending on version) and add the
   repo URL: `https://github.com/<you>/homelab-umbrel-store`
3. Update the `icon:` URL in `homelab-hdhr2xmltv/umbrel-app.yml` to point at
   your repo, otherwise the listing shows a broken icon:
   `https://raw.githubusercontent.com/<you>/homelab-umbrel-store/main/homelab-hdhr2xmltv/icon.svg`
4. Install **HDHR2XMLTV** from the new "Homelab" section of the App Store.

## Configure

- **Tuner IP** (default `192.168.4.64`) and **cache TTL** (default `60`
  minutes) are editable in the app's settings in umbrelOS after install.
- The app's status page opens at `http://umbrel.local:8215` (port `8215`
  was verified free across the official App Store).

## Point Jellyfin at it

1. Jellyfin **Dashboard > Live TV > +** next to **TV Guide Data Providers**.
2. Choose **XMLTV**, URL `http://umbrel.local:8215/xmltv`
   (or `http://<umbrel-ip>:8215/xmltv` if `.local` doesn't resolve for you).
3. Save, then **⋯ > Map Channels** on the provider and match each tuner
   channel (e.g. `2.1 WJBK`) to its guide entry.
4. Also useful: `http://umbrel.local:8215/lineup.m3u` (channel playlist),
   `http://umbrel.local:8215/guide.xml` (XMLTV alias),
   `http://umbrel.local:8215/cache` (cache status).

The feed endpoints are LAN-public by design (media servers fetch them
server-side and can't do Umbrel login). Your tuner's `DeviceAuth` token
stays server-side inside the container and is never served.

## Notes

- Image `ghcr.io/patrickjmcd/hdhr2xmltv` only publishes a `latest` tag, so
  the compose file pins it by multi-arch manifest digest
  (`linux/amd64` + `linux/arm64`). Re-pin when updating:
  `docker buildx imagetools inspect ghcr.io/patrickjmcd/hdhr2xmltv:latest`
- Upstream: https://github.com/patrickjmcd/hdhr2xmltv (MIT)
- Guide API docs: https://github.com/Silicondust/documentation/wiki/XMLTV-Guide-Data
