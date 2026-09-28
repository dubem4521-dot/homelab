# Entry 06: 2026,09,09 - Beacon starts, HTML CSS JS skeleton

**Duration:** ~4 hours
**Outcome:** Working HTML, CSS, JS skeleton. No Docker yet.

---

## Context

Fresh from planning. Time to actually build. The Beacon dashboard is the foundation the whole suite rests on.

Started with the simplest possible thing: a static HTML page that shows the structure I wanted.

---

## What I Tried

1. Created the repo: `beacon`
2. Wrote `index.html` with the basic layout
3. Added `styles.css` for dark theme styling
4. Wrote a placeholder `script.js` for future interactivity
5. Committed the skeleton

---

## What Broke

Nothing major, but a few small things:

### The dark theme came first

Before writing any content, I picked a background color (`#0a0a0f`), an accent color (teal), and a text color (`#e5e7eb`). This is backwards from most tutorials but correct for a dashboard. The colors set the mood. The content follows.

### Layout structure decisions

I went back and forth on the layout:

- Should the sidebar be fixed or scrollable?
- Should the panels stack on mobile or side scroll?

Ended up with:

```
+----------------------------+
| Top panel: logo and user   |
+-----------+----------------+
| Sidebar   | Main panels    |
|           |                |
+-----------+----------------+
```

That is the layout every dashboard uses. Grafana, Portainer, and others. Sticking with convention means no one has to learn my layout.

### The meta viewport typo

Wrote this:

```html
<meta name="viewport" content="width=, initial-scale=1.0">
```

The `width=` was empty. Should be `width=device-width`. Fixed in the next session but noted here as a lesson.

---

## Root Cause

N/A. This session was straightforward. The breaks were aesthetic choices, not bugs.

---

## What I Learned

- **A dashboard is a specific UI pattern.** Sidebar navigation plus a main content area. Every tool uses it for good reason: users already know how to use it.
- **Dark theme first.** The colors set expectations. Once the background is dark, everything else falls into place.
- **Meta viewport matters for mobile.** Without `width=device-width`, mobile browsers assume a default width and zoom out. The whole page becomes tiny.
- **Committing early gives you a checkpoint.** The first commit is always ugly. That is fine. It is the starting line, not the finish line.

---

## If This Happens Again

Starting a dashboard project:

1. Create the folder and run `git init`
2. Write a minimal `index.html` with three regions: top bar, sidebar, main area
3. Write `styles.css` with dark theme colors first
4. Add an empty `script.js` so you have somewhere for interactivity later
5. Commit
6. Then iterate on the actual content

**Meta viewport for all responsive pages:**

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

---

## What Came Next

Skeleton up. Time to make it functional. Sidebar navigation, view switching, status indicators.

See [Entry 07: Sidebar, view switching, status indicators](07,%202026,09,10%20Sidebar,%20view%20switching,%20status%20indicators.md).

---

## Commits

- `feat: initial beacon scaffold`
- `feat: add dark theme and layout structure`

---

## Links

- [Beacon repo](https://github.com/rustytoothpickk/beacon)