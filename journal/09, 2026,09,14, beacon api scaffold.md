# Entry 09: 2026,09,12 - Dockerize Beacon, GitHub Actions CI

**Duration:** ~2 days, evenings across the 12th and 13th
**Outcome:** Beacon running in a container. GitHub Actions builds and pushes to Docker Hub on every commit.

---

## Context

The dashboard worked in the browser when opened as a file. But that is not deployment.

Wanted to containerize Beacon, then automate the build with GitHub Actions.

**Two sessions combined here** because it was really one effort split across two evenings. Started the Dockerfile on the 12th, finished the pipeline on the 13th.

---

## What I Tried

### Session 1, evening of the 12th

1. Wrote a Dockerfile based on `nginx:alpine`
2. Copied HTML, CSS, JS into the nginx web root
3. Built and ran locally to verify
4. Committed the Dockerfile

### Session 2, evening of the 13th

1. Added `.dockerignore`
2. Wrote `.github/workflows/docker-publish.yml`
3. Configured Docker Hub secrets in GitHub
4. Pushed to trigger the first build
5. Verified the image landed on Docker Hub

---

## What Broke

### The Dockerfile copied files but changes were not showing

Classic mistake. I would edit `script.js`, rebuild, restart the container, and see the old version.

Cause: Docker copies files at build time. If you do not rebuild, you are running old code.

Diagnostic:

```bash
docker exec beacon cat /usr/share/nginx/html/script.js | grep API_BASE
```

If the output does not match your local file, the image is stale.

Fix: rebuild and restart.

```bash
docker rm -f beacon
docker build -t rustytoothpickk/beacon:latest .
docker run -d --name beacon -p 80:80 rustytoothpickk/beacon:latest
```

**Lesson:** Docker images are snapshots. Always rebuild after editing files.

### The `docker build` command missing a dot

First time running `docker build`, forgot the trailing `.`, the build context:

```
ERROR: docker: 'docker buildx build' requires 1 argument
```

The `.` means "use the current directory as the build context". Without it, Docker has no idea what to build.

Correct:

```bash
docker build -t rustytoothpickk/beacon:latest .
```

### GitHub Actions workflow did not push to Docker Hub

First version of the workflow built the image but never pushed it. Missing `push: true` in the build step.

```yaml
- uses: docker/build-push-action@v6
  with:
    context: .
    push: true     # this was missing
    tags: |
      rustytoothpickk/beacon:latest
      rustytoothpickk/beacon:${{ github.sha }}
```

### Docker Hub credentials as secrets

The workflow needs `DOCKER_USERNAME` and `DOCKER_PASSWORD` to log in. Added them under repo Settings, then Secrets and variables, then Actions.

Important: `DOCKER_PASSWORD` should be a Docker Hub **access token**, not the account password.

---

## Root Cause

Two different classes of issue:

1. **Docker layer confusion.** Files are baked in at build time. To change them, rebuild. Volume mounts avoid this in dev but should not be used in production.
2. **Workflow config details.** `push: true` is required. Env var typos are silent. Secrets must exist before the workflow runs.

---

## What I Learned

- **Docker images are immutable snapshots.** Everything the container needs must be inside the image or mounted in. There is no "the container sees my filesystem".
- **`docker exec <container> cat <file>`** is the fastest way to verify what the container actually has.
- **The trailing dot in `docker build` matters.** It is the build context.
- **`docker/build-push-action@v6` needs `push: true`** to actually push.
- **Tag twice: `latest` and `${{ github.sha }}`.** The first is for convenience, the second is a permanent record keyed to a specific commit.
- **GitHub Actions caches help a lot.** `cache-from: type=gha` and `cache-to: type=gha,mode=max` dramatically speed up subsequent builds.
- **Docker Hub access tokens are better than passwords.** Scoped, revocable, and can be regenerated without changing your account password.

---

## If This Happens Again

Dockerizing a static site:

1. Use `nginx:alpine` as base. 7 MB, standard.
2. `COPY . /usr/share/nginx/html/`
3. Add `.dockerignore` to exclude `.git`, `README.md`, etc.
4. `EXPOSE 80`
5. Build with `docker build -t name:tag .`. Do not forget the dot.
6. Run with `docker run -d -p 80:80 name:tag`

GitHub Actions workflow for Docker Hub:

```yaml
- uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_PASSWORD }}

- uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: |
      user/image:latest
      user/image:${{ github.sha }}
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

**Verify the image landed:**

```bash
docker pull <user>/<image>:latest
```

---

## What Came Next

Static, containerized dashboard. Now it needed a backend for real data.

See [Entry 10: beacon api scaffold](10,%202026,09,14%20beacon%20api%20scaffold,%20FastAPI%20hello%20world.md).

---

## Commits

- `feat: add Dockerfile for beacon`
- `ci: docker publish workflow`
- `fix: add push true to build step`

---

## Links

- [Beacon repo](https://github.com/rustytoothpickk/beacon)
- [Docker build push action](https://github.com/docker/build-push-action)