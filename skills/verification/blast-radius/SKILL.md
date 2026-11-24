---
name: blast-radius
description: "Find what a change could break somewhere else before it ships, beyond the diff, and prove the one fact it's safe because of by running real code instead of arguing from grep. Use automatically on risky cross-module, schema, or contract changes, or when asked 'blast radius of X' or 'what could this break'."
---

# Blast Radius

Find what a change breaks somewhere else before it ships. Use automatically during implementation/review on risky shared seams, schemas, wire formats, or contracts—or directly via `/blast-radius`.

Listing callers is not the job; an agent can grep those in a second. The job is finding the breakage grep will not show you.

## Don't Trust Your Own Writeup

A convincing writeup proves nothing. Find the one or two facts the change's safety depends on and **prove them by running real code**.

### Proof Ladder (Aim for Step 4 or 5)

For each safety-critical fact, climb as far down this ladder as is cheap, and state where you stopped:

1. You asserted it. *(Worthless on its own.)*
2. You pointed at a real `file:line` or library source.
3. You traced the failure path step-by-step and showed it cannot reach.
4. **You ran it:** a script or test that imports the real code and fails loudly if your assumption is wrong.
5. **You reproduced it in the running app** (via `verify-<app>` or `browser-verify`).

## Steps

1. **Read the full change:** the diff, added/changed/deleted symbols, and implicit behavior changes the diff does not spell out.
2. **Find the load-bearing safety fact:** most risky-looking changes are safe because of one core invariant (e.g., *"this cleanup only evicts already-expired entries"*). Spend your energy proving that invariant rather than listing 20 hypothetical maybes.
3. **Look where grep stops:** read the actual source of libraries called at the pinned version; check execution order (microtasks, teardown/unmount, hooks, migrations); check indirect consumers (JSON payloads, DB columns, wire formats, feature flags, code three hops downstream).
4. **Separate confirmed risks from cleared checks:** cite exact `file:line` for every risk and every cleared check. Never invent a caller or API.
5. **Prove the safety fact with runnable code:** run a targeted test or one-off script against the real code and capture its output.
6. **For wide or high-risk changes:** dispatch an independent reviewer subagent on a fresh context to challenge your safety fact and try to break it.

## Output Format

Keep it direct, concise, and grounded in real code citations:

- **What it does:** behavioral summary, including non-obvious effects.
- **Load-bearing safety fact:** stated clearly, with Proof Ladder level (1–5) and runnable output (or marked **unproven**).
- **Confirmed risks:** how each breaks, `file:line`, likelihood/severity, and proof.
- **Cleared checks:** what was inspected and why it is safe.
- **Before merging:** cheapest regression test or check that pins the safety fact (including any script run above).
