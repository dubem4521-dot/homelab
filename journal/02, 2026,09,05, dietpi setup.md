# Entry 02: 2026,09,05 — DietPi setup, static IP, SSH

**Duration:** ~3 hours
**Outcome:** Node 1 online. SSH working. Static IP configured. Ready for services.
**Mood:** 🚀

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
```

**Breaking this down:**

| Piece | Meaning |
|---|---|
| `ip` | Modern network configuration tool |
| `link` | Show network interfaces, devices, not addresses |

Output showed `eth0` for Ethernet and `wlan0` for WiFi. Both present. Good.

Set the static IP on `eth0`.

---

## Root Cause

N/A. Clean session. DietPi does most of the heavy lifting.

---

## What I Learned

- **`dietpi-config` is the master control panel.** Network, display, timezone, SSH, everything is in there. Do not edit `/etc/network/interfaces` by hand unless you have to.
- **Static IP beats DHCP for servers.** Simpler to reason about. No more "where is the machine today?"
- **`ip link`** shows interfaces. **`ip addr`** shows addresses. **`ip route`** shows routing. Learn all three.
- **The t520's WiFi is optional.** Some units ship with it, some do not. If `wlan0` does not appear in `ip link`, the card is not installed.
- **DietPi's dashboard**, shown on SSH login, is genuinely useful. CPU temp, memory, disk usage, and key services at a glance.

---

## If This Happens Again

Setting up a fresh DietPi machine:

1. `dietpi-config` to set hostname, timezone, locale, static IP
2. Enable SSH: `dietpi-config` → 5 (SSH Server) → enable
3. From another machine: `ssh root@<ip>`. Default password is `dietpi`.
4. Immediately change the default password: `passwd`
5. Update the system: `apt update && apt upgrade -y`
6. Install tools: `apt install -y git curl htop nano`
7. Create your own user, recommended: `adduser <username>`

**Security note:** DietPi's default root password is `dietpi`. Change it on first login.

---

## What Came Next

With a working server, the next step was obvious: Pi-hole.

See [Entry 03: Pi-hole and Immich native install](03,%202026,09,07%20Pi-hole%20and%20Immich%20native%20install.md).

---

## Commits

N/A. System config, no code.

---

## Links

- [DietPi configuration docs](https://dietpi.com/docs/config/)