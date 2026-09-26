# Entry 01: 2026,08,29 — Switched to DietPi. First boot, snappy.

**Duration:** ~2 hours
**Outcome:** DietPi installed. First boot was dramatic, in the good way.

---

## Context

Linux Mint XFCE booted on the t520, but it was sluggish enough to be demoralizing. Opening a terminal took longer than it should. Firefox was practically unusable.

I started looking for a leaner OS. Something designed for exactly this hardware.

---

## What I Tried

1. Researched lightweight Linux distributions
2. Found **DietPi**
3. Downloaded the DietPi image
4. Flashed it to the same 32 GB USB
5. Booted the t520 from USB, installed DietPi to the internal SSD
6. Booted into DietPi for the first time

---

## What Broke

Nothing. This was the smoothest install yet.

DietPi's installer is minimal, straightforward, and made for exactly this kind of hardware:

- Small download
- Fast flash
- Boots to a menu driven first boot setup
- Asks about SSH, static IP, timezone, sensible defaults for a server

---

## Root Cause

N/A. Clean session.

The difference is architectural:

- **Mint XFCE** = full desktop OS, around 1.5 to 2 GB RAM at idle
- **DietPi** = minimal Debian base, LXDE optional, around 80 to 150 MB RAM at idle

That is roughly a 10x reduction in memory pressure. On a 4 GB machine, that is the difference between "usable" and "painful."

---

## What I Learned

- **DietPi is built for exactly this use case.** SBCs, thin clients, low power machines. It strips Debian down to a minimal base and adds a management layer (`dietpi-config`, `dietpi-software`) for installing services.
- **The boot experience is snappy.** Around 15 seconds from power to login prompt. Mint was 60+.
- **LXDE feels lighter than XFCE on this hardware.** Noticeably.
- **DietPi's `dietpi-software` menu is a goldmine.** One command installs Pi-hole, Docker, Node.js, and dozens of other services, preconfigured.
- **The right OS matters more than the right hardware.** The t520 is not fast, but with DietPi it is *fine*. Same machine, completely different experience.

---

## If This Happens Again

Installing DietPi on a t520:

1. Download the DietPi image from [dietpi.com](https://dietpi.com)
2. Flash to USB with Rufus, same process as Mint
3. Boot the t520, `F9` for boot menu, select USB
4. Install DietPi to internal SSD. The installer handles partitioning.
5. First boot drops into `dietpi-config`. Set:
   - Timezone
   - Locale
   - Static IP if you have a preferred address
   - SSH enable
6. Run `dietpi-software` to install services

**Note:** The installer might ask about SSH keys, hostname, and whether you want a desktop. For a server, skip the desktop.

---

## What Came Next

DietPi was up. Now to actually use it. A week later I set up static IP and SSH properly, then started installing services.

See [Entry 02: DietPi setup](02,%202026,09,05%20DietPi%20setup,%20static%20IP,%20SSH.md).

---

## Commits

N/A. No code.

---

## Links

- [DietPi](https://dietpi.com)
- [DietPi documentation](https://dietpi.com/docs/)
- [DietPi software list](https://dietpi.com/docs/software/)