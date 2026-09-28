# Entry 00: 2026,08,28 - HP t520, Windows 8 to Linux Mint XFCE. First boot

**Duration:** ~3 hours
**Outcome:** t520 running Linux Mint XFCE. First boot was disappointing.

---

## Context

The HP t520 arrived running Windows 8. Useless for anything modern. I wanted Linux on it. Something light, familiar, and actually usable as a machine I could learn on.

I had a 32 GB USB drive that previously held Raspberry Pi OS.

---

## What I Tried

1. Checked the USB on a Windows machine
2. Downloaded Rufus Portable
3. Wrote the Linux Mint XFCE ISO to the USB
4. Booted the t520 from USB, installed Mint
5. Booted into Mint for the first time

---

## What Broke

### The USB partition panic

Plugged the 32 GB USB into Windows. Windows only showed a **500 MB `bootfs` partition**. Panicked briefly, thought 31 GB had vanished.

**Explanation:** Raspberry Pi OS creates multiple partitions. Windows can only read the small FAT `bootfs` partition. The rest is a Linux filesystem Windows does not understand.

**Lesson:** Windows not showing a partition does not mean the space is gone.

### Mint is sluggish on the t520

After a successful install, Mint XFCE booted. But it was **slow**. Really slow.

- Opening a file manager: 5 to 10 seconds
- Launching Firefox: 30+ seconds
- Any package operation: painful

The t520 is a thin client from around 2014. AMD GX-212JC, 4 GB RAM, 16 GB M.2 SATA SSD. It was never going to be fast, but this was worse than expected.

---

## Root Cause

Two issues:

1. **Mint XFCE is a desktop distribution.** It ships with a full desktop environment, window manager, file manager, display manager, and dozens of background services, all consuming RAM and CPU the t520 barely has.
2. **16 GB of storage is tight.** Between the OS, updates, and browser caches, disk space becomes a constant problem.

---

## What I Learned

- **Windows cannot read Linux partitions.** If a USB shows "500 MB" and nothing else, that is normal for a Linux formatted drive.
- **Rufus repartitions the whole USB.** The old Raspberry Pi layout is fully overwritten. No need to clean the drive manually first.
- **HP BIOS keys on the t520:**
  - `Esc` or `F10` for BIOS setup
  - `F9` for boot menu
- **Mint XFCE is not a light distro** in practice. It is lighter than GNOME or KDE, but it is still a full desktop OS. On a weak CPU with 4 GB RAM, it crawls.
- **A thin client is not a desktop.** I bought it thinking "small PC," but it was designed to run a remote desktop session and nothing else.

---

## Hardware Notes, For Future Reference

- **Storage:** M.2 **SATA** SSD, not NVMe. Upgradeable to 32, 64, or 128 GB.
- **RAM:** 1 SODIMM slot, supports up to **8 GB**.
- **WiFi:** optional. Check for antenna ports. If absent, use Ethernet or add a Mini PCIe card.
- **Ethernet:** gigabit RJ45 built in.
- **CPU:** AMD GX-212JC, dual core, around 1.2 GHz, very low power.
- **GPU:** Integrated Radeon.

---

## What Came Next

Mint was usable but not enjoyable. Started looking for something lighter and found DietPi the next day.

See [Entry 01: Switched to DietPi](01,%202026,08,29%20Switched%20to%20DietPi.%20First%20boot,%20snappy.md).

---

## Commits

N/A. No code yet.

---

## Links

- [Linux Mint](https://linuxmint.com)
- [Rufus](https://rufus.ie)
- [HP t520 spec sheet](https://support.hp.com)