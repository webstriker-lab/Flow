---
name: grill-with-docs
description: "Gate 1 of Flow: a relentless design-tree interview that builds your domain model (GLOSSARY.md & ADRs), locks deep module seams, writes a spec, and hands off into the autonomous execution pipeline."
disable-model-invocation: true
---

# Grill With Docs (Gate 1)

This is **Gate 1** — the primary human alignment checkpoint before autonomous execution begins. Once the design tree is settled here, the agent executes end-to-end without mid-build interruptions until Gate 2 (final review before push/merge).

## Step 1: Load the Three Primitives

Invoke these three model-invoked skills right now:

1. `grilling` — governs the branching design-tree interview rounds.
2. `domain-modeling` — keeps `GLOSSARY.md` and `docs/adr/` sharp inline as decisions land.
3. `codebase-design` — ensures module seams, interfaces, and any major deepening trade-offs are settled *during* grilling instead of guessed later.

## Step 2: Grill Until the Frontier Is Empty

Walk the design tree in rounds per `grilling`:

- **Inspect the codebase yourself** for facts, existing patterns, `GLOSSARY.md`, `docs/adr/`, and current test/verification harnesses. Never ask the user what you can observe directly.
- **Resolve every decision the autonomous executor would otherwise stall on:**
  - Scope, non-goals, and concrete acceptance / finish criteria (including UI flows to verify with `verify-<app>` or `browser-verify`).
  - Domain terms (update `GLOSSARY.md` inline) and architectural decisions / module seams (record load-bearing decisions in `docs/adr/`).
  - Track slug `<track>` (short kebab-case name used for `.agent-state/<track>/STATE.md`, the spec, and the plan).
  - Execution preference if non-default (default: `subagent-driven-development` when subagents are available, otherwise `executing-plans`; or `/overnight` if the user plans to walk away).
- **Do not write production code** until the frontier is empty and the user confirms shared understanding.

## Step 3: Write the Spec & Initialize State (Gate 1 Exit)

Once the user confirms shared understanding:

1. Write a concise, unambiguous spec to `docs/flow/specs/YYYY-MM-DD-<track>.md` containing:
   - **Goal & Non-Goals**
   - **Domain Terms & ADRs Referenced**
   - **Module & Seam Design** (using `codebase-design` vocabulary)
   - **Global Constraints** (exact values, invariants, compatibility rules)
   - **Acceptance & Verification Criteria** (unit tests + live app/UI verification proof)
2. Ensure `.agent-state/` is gitignored, then create `.agent-state/<track>/STATE.md` with:
   - `Status: in_progress`
   - `Phase: planning`
   - `Base Branch: <current-branch>`
   - `Spec: docs/flow/specs/YYYY-MM-DD-<track>.md`
   - `Execution Mode: <chosen-or-default>`
   - `Next Skill: using-git-worktrees`
   - `Next Action: Create isolated worktree and run writing-plans from the spec.`
3. **Hand off immediately** (unless the user asked to pause or run `/overnight`):
   - Invoke `using-git-worktrees` to create an isolated workspace on a feature branch.
   - Then invoke `writing-plans` to generate the implementation plan and run autonomously through Gate 2 (`finishing-a-development-branch`).
