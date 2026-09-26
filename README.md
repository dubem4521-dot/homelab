# Homelab

A chronological log of my homelab: hardware, services, mistakes, and every debugging session.

Started as a way to keep notes. Became a portfolio. Turned into a habit.

---

## What This Is

Two HP t520 thin clients running a small but real infrastructure stack. It started as "let me try Pi-hole" and grew into Docker containers, a custom dashboard, a REST API, and eventually a Kubernetes cluster.

This repo documents **every step**, dated, in the order it happened. The good parts, the broken parts, and the parts where I spent three hours on a typo.

**Real timeline, real time.** Work is bursty — some days I ship a whole feature, some weeks I don't touch it. Both are documented.

---

## Current Services

| Service | Purpose | Access | Status |
|:--------|:--------|:-------|:-------|
| Pi-hole | Network-wide DNS + ad-blocking | `http://192.168.110.44:8080/admin` | Running |
| Beacon | Custom homelab dashboard | `http://192.168.110.44` | Running |
| Beacon API | REST API + Postgres for Beacon | `http://192.168.110.44:8000/docs` | Running |
| TJPork | Client web application | `http://192.168.110.44:5000` | Running |

**Remote access:** Tailscale (mesh VPN). All services reachable at `100.x.x.x` addresses from anywhere.

---

## Hardware

| Node | Model | Specs | Role |
|:-----|:------|:------|:-----|
| Node 1 | HP t520 | AMD GX-212JC, 4GB RAM, 15GB SSD | Control plane, primary services |
| Node 2 | HP t520 | AMD GX-212JC, 4GB RAM | Planned: k3s agent, secondary services |

**Storage reality:** Multiple 2.5" drives have failed in this project. Currently between drives for anything heavy (like photo management). The t520's internal bay is a lottery for old hardware.

See [docs/hardware.md](docs/hardware.md) for details.

---

## Repo Structure
