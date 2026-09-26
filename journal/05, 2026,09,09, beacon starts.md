# Entry 05: 2026,09,08 — Roadmap shared, Beacon Suite planned

**Duration:** ~2 hours
**Outcome:** Full plan for the Beacon Suite. Roadmap doc written.

---

## Context

Fresh DietPi flashed earlier in the day. The Immich project was paused. The drive was set aside, and I was not ready to try again.

Wanted to start something new, something that did not need extra storage. Something that would actually grow skills.

---

## What I Tried

1. Sat down and thought about what would be worth building
2. Wrote out a roadmap document: "Max's DevOps Roadmap"
3. Defined three connected projects as the Beacon Suite
4. Time boxed it: 4 months to land a junior DevOps role or internship

---

## What Broke

Nothing to break. This was a planning session.

But the plan itself revealed something important. I had been collecting tools and services without a clear story. Pi-hole was useful, but "I run Pi-hole" is not a portfolio. I needed a **connected project** that demonstrated the full DevOps pipeline.

---

## Root Cause

N/A. Clean session. This was strategic thinking, not debugging.

---

## What I Learned

- **A portfolio needs a narrative, not a checklist.** A list of tools means nothing without a system that uses them together.
- **The plan chose itself once I looked at what I already had.** Docker experience from client work, a homelab with spare machines, an interest in infrastructure. The Beacon Suite is what those things want to become.
- **Time boxing forces prioritization.** Four months is short. It cuts the endless tinkering.
- **Writing the roadmap down made it real.** A plan in my head is a wish. A plan in a document is a commitment.

---

## If This Happens Again

When planning a portfolio project:

1. Start from what you already do, not what you wish you did
2. Identify the story the project will tell
3. Break it into phases that each produce something demonstrable
4. Time box the whole thing
5. Write it down. Version control it.
6. Revisit monthly. Adjust or recommit.

---

## The Plan, As Written

**Long term goal:** Land a junior DevOps role or internship within 4 months.

**Short term goal:** Ship three connected GitHub repos that together demonstrate the full DevOps pipeline.

| Repo | Purpose | What it proves |
|---|---|---|
| beacon | Homelab dashboard, HTML, CSS, JS | Can build and containerize a real frontend |
| beacon api | REST API plus database serving the dashboard | Can design and build a backend |
| beacon cluster | k3s cluster across both t520s, GitOps via ArgoCD | Can orchestrate multi node infrastructure |

**Escalating intensity:** Each project adds one new layer of complexity on top of what I already know. By the time I reach beacon cluster, the deployment pipeline is muscle memory.

**Why homelab based:** Free. The t520s are already owned hardware. Cloud free tiers carry risk of surprise charges.

**Why this order:** beacon first because it needs nothing else. beacon api second because it is independent but closes the loop. beacon cluster last because it deploys the first two.

---

## What Came Next

Plan written. Time to execute. Started with the frontend.

See [Entry 06: Beacon starts](06,%202026,09,09%20Beacon%20starts,%20HTML%20CSS%20JS%20skeleton.md).

---

## Commits

N/A. This was a document, later committed to the homelab repo.

---

## Links

- Roadmap doc, referenced conceptually