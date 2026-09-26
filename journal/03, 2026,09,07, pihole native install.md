# Entey `03, 2026,09,07 Pi-hole and Immich native install.md`

```markdown
# Entry 03: 2026,09,07 — Pi-hole and Immich native install

**Duration:** ~4 hours
**Outcome:** Pi-hole running. Immich running, temporarily.

---

## Context

The point of this whole project: kill ads at the DNS level with Pi-hole. And a bonus, self hosted photo backup with Immich.

Installed both via `dietpi-software`, DietPi's package manager for common server software.

**Note:** Both installed natively, not as Docker containers. This later turns out to be a mistake. See entries 14 and 16.

---

## What I Tried

1. `dietpi-software install 93` for Pi-hole
2. Configured Pi-hole as DNS on the home router
3. `dietpi-software install 215` for Immich
4. Verified both were running

---

## What Broke

### Immich's dependencies

Immich has its own dependency chain: PostgreSQL, Redis, Node.js, and a machine learning component. On a 4 GB t520, running all of these natively alongside Pi-hole caused immediate resource pressure.

The `dietpi-software` install took over **an hour**, much longer than the docs suggested. The install log warned about memory usage.

After install:

- Memory usage was near 100 percent at idle
- Pi-hole queries were starting to lag
- The system felt on the edge of instability

### Pi-hole capability warnings

Warnings appeared in the logs that I would see again later when migrating to Docker:

**Explanation:** Pi-hole, running as a non root user, wants to raise its own priority and set the system time. Both require capabilities the native install does not grant. Harmless for basic operation.

---

## Root Cause

**Bare metal Immich on a 4 GB machine is a bad idea.** Immich's docs recommend 6 GB minimum, 8 GB preferred. The t520 has 4 GB.

Running it natively, as opposed to in Docker, means it consumes resources directly on the host. No isolation, no limits. Pi-hole lives in the same memory pool. When Immich's ML container or Postgres starts using RAM, Pi-hole suffers.

---

## What I Learned

- **`dietpi-software` is convenient** but installs services natively on the host. Docker is safer for experimentation.
- **Immich is heavier than the docs imply.** A 4 GB machine will struggle with the full stack.
- **Pi-hole wants Linux capabilities it cannot have on a normal install.** Not a bug, just a permissions model thing.
- **Bare metal means shared fate.** If one service misbehaves, everything on the host is affected. Containers isolate this.
- **The t520 is not a powerful machine.** I keep re-learning this.

---

## If This Happens Again

Installing Pi-hole via `dietpi-software`:

1. `dietpi-software install 93`
2. Note the admin password shown at the end
3. Set router DNS to the DietPi machine IP
4. Test: `nslookup doubleclick.net <dietpi-ip>` should return `0.0.0.0`

**For Immich:** do not install bare metal on a 4 GB machine. Use Docker. Later entries explain why this is also a bad idea on this hardware, but at least it will not crash your Pi-hole.

---

## What Came Next

For reasons I now understand in hindsight, the system became unstable within a day. The next entry is the aftermath.

See [Entry 04: First drive failure](04,%202026,09,08%20First%20drive%20failure,%20OS%20corrupted,%20fresh%20flash.md).

---

## Commits

N/A. Package installs, no code.

---

## Links

- [Pi-hole docs](https://docs.pi-hole.net)
- [Immich requirements](https://immich.app/docs/install/requirements)
- [DietPi software list](https://dietpi.com/docs/software/)