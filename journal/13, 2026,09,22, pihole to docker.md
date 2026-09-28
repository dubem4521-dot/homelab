# Entry 13: 2026,09,21 - BATABASE URL typo, CORS, first live data

**Duration:** ~5 hours
**Outcome:** Frontend talking to backend. First real persistent data.

---

## Context

Back from the 5 day gap. beacon api worked locally. beacon frontend was deployed but used mock data.

Time to connect them. Replace `statusData` in the frontend with real `fetch` calls to the API.

Two problems stood in the way.

---

## What I Tried

1. Re read the frontend `script.js` to remember how data was structured
2. Updated `renderList` to accept items as an argument instead of reading from a global
3. Wrote `loadItems()` that fetches from the API and groups by category
4. Updated the add modal to POST to `/items` instead of appending to the DOM
5. Updated the delete handler to call `DELETE /items/{id}`
6. Added CORS middleware to the backend
7. Fixed the typo. Finally.

---

## What Broke

### The `BATABASE_URL` typo

This one hurt. Spent 20 minutes staring at:

```
connection to server at "127.0.0.1", port 5432 failed: Connection refused
```

The API container was trying to reach Postgres at `127.0.0.1` instead of `db`. But the compose file clearly said `DATABASE_URL: ...@db:5432/...`.

The problem: the env var was named `BATABASE_URL`. Not `DATABASE_URL`. B instead of D.

Because the name did not match, Python's `os.getenv("DATABASE_URL", default)` returned the default value, which was `localhost`. And `localhost` inside a container is the container itself, where there is no Postgres.

**How I found it:**

```bash
docker compose exec api env | grep -i database
```

Printed `BATABASE_URL=...`. There it was.

**Fix:** rename to `DATABASE_URL` in the compose file.

**Lesson:** env var names are silent. A typo does not raise an error. The app just falls back to a default, and you spend 20 minutes debugging the default.

### CORS blocked every frontend request

Even with the correct database URL, the browser refused to let the frontend fetch from the API:

```
Access to fetch at 'http://localhost:8000/items' from origin 'http://localhost:8080'
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header
is present on the requested resource.
```

**Explanation:** the browser's same origin policy blocks requests between different origins. Different port counts as a different origin. So `beacon` on port 8080 fetching from `beacon api` on port 8000 is a cross origin request.

**Fix:** add CORSMiddleware to the FastAPI app:

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

**Note on `allow_origins=["*"]`:** fine for development. In production, replace with the specific frontend origin.

### The `renderList` function signature mismatch

The frontend had a rendering bug where items would disappear after being added. Traced to a mismatch between how `loadItems` and `renderList` were called.

`loadItems` called `renderList('deployed-list', deployed)`. But `renderList` expected `renderList('deployed')` and read from a global.

Fixed by making `renderList` take both the list key and the items array:

```js
function renderList(listKey, items) {
  const el = document.getElementById(listElementIds[listKey]);
  // ...render items
}
```

Now every caller passes items explicitly. No hidden global dependency.

### The Tailscale firewall

Once the frontend worked locally, tried to access from a remote machine. Got:

```
Failed to connect to 100.x.x.x port 8000
```

Same pattern as the Pi-hole migration issue. Tailscale IPs were being dropped by iptables except for a specific allow listed address.

**Fix:**

```bash
sudo iptables -I INPUT 1 -s 100.64.0.0/10 -j ACCEPT
```

Then make it persistent:

```bash
sudo apt install iptables-persistent
sudo netfilter-persistent save
```

---

## Root Cause

Three separate issues, all common on first real deployment:

1. **Env var typo.** Silent failure. Always verify with `docker compose exec ... env`.
2. **CORS.** Required for any browser request between different origins.
3. **Render function signatures.** Silent mismatch. Items disappear without an error.

Plus the recurring Tailscale firewall issue. Same rule as before.

---

## What I Learned

- **Env var names must match exactly.** `BATABASE_URL` versus `DATABASE_URL` is a silent bug. Nothing tells you.
- **`docker compose exec <service> env`** is the fastest way to verify env vars in a running container.
- **CORS is required for cross origin browser requests.** Same origin means same protocol, hostname, and port. Any difference requires CORS.
- **`CORSMiddleware` in FastAPI is one `add_middleware` call.** Three lines. No complex config.
- **Silent function signature mismatches** are the most annoying class of JavaScript bug. There is no compiler to catch them.
- **Tailscale iptables rules block many ports by default.** Same fix as the Pi-hole migration.
- **Making functions take arguments rather than read globals** makes bugs visible. Every dependency is explicit.

---

## If This Happens Again

Debugging API connection issues:

1. **Is the API reachable from the host?**
   ```bash
   curl -I http://localhost:8000/docs
   ```
2. **Is the env var correct inside the container?**
   ```bash
   docker compose exec api env | grep -i database
   ```
3. **Can the API container reach the DB container?**
   ```bash
   docker compose exec api getent hosts db
   docker compose exec api nc -zv db 5432
   ```
4. **Is the DB healthy?**
   ```bash
   docker compose ps
   docker compose logs db --tail 30
   ```
5. **Is CORS configured?**
   Open DevTools, Network tab, look for the request. If it says CORS, add the middleware.

For Tailscale specifically:

```bash
sudo iptables -I INPUT 1 -s 100.64.0.0/10 -j ACCEPT
sudo netfilter-persistent save
```

---

## What Came Next

Everything worked. Frontend talked to backend. Data persisted across page reloads.

Next: migrate Pi-hole from native to Docker. Same pattern, new challenges.

See [Entry 14: Pi-hole to Docker migration](14,%202026,09,22%20Pi-hole%20to%20Docker%20migration.md).

---

## Commits

- `fix: rename BATABASE_URL to DATABASE_URL`
- `feat: add CORS middleware to beacon-api`
- `feat: wire beacon frontend to beacon-api`
- `fix: renderList accepts items as argument`

---

## Links

- [beacon repo](https://github.com/rustytoothpickk/beacon)
- [beacon api repo](https://github.com/rustytoothpickk/beacon-api)
- [FastAPI CORS docs](https://fastapi.tiangolo.com/tutorial/cors/)