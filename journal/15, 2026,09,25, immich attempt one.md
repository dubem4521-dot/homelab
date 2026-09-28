# Entry 15: 2026,09,23 to 09,24 - RO boot loop, emergency mode, recovery

**Duration:** ~2 days
**Outcome:** Full recovery. Root cause identified. New entry added to fstab.

---

## Context

Pi-hole was migrated to Docker the day before. Everything worked. Then I rebooted.

The system dropped into emergency mode with a **read only filesystem**. Every service failed. SSH worked but nothing else did.

This is the same pattern as the OS corruption on 2026,09,08. Only this time, the OS itself was not corrupted. Just misconfigured.

---

## What I Tried

1. Interrupted boot, landed at emergency prompt
2. Logged in as root
3. Tried `mount -o remount,rw /`, worked but did not persist across reboot
4. Checked `dmesg` for I/O errors, none
5. Checked `systemctl --failed`, found `dietpi-ramlog`, `grub-common`, `containerd`, and `lightdm` failing
6. Checked `/etc/fstab`, found the problem
7. Fixed it, rebooted, confirmed clean

---

## What Broke

### The read only filesystem

On boot, the system entered emergency mode and remounted `/` as read only. Services that tried to write failed immediately:

- `dietpi-ramlog` failed because it could not write to `/var/lib/dietpi/logs/`
- `grub-common` failed because it could not write to `/boot/grub/`
- `containerd` failed because it could not create `/var/lib/containerd/`
- `lightdm` failed because it could not own its data directory

Nothing could start. Nothing could persist. Every boot was the same.

### The missing root entry in fstab

The actual cause. `/etc/fstab` had:

```
# You can use "dietpi-drive_manager" to setup mounts.
tmpfs /tmp tmpfs size=1674M,noatime,lazytime,nodev,nosuid,mode=1777
tmpfs /var/log tmpfs size=50M,noatime,lazytime,nodev,nosuid
```

That was it. No line for `/`.

Normally the kernel mounts `/` based on the bootloader's `root=` parameter, and systemd has a service (`systemd-remount-fs`) that reads `/etc/fstab` to know which filesystems to remount read write after fsck. If `/` is not in fstab, systemd does not know to remount it. It stays read only.

The dead drive entry had been removed earlier but the root entry was never there in the first place. Every reboot since install had been remounting `/` read only and nobody had noticed because nothing needed to write during a normal boot.

Once Pi-hole moved to Docker and containers needed to write, the failure became visible.

### The UUID vs PARTUUID confusion

While debugging, I found references to a PARTUUID that did not match the actual filesystem UUID:

```
PARTUUID=e7614451-8f6a-44ab-9d0c-c00882a7a5d0 /srv/share/docker/immich ext4 defaults,noatime 0 2
```

This was the dead drive from 2026,09,08. The PARTUUID had been copied from `blkid` output but from the wrong field. `blkid` shows both `UUID` for the filesystem and `PARTUUID` for the partition. They are different.

The system was trying to mount a partition that did not exist, waiting for it, and timing out. After 90 seconds, emergency mode.

---

## Root Cause

Two related issues:

1. **The root filesystem was missing from `/etc/fstab`.** Without it, systemd did not know to remount `/` read write after fsck. Every boot came up read only.

2. **A stale fstab entry referenced a drive that no longer existed.** Systemd waited 90 seconds for the device to appear, failed, and triggered emergency mode. Emergency mode defaults to read only, which then cascaded into every service failure.

The two compounded: emergency mode caused read only, read only caused service failures, service failures made the symptoms confusing.

---

## What I Learned

- **`/etc/fstab` should always contain a line for `/`.** Even though the kernel mounts root based on the bootloader config, systemd uses fstab to know it should remount read write.
- **fsck order matters.** Root gets `1`, other filesystems get `2`. Non filesystem entries get `0`.
- **Read only filesystem is a symptom, not a cause.** Something else triggered emergency mode. Find that first.
- **`systemctl --failed` shows what is actually broken.** Everything that could not write will be in this list.
- **`dmesg` tells you if it is hardware or software.** Ext4 errors mean hardware. Silence means configuration.
- **UUID and PARTUUID are different things.** `UUID` identifies the filesystem. `PARTUUID` identifies the partition. Use `UUID` in fstab. It survives reformatting better.
- **The marker file trick:** create `/srv/share/docker/immich/THIS_IS_ON_THE_BOOT_SSD` before mounting a drive there. If you see the marker after a reboot, the drive did not mount. The marker is a canary.
- **Persistent journals are worth enabling.** Without them, `journalctl -b -1` does not work and you cannot see the previous boot.

---

## The Fix

The corrected `/etc/fstab`:

```
# You can use "dietpi-drive_manager" to setup mounts.
UUID=4f91ece2-ee09-4e0a-982a-c44f5351171e / ext4 defaults,noatime 0 1
tmpfs /tmp tmpfs size=1674M,noatime,lazytime,nodev,nosuid,mode=1777
tmpfs /var/log tmpfs size=50M,noatime,lazytime,nodev,nosuid
```

The three changes:

1. **Added a line for `/`.** The UUID is the root filesystem's UUID, from `blkid /dev/sda1`. Fsck order `1`.
2. **Removed the dead drive's PARTUUID line.** No more waiting for a drive that does not exist.
3. **Kept the tmpfs mounts.** Those were fine.

After this change:

```bash
sudo systemctl daemon-reload
sudo mount -a
sudo reboot
```

On reboot, `/` came up read write immediately. No manual intervention. No emergency mode. `systemctl --failed` returned zero. `docker ps` showed every container running.

---

## If This Happens Again

Boot loop with read only filesystem:

1. **Get to a shell.** If emergency mode drops you at a prompt, log in as root. If not, try `Ctrl+D` at the boot screen to continue booting.
2. **Remount root read write.**
   ```bash
   mount -o remount,rw /
   ```
3. **Check what failed.**
   ```bash
   systemctl --failed
   ```
4. **Look at the kernel log.**
   ```bash
   sudo dmesg | grep -iE 'error|fail|I/O' | tail -30
   ```
   If there are ext4 or I/O errors, the drive is failing. See Entry 04 and Entry 16.
   If there is silence, the problem is configuration.
5. **Check fstab.**
   ```bash
   cat /etc/fstab
   ```
   Make sure root is listed with fsck order `1`.
6. **Search for stale references.**
   ```bash
   grep -r "<old-uuid>" /etc/ 2>/dev/null
   ```
7. **Fix fstab, reload systemd, reboot.**
   ```bash
   sudo systemctl daemon-reload
   sudo mount -a
   sudo reboot
   ```

**Prevent it from happening again:**

- Always have a root entry in fstab.
- Always use `UUID=` not `/dev/sdX`.
- Add `nofail` to any non essential mount so a missing drive does not block boot.
- Add `x-systemd.device-timeout=10s` to shorten the timeout for removable media.

---

## What Came Next

Everything recovered. Pi-hole running. beacon running. beacon api running. Clean boot.

Then I got the idea to try Immich again. One more time.

See [Entry 16: Immich attempt 2, Drive A fails](16,%202026,09,25%20Immich%20attempt%202,%20Drive%20A%20fails.md).

---

## Commits

- `fix: add root filesystem to fstab`
- `fix: remove dead drive PARTUUID from fstab`
- `docs: add reboot recovery runbook`

---

## Links

- [systemd remount fs](https://www.freedesktop.org/software/systemd/man/systemd-remount-fs.service.html)
- [ext4 journaling](https://www.kernel.org/doc/html/latest/filesystems/ext4/journal.html)