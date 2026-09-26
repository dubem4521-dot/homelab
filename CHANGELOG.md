# Changelog

One-line-per-day summary. For full entries, see `journal/`.

---

## 2026,09,26
- Immich attempt #2: second drive failed (I/O errors, JBD2 journal failure)
- Abandoned Immich on the t520 (hardware can't handle it)
- Started this documentation repo

## 2026,09,25
- Immich attempt #1: first 500GB drive showed I/O errors
- Diagnosed via `dmesg` and `smartctl` — physical drive failure
- Removed failed drive from fstab

## 2026,09,23 – 09,24
- Hit read-only filesystem boot loop
- Diagnosed: missing root entry in `/etc/fstab`
- Fixed by adding `UUID=... / ext4 defaults,noatime 0 1`
- Learned difference between UUID and PARTUUID

## 2026,09,22
- Migrated Pi-hole from native to Docker container
- Port 80 already taken by beacon-dashboard — moved Pi-hole to 8080
- Pi-hole v6 uses internal port 8089, not 80

## 2026,09,21
- Fixed `BATABASE_URL` typo (should be `DATABASE_URL`)
- Added CORS middleware to beacon-api
- Wired beacon frontend to beacon-api — first real live data

## 2026,09,16 – 09,20
- No activity. Life got in the way.

## 2026,09,15
- Added Postgres + SQLAlchemy to beacon-api
- Full CRUD: GET, POST, DELETE on `/items`
- Auto-create tables on startup

## 2026,09,14
- Started beacon-api
- FastAPI scaffold with `/health` and `/` endpoints
- First working containerized Python app

## 2026,09,12 – 09,13
- Dockerized beacon (nginx:alpine)
- Set up GitHub Actions to build and push to Docker Hub
- First automated CI pipeline

## 2026,09,11
- Added add-item modal with favicon auto-fetch
- Items appear with site icons

## 2026,09,10
- Sidebar navigation with view switching
- Status indicators (colored dots)
- System health bar

## 2026,09,09
- Started beacon dashboard
- Static HTML/CSS/JS skeleton

## 2026,09,08
- Planned the Beacon Suite: beacon, beacon-api, beacon-cluster
- Attempted to mount a drive for Immich manually
- Mount issue broke the boot process; DietPi OS corrupted
- Flashed a fresh DietPi OS to the t520
- Drive itself was fine... kept it for later

## 2026,09,07
- Installed Pi-hole natively via `dietpi-software`
- Installed Immich self-hosted media backup bare metal as well via `dietpi-software`
- Configured as primary DNS for home network
- First critical service

## 2026,09,05
- Set up Node 1 (HP t520) with DietPi
- Static IP, SSH, base tooling
- First homelab machine online

## 2026,08,28
- Wiped Windows 8 from HP t520 thin client
- Wrote Linux Mint XFCE ISO to 32 GB USB using Rufus Portable
- Booted from USB and installed Mint over Windows 8
- Machine later becomes Node 1