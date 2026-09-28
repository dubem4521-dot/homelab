# Entry 16: 2026,09,25 - Immich attempt 2, Drive A fails

**Duration:** ~5 hours
**Outcome:** Drive A confirmed failing. Immich abandoned again for now.

---

## Context

The 500 GB drive from 2026,09,08 was set aside. I assumed at the time it was probably fine, that the OS corruption was a fluke from the bare metal install.

Came back to it on 2026,09,25. Time to try Immich again. This time with Docker instead of bare metal, and with a fresh perspective.

The plan: format the drive, set up Immich with the official Docker compose, point it at the drive, see what happens.

---

## What I Tried

1. Reconnected the 500 GB drive (Drive A)
2. Formatted it fresh with `wipefs`, `parted`, and `mkfs.ext4`
3. Mounted at `/srv/share/docker/immich`
4. Added a marker file: `THIS_IS_ON_THE_BOOT_SSD`
5. Downloaded the official Immich compose file
6. Configured `.env` with library and Postgres paths
7. Ran `docker compose up -d`
8. Watched `immich_postgres` crash loop

---

## What Broke

### Postgres could not initialize its data directory

Container logs:

```
find: '/var/lib/postgresql/data': Input/output error
chown: changing ownership of '/var/lib/postgresql/data': Input/output error
chmod: changing permissions of '/var/lib/postgresql/data': Input/output error
```

At first thought this was a permissions issue. Tried `chown -R 1000:1000`. Tried `chmod`. Still failed.

Realized the message was not permissions. **Input/output error** means the kernel cannot complete the operation. Not that permission was denied.

### Kernel log confirmed hardware failure

```bash
sudo dmesg | grep -iE 'error|fail|I/O'
```

Output:

```
critical medium error, dev sdb, sector 62916608 op 0x0:(READ)
critical medium error, dev sdb, sector 54545872 op 0x0:(READ)
EXT4-fs warning (device sdb1): htree_dirblock_to_tree: inode #22544385: error -5
```

**Translation:**

- `critical medium error` means the physical disk is returning read errors
- `error -5` is `EIO`. The kernel cannot read the sector.
- The same inode fails repeatedly, meaning the same physical sector is bad

### `touch` worked but `dd` failed

Tried a simple write test:

```bash
sudo touch /srv/share/docker/immich/test.txt
```

That worked. Then:

```bash
sudo dd if=/dev/zero of=/srv/share/docker/immich/test.bin bs=1M count=100
```

That failed with I/O errors. The touch succeeded because it only modified cached directory metadata. The `dd` failed because it actually had to write to the physical platter.

**Lesson:** `touch` succeeding does not mean the drive is fine. It means the inode table is cached.

---

## Root Cause

**Drive A was dying. It had always been dying.**

The 2026,09,08 failure was not a fluke. It was the first symptom of a drive on its way out. The bare metal Immich install that time made it worse by pointing the entire OS at the failing disk. This time, running Immich in Docker at least isolated the damage to Immich's containers. But the drive was still going to fail.

What I did wrong in between: I assumed the drive was fine because I wanted it to be fine. I did not run `smartctl`. I did not do a write test. I did not check `dmesg` after the first failure. I just moved on and hoped.

**What I should have done on 2026,09,08:** marked the drive as suspect. Run diagnostics. If bad, replace it. Never trust it again.

---

## What I Learned

- **`Input/output error` is a hardware failure, not a permission issue.** The distinction is subtle but critical. "Permission denied" is `EACCES`. "Input/output error" is `EIO`. Different errno, different cause.
- **`touch` succeeding does not mean the drive is fine.** It only modifies cached metadata. Force a real write with `dd` to be sure.
- **Old drives do not heal.** A failing drive will get worse, not better. Every additional write cycle damages it further.
- **Run `smartctl` before trusting any drive.** Five seconds of diagnostics saves hours of debugging.
- **`dmesg | grep -iE 'I/O|error'`** is the first command to run when anything storage related misbehaves.
- **Trust your past self.** If a drive caused an OS corruption once, it will do it again.
- **Docker isolation helped but was not enough.** The container could not write to the failing drive, but at least the failure did not corrupt the host this time.

---

## If This Happens Again

Suspect drive diagnostics:

1. **Check the kernel log:**
   ```bash
   sudo dmesg | grep -iE 'error|fail|I/O' | tail -30
   ```
2. **Run SMART diagnostics:**
   ```bash
   sudo smartctl -a /dev/sdX
   ```
   Look for `Reallocated_Sector_Ct`, `Current_Pending_Sector`, `Offline_Uncorrectable`. Any of these greater than zero means the drive is failing.
3. **Force a real write test:**
   ```bash
   sudo dd if=/dev/zero of=/path/to/test.bin bs=1M count=100
   ```
   If this fails, the drive cannot be trusted.
4. **Stop using the drive immediately.**
   ```bash
   sudo umount /path/to/mount
   ```
5. **Remove from fstab** so the next boot does not hang waiting for it.
6. **Replace the drive.** Do not try to repair, reformat, or work around it.

**The rule:** a drive with `Input/output` errors is dead. Not "mostly dead." Dead. Stop trusting it.

---

## What Came Next

Replaced Drive A with a different drive. Drive B. Confident that this time it would work.

It did not work.

See [Entry 17: Immich attempt 3, Drive B fails, docs repo](17,%202026,09,26%20Immich%20attempt%203,%20Drive%20B%20fails,%20docs%20repo.md).

---

## Commits

- `fix: remove dead drive from fstab`
- `docs: add drive failure notes to runbook`

---

## Links

- [smartctl docs](https://www.smartmontools.org/wiki/TocDoc)
- [EIO errno](https://man7.org/linux/man-pages/man3/errno.3.html)