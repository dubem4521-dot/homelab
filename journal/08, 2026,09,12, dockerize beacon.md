# Entry 08: 2026,09,11 — Add item modal, favicon auto fetch

**Duration:** ~4 hours
**Outcome:** Reusable modal for adding items. Auto fetched favicons.

---

## Context

Dashboard had static content. The three panels, deployed, infrastructure, portfolio, were hardcoded.

Wanted users, well, me, to be able to add items. Click a plus button, get a modal, fill in a name and URL, and the item appears.

Also: items should show the site favicon automatically, like Chrome shortcuts do.

---

## What I Tried

1. Added plus buttons to each panel with a `data-list` attribute
2. Wrote a modal HTML block, hidden by default
3. Wired up the plus buttons to open the modal with the right list context
4. Wrote the save handler: read name and URL, add the item to the DOM
5. Added favicon fetching via Google's favicon service

---

## What Broke

### Favicon service quirks

Google runs a favicon service at:

```
https://www.google.com/s2/favicons?domain=example.com&sz=32
```

Give it a domain, get back a 32 by 32 icon. Simple.

But the URL parameter needed to be just the hostname, not the full URL. My first attempt passed the whole URL:

```
https://www.google.com/s2/favicons?domain=https://github.com&sz=32
```

That returned a generic globe icon. The service wants:

```
https://www.google.com/s2/favicons?domain=github.com&sz=32
```

Fixed with:

```js
const domain = new URL(fullUrl).hostname;
```

The `URL` API in JavaScript parses a URL and gives you `.hostname` as a separate field.

### The modal that would not close

First version of the modal worked but could only close via the cancel button. Clicking outside or pressing Escape did nothing.

Fixed by adding two handlers:

```js
// Close when clicking outside the modal
modal.addEventListener('click', (e) => {
  if (e.target === modal) closeModal();
});

// Close with Escape key
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape' && modal.classList.contains('active')) closeModal();
});
```

The `e.target === modal` check is important. It means the click happened on the overlay itself, not on any child of the modal box.

### Multiple plus buttons, one modal

At first, each plus button had its own modal. That was wasteful. Fixed by using `data-list` on each button:

```html
<button class="add-btn" data-list="deployed-list">+</button>
```

Then the click handler reads the attribute to know which list to add to:

```js
activeListId = btn.dataset.list;
```

One modal, three entry points. Clean.

---

## Root Cause

Not really a broke session. Mostly iterating on small UX issues.

The favicon thing was the main gotcha. The Google service wants a hostname, not a URL. Easy to get wrong.

---

## What I Learned

- **`new URL(input)` is the safest way to parse URLs in JS.** Handles `http://`, `https://`, missing schemes, everything. Returns an object with `.hostname`, `.pathname`, `.search`, etc.
- **One modal, multiple triggers** is the right pattern. Give each trigger a `data` attribute describing its context.
- **`e.target === modal`** distinguishes clicks on the overlay from clicks on the modal content. Essential for click outside to close.
- **Escape key handlers should check `modal.classList.contains('active')`** before doing anything, so the key press is ignored when the modal is not open.
- **Small UX details matter.** Being able to close a modal three different ways, button, outside click, Escape, is how real apps feel polished.

---

## If This Happens Again

Building a reusable modal:

1. Put the modal HTML once at the bottom of `<body>`
2. Add `data` attributes to triggers so the handler knows the context
3. On open: set context, populate fields, add the `active` class
4. On close: clear context, remove the `active` class
5. Handle three close paths: cancel button, outside click, Escape key
6. Test all three

For favicons:

```js
const url = new URL(userInput);
const favicon = `https://www.google.com/s2/favicons?domain=${url.hostname}&sz=32`;
```

---

## What Came Next

The dashboard was functional in the browser. But it was not deployed. Time to containerize.

See [Entry 09: Dockerize Beacon, GitHub Actions CI](09,%202026,09,12%20Dockerize%20Beacon,%20GitHub%20Actions%20CI.md).

---

## Commits

- `feat: add item modal with favicon auto fetch`
- `feat: extract item rendering to shared function`
- `fix: modal close on outside click and Escape`

---

## Links

- [Beacon repo](https://github.com/rustytoothpickk/beacon)
- [Google favicon service](https://www.google.com/s2/favicons)