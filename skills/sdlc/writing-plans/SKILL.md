---
name: writing-plans
description: Use when you have a grilled spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write implementation plans for an engineer who has not seen this codebase or this spec. Assume they write idiomatic code once they know the exact interface, exact values from the spec, and exact tests. Document every decision they cannot infer: which files, which names and signatures, which values from the spec, and which tests prove each task. Bite-sized tasks. DRY. YAGNI. TDD. Deep modules (`codebase-design`). Frequent commits.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** Work in the isolated worktree created via `using-git-worktrees`.

**Save plans to:** `docs/flow/plans/YYYY-MM-DD-<track>.md` (unless the user configured a different location).

## Scope Check

If the spec covers multiple independent subsystems that should have been separate tracks, split into separate plans—one per subsystem—so each produces working, testable software on its own.

## Verify APIs Against Primary Sources (Anti-Hallucination)

Before pinning signatures, config flags, or imports for unfamiliar or fast-moving libraries/SDKs into the plan, verify them directly against **installed source/types** (`node_modules/`, `.venv/`, vendor dirs, lockfile versions) or current official documentation. Never write plan steps from memory for version-sensitive APIs.

## File Structure & Proactive Architecture (`codebase-design`)

Before defining tasks, apply `codebase-design` to map every file being created or modified:

- Design **deep modules** with clear seams and small interfaces—avoid shallow pass-through wrappers that fail the deletion test.
- Files that change together live together. Split by responsibility and domain concepts from `GLOSSARY.md`, not arbitrary technical layers.
- Respect existing ADRs in `docs/adr/`. If a touched file has grown unwieldy or shallow, fold the bounded deepening refactor directly into the owning task.

## Task Right-Sizing

A task is the smallest unit that carries its own RED-GREEN-REFACTOR test cycle and is worth a fresh reviewer's gate. Fold setup, config, and docs into the task whose deliverable needs them; split only where a reviewer could meaningfully reject one task while approving its neighbor.

## Step Granularity

Each step is one action with a checkable result:
- Write the failing test
- Run it to verify it fails
- Implement minimal, clean code to make it pass
- Run tests to verify pass
- Commit

## Plan Document Header

Every plan MUST start with this header:

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use `subagent-driven-development` (default when subagents are available) or `executing-plans` (inline fallback) to implement this plan task-by-task. Update `.agent-state/<track>/STATE.md` on every task transition.

**Track:** `<track>`
**Goal:** [One sentence describing what this builds]
**Architecture:** [2-3 sentences naming the deep modules and seams]
**Tech Stack:** [Key technologies/libraries]
**Spec:** [path to `docs/flow/specs/...md`]

## Global Constraints

[Verbatim project-wide requirements from the spec — versions, invariants, naming, platform rules, verification requirements.]

## Review Focus

[Up to five input classes or failure modes the spec implies that are most likely to bite a real user — one line each, most likely first. Pin each with a concrete test inside the task that owns the code.]

---
```

## Task Structure

````markdown
### Task N: [Component / Module Name]

**Files:**
- Create: `exact/path/to/file.ts`
- Modify: `exact/path/to/existing.ts:123-145`
- Test: `tests/exact/path/to/test.ts`

**Interfaces:**
- Consumes: [exact signatures from earlier tasks]
- Produces: [exact names, parameter types, and return types later tasks rely on]

- [ ] **Step 1: Write the failing test**
...
- [ ] **Step 2: Run test to verify it fails**
Run: `<exact test command>`
Expected: FAIL with `<specific failure>`
- [ ] **Step 3: Implement `<signature>` in `<path>`**
[Approach note when signature + test leave a choice; code block only for non-obvious algorithms or fixed copy.]
- [ ] **Step 4: Run test to verify it passes**
Run: `<exact test command>`
Expected: PASS
- [ ] **Step 5: Commit**
```bash
git add <files>
git commit -m "feat(<track>): <concise description>"
```
````

## What a Step Contains

A step is done when the implementer can write exactly one reasonable thing from it:
- **Test step:** test name and assertions with the spec's exact values.
- **Code step:** exact signature, file path, and pinned values; let the implementer write obvious bodies.
- **Verification step:** exact command and expected output.
- **Cross-task reference:** rely on the task's `Interfaces` block; do not duplicate earlier code.

No hand-wavy placeholders ("TBD", "handle edge cases", "write tests for the above").

## Self-Review

Before handing off, review the plan yourself against the spec:
1. **Spec coverage:** Every requirement in the spec maps to a concrete task.
2. **Step scan:** Every step is unambiguous and not bloated into a code transcript.
3. **Type & interface consistency:** Names and signatures match across tasks.
4. **Architecture & depth:** Modules are deep, seams are clean, and touched shallow code is deepened rather than layered over.
5. **Review Focus:** Every failure mode listed in Review Focus has an explicit test in its owning task.
6. **UI / App Verification:** If the feature touches user-facing UI/CLI/API behavior, include a final verification step referencing `verify-<app>` or `browser-verify`.

Fix any issues inline immediately.

## Update State & Autonomous Execution Handoff (Two-Gate Contract)

1. Update `.agent-state/<track>/STATE.md`:
   - Set `Phase: executing`
   - Record `Plan: docs/flow/plans/YYYY-MM-DD-<track>.md`
   - Populate the `## Tasks` checklist with every task (`- [ ] Task N: ...`)
   - Set `Next Skill: subagent-driven-development` (or `executing-plans` if inline was requested or no subagent tool exists).
2. **Proceed immediately into execution** — do NOT pause for another human approval round if Gate 1 (`/grill-with-docs`) already settled the spec and authorized execution.
   - Only pause for human plan review if the user invoked `writing-plans` standalone and explicitly asked to review the plan before coding, or if `/overnight` pre-flight is about to launch.
3. Invoke the executor skill:
   - **Default (subagents available):** `subagent-driven-development`
   - **Inline fallback (no subagent tool, or user chose inline):** `executing-plans`
