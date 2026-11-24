---
name: codebase-design
description: Shared vocabulary and proactive discipline for designing deep modules. Use automatically during grilling, planning, implementation, and code review to spot shallow modules, place clean seams, deepen touched code, and keep the codebase testable and AI-navigable.
---

# Codebase Design

Design **deep modules**: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface. In Flow, this is a **proactive, model-invoked engineering discipline** wired directly into planning, execution, and code review—not a separate manual cleanup phase you wait on the human to schedule.

## Glossary

Use these terms exactly: don't substitute "component," "service," "API," or "boundary."

**Module**: anything with an interface and an implementation. Deliberately scale-agnostic: a function, class, package, or tier-spanning slice. _Avoid_: unit, component, service.

**Interface**: everything a caller must know to use the module correctly: the type signature, invariants, ordering constraints, error modes, required configuration, and performance characteristics. _Avoid_: API, signature (too narrow).

**Implementation**: what's inside a module, its body of code. Distinct from **Adapter**: a small adapter can hide a large implementation (a Postgres repo) or a large adapter a small implementation (an in-memory fake).

**Depth**: leverage at the interface. The amount of behaviour a caller (or test) can exercise per unit of interface they have to learn. **Deep** = large behaviour behind a small interface; **shallow** = interface nearly as complex as implementation.

**Seam** _(Michael Feathers)_: a place where you can alter behaviour without editing in that place; the *location* at which a module's interface lives. _Avoid_: boundary.

**Adapter**: a concrete thing that satisfies an interface at a seam. Describes *role*, not substance.

**Leverage**: what callers get from depth. One implementation pays back across N call sites and M tests.

**Locality**: what maintainers get from depth. Change, bugs, knowledge, and verification concentrate in one place rather than spreading across callers.

## Deep vs Shallow

```text
Deep module = small interface + deep implementation
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│  Deep Implementation│  ← Complex logic hidden inside
└─────────────────────┘

Shallow module = large interface + thin pass-through (avoid)
┌─────────────────────────────────┐
│       Large Interface           │  ← Many methods, leaky params
├─────────────────────────────────┤
│  Thin Implementation            │  ← Just passes through
└─────────────────────────────────┘
```

## Principles

- **Depth is a property of the interface, not the implementation.** Internal seams may decompose the implementation privately; external callers only see the small external seam.
- **The deletion test.** Imagine deleting the module. If complexity vanishes, it was a pass-through—collapse it. If complexity reappears across N callers, it earns its keep.
- **The interface is the test surface.** Callers and tests cross the same seam. If you must test *past* the interface, reshape the module.
- **One adapter means a hypothetical seam. Two adapters means a real one.** Do not invent abstractions for hypothetical second implementations.
- **Subtract before you add.** Remove dead wrappers, redundant validators, and unused pass-throughs in touched code before layering new logic on top.

## Proactive Deepening During Development

Apply this continuously across the SDLC without waiting to be asked:

### Friction Signals to Watch For
- Understanding one concept requires bouncing across many tiny modules.
- A module is **shallow**—its interface is nearly as complex as its implementation (fails the deletion test).
- Pure helpers were extracted solely for testability, breaking **locality** so bugs hide in the glue that calls them.
- Tightly coupled modules leak internal assumptions across their seams.
- Code is hard to test through its public interface.

### Where to Act (Respecting the Two-Gate Contract)
1. **During Gate 1 (`grill-with-docs`):** Surface major cross-cutting seam choices or ADR conflicts while the user is present. Never silently contradict an ADR in `docs/adr/`.
2. **During `writing-plans`:** Lock deep module boundaries and clean file ownership into the plan before coding starts. Use `GLOSSARY.md` terms for module names.
3. **During Execution (`subagent-driven-development` / `executing-plans`):** When modifying an area with obvious local shallowness (pass-through wrappers, duplicated logic in touched files, leaky helpers), **deepen it proactively within the task** and verify with tests. Keep refactors bounded to the files/seams your feature touches.
4. **During `requesting-code-review`:** Flag newly introduced shallow pass-throughs, leaky seams, or broken locality as **Important** findings.
5. **Out-of-scope structural debt:** When you spot a larger codebase deepening opportunity *outside* the current feature's scope, do **not** derail the task or interrupt the user mid-build—record it under `Architecture Notes (Out-of-Scope)` in `.agent-state/<track>/STATE.md` (files, friction, proposed deepened seam, leverage/locality gain) so it appears cleanly at Gate 2 and `/retro`.

### Brownfield Guardrails (Seam-Only & Chesterton's Fence)
In existing or legacy codebases, autonomous deepening must stay surgical:
- **Chesterton's Fence:** Before deleting, simplifying, or rewriting non-obvious existing code, guards, or workarounds, inspect `git log -S` / `git blame` to learn *why* that code exists. Never strip out a past incident fix by accident.
- **Seam-Only Deepening:** Respect prevailing repo conventions in untouched files. Deepen *only* the exact seam your task modifies, and pin existing untested legacy behavior with **characterization tests** (`test-driven-development`) before refactoring. Log wider legacy messes to `Architecture Notes` rather than turning a scoped task into a sprawling rewrite.

## Designing for Testability

1. **Accept dependencies, don't instantiate hidden ones.** Pass adapters at the seam.
2. **Return values instead of mutating hidden state.**
3. **Minimize surface area.** Fewer methods and simpler parameters mean stronger, cheaper tests.

## Going Deeper

- **Deepening a cluster given its dependencies:** see [DEEPENING.md](DEEPENING.md) (dependency categories, seam discipline, replace-don't-layer testing).
- **Exploring alternative interfaces on tricky seams:** see [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md) (parallel sub-agent interface designs compared on depth, locality, and seam placement).
