# Changelog

One line per day, or per multi day session. For full entries, see `journal/`.

---

## 2026,09,26
- Immich attempt 3: second drive failed. `JBD2` journal I/O errors and repeated `EXT4-fs` inode read errors.
- Confirmed hardware failure via `dmesg` and `smartctl`.
- Abandoned Immich on the t520. Hardware cannot handle it.
- Removed failed drive from fstab.
- Started this documentation repo.

## 2026,09,25
- Immich attempt 2: Drive A failed for real this time.
- Same drive that caused the OS corruption on 2026,09,08.
- Diagnosed via `dmesg` and `smartctl`. Physical drive failure.
- Removed failed drive from fstab.

## 2026,09,23 to 09,24
- Hit read only filesystem boot loop.
- Diagnosed: missing root entry in `/etc/fstab`.
- Fixed by adding `UUID=... / ext4 defaults,noatime 0 1`.
- Learned the difference between UUID and PARTUUID.

## 2026,09,22
- Migrated Pi-hole from native install to Docker container.
- Port 80 already taken by beacon dashboard. Moved Pi-hole web UI to 8080.
- Pi-hole v6 uses internal port 8089, not 80.
- Tailscale firewall was blocking port 8080. Added iptables allow rule.

## 2026,09,21
- Fixed `BATABASE_URL` typo. Should have been `DATABASE_URL`.
- Added CORS middleware to beacon api.
- Wired beacon frontend to beacon api. First real live data.

## 2026,09,16 to 09,20
- No activity. Life got in the way.

## 2026,09,15
- Added Postgres and SQLAlchemy to beacon api.
- Full CRUD: GET, POST, DELETE on `/items`.
- Auto create tables on startup.
- First working containerized backend.

## 2026,09,14
- Started beacon api.
- FastAPI scaffold with `/health` and `/` endpoints.
- First containerized Python app.

## 2026,09,12 to 09,13
- Dockerized beacon with nginx alpine.
- Set up GitHub Actions to build and push to Docker Hub.
- First automated CI pipeline.
- Learned Docker images are snapshots, changes require rebuild.

## 2026,09,11
- Added add item modal with favicon auto fetch.
- Items appear with site icons.
- One reusable modal, multiple plus buttons via `data-list`.

## 2026,09,10
- Sidebar navigation with view switching.
- Status indicators, colored dots for running, deploying, planned, docs.
- System health bar at top of dashboard.
- Found a typo in a `data-view` attribute the hard way.

## 2026,09,09
- Started beacon dashboard.
- Static HTML, CSS, JS skeleton.
- Dark theme and dashboard layout pattern.

## 2026,09,08
- Planned the Beacon Suite: beacon, beacon api, beacon cluster.
- Wrote the DevOps roadmap doc, 4 month timeline.
- Also: Immich attempt 1. Drive A showed I/O errors, corrupted the OS, required fresh DietPi flash.
- Learned `critical medium error` means physical drive failure.

## 2026,09,07
- Installed Pi-hole natively via `dietpi-software`.
- Installed Immich self hosted media backup bare metal via `dietpi-software`.
- Configured Pi-hole as primary DNS for home network.
- First critical service.
- Immich immediately stressed the 4 GB t520.

## 2026,09,05
- Set up Node 1, HP t520, with DietPi.
- Static IP, SSH, base tooling.
- First homelab machine online.

## 2026,08,29
- Looked for a lighter OS for the t520.
- Found DietPi and its optional LXDE desktop.
- Easy flash and install.
- First boot. System was very snappy compared to Mint.

## 2026,08,28
- Wiped Windows 8 from HP t520 thin client.
- Wrote Linux Mint XFCE ISO to 32 GB USB using Rufus Portable.
- Booted from USB and installed Mint over Windows 8.
- Booted Linux Mint XFCE for the very first time.
- It was very sluggish. Took a long time to run anything.
- Machine later becomes Node 1.