# Entry 02: 2026,09,05 — DietPi setup, static IP, SSH

**Duration:** ~3 hours
**Outcome:** Node 1 online. SSH working. Static IP configured. Ready for services.


---

## Context

DietPi was installed but only barely configured. Time to make it a proper server: static IP, SSH, base tooling.

The t520 becomes **Node 1**, the primary homelab machine.

---

## What I Tried

1. Ran `dietpi-config` to set hostname, timezone, locale
2. Set a static IP
3. Enabled SSH
4. Installed base tools: `git`, `curl`, `htop`, `nano`
5. Verified SSH access from another machine on the network

---

## What Broke

Nothing major. DietPi's config tool is well designed.

One minor confusion: the t520 has both WiFi and Ethernet, but the WiFi card is optional and may or may not be installed. I checked with:

```bash
ip link