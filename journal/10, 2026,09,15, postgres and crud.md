# Entry 10: 2026,09,14 — beacon api scaffold, FastAPI hello world

**Duration:** ~3 hours
**Outcome:** Working FastAPI app with `/health` and `/` endpoints. Containerized.

---

## Context

Beacon frontend was containerized and deployed. But it had no real backend. All data was mocked in JavaScript.

The next project in the Beacon Suite is `beacon api`. A REST API plus database that will eventually serve the dashboard.

Started with the simplest possible thing: a FastAPI app that responds to two endpoints.

---

## What I Tried

1. Created the repo: `beacon api`
2. Chose Python plus FastAPI for the backend
3. Wrote `app/main.py` with a `/health` and `/` endpoint
4. Wrote a minimal `requirements.txt` with just FastAPI and Uvicorn
5. Wrote a Dockerfile based on `python:3.12-slim`
6. Built and ran locally, verified endpoints responded
7. Pushed to GitHub

---

## What Broke

Nothing on the first try. This was a clean scaffold.

A few small decisions that were not bugs but were worth noting:

### The Python version matters

Chose Python 3.12 because it is current and stable. The `python:3.12-slim` image is around 150 MB, much smaller than the full `python:3.12` image which is over 900 MB.

The `slim` variant omits build tools and documentation. Perfect for a runtime only container.

### The Dockerfile layering

Wrote the Dockerfile with layer order in mind:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Why the order matters:** Docker caches each layer. If `requirements.txt` has not changed, the `pip install` layer is cached and skipped on rebuild. Only the final `COPY app/` layer is re run. This makes iteration fast, from around 30 seconds down to 2 seconds.

If the order were flipped, `COPY app/ ./app/` first, then every code change would invalidate the pip install cache. Slow.

### The magic of `/docs`

FastAPI auto generates interactive API documentation at `/docs`. Open that URL and you get Swagger UI: every endpoint listed, click to test, request and response schemas.

This was a genuine surprise. Twenty lines of code, and there is a full testable API interface available.

---

## Root Cause

N/A. Clean session.

The main thing learned here was not about fixing bugs, but about architecture:

- FastAPI for a Python backend is the right choice. Type hints become validation. Return values become JSON automatically.
- Layer ordering in Dockerfiles is not cosmetic. It determines rebuild speed.
- Auto generated API docs come free and are worth showing in an interview.

---

## What I Learned

- **FastAPI is minimal.** A working app is under fifteen lines.
- **The decorator plus function pattern is the whole API.** `@app.get("/path")` plus a function returning a dict.
- **`/docs` is auto generated Swagger UI.** Huge for demos.
- **`python:3.12-slim` is the right base image** for a Python service. Not the full image, not alpine (which has musl issues with some Python packages).
- **Layer order in Dockerfile determines rebuild speed.** Put `requirements.txt` and `pip install` before `COPY app/`.
- **`--no-cache-dir` in pip install** saves around 100 MB by not caching wheels inside the image.

---

## If This Happens Again

Scaffolding a FastAPI service:

1. `mkdir beacon api && cd beacon api`
2. Create `app/main.py` with:
   ```python
   from fastapi import FastAPI
   app = FastAPI(title="beacon-api", version="0.1.0")

   @app.get("/health")
   def health():
       return {"status": "ok", "service": "beacon-api"}

   @app.get("/")
   def root():
       return {"message": "beacon-api is running", "docs": "/docs"}
   ```
3. Create `requirements.txt`:
   ```
   fastapi==0.115.0
   uvicorn[standard]==0.32.0
   ```
4. Create the Dockerfile as above.
5. `docker build -t user/beacon-api:latest .`
6. `docker run -d -p 8000:8000 --name beacon-api user/beacon-api:latest`
7. Open `http://localhost:8000/docs`

Verify with `curl -I http://localhost:8000/health`. Should return 200.

---

## What Came Next

Endpoints existed, but they returned hardcoded data. Time to add a real database.

See [Entry 11: Postgres, SQLAlchemy, CRUD](11,%202026,09,15%20Postgres,%20SQLAlchemy,%20CRUD.md).

---

## Commits

- `feat: scaffold beacon-api with FastAPI health endpoint`
- `feat: add Dockerfile for beacon-api`

---

## Links

- [beacon api repo](https://github.com/rustytoothpickk/beacon-api)
- [FastAPI docs](https://fastapi.tiangolo.com)