---
name: using-flow
description: Use when starting any conversation - establishes the Flow two-gate SDLC, continuous cross-harness checkpointing, and mandatory skill invocation before any action
---

# Using Flow

<SUBAGENT-STOP>
If you were dispatched as a subagent to execute a specific task, ignore this skill.
</SUBAGENT-STOP>

<EXTREMELY-IMPORTANT>
If there is even a 1% chance a Flow skill applies to what you are doing, you MUST invoke that skill before responding, asking clarifying questions, exploring files, or writing code.

IF A SKILL APPLIES TO YOUR TASK, YOU DO NOT HAVE A CHOICE. YOU MUST USE IT.
</EXTREMELY-IMPORTANT>

## The Two-Gate SDLC

Flow enforces **two human touchpoints** and an **autonomous middle**:

1. **Gate 1 — Align (`/grill-with-docs`):** Before writing code for any feature or non-trivial change, grill the user relentlessly across the design tree, lock architectural seams (`codebase-design`), update `GLOSSARY.md` and `docs/adr/` (`domain-modeling`), write a spec, and initialize `.agent-state/<track>/STATE.md`.
2. **Autonomous Execution (no mid-build check-ins):** Once Gate 1 is confirmed, run end-to-end without pausing for human approval:
   - `using-git-worktrees` → `writing-plans` → `subagent-driven-development` (or `executing-plans`)
   - Active disciplines throughout: `test-driven-development`, proactive `codebase-design`, `systematic-debugging`, `requesting-code-review` / `receiving-code-review`, and `blast-radius` on risky changes
   - Proof gate: `verification-before-completion` (full test suite + project-local `verify-<app>` or `browser-verify` for UI)
   - PR draft: `pr`
3. **Gate 2 — Review before Push/Merge (`finishing-a-development-branch`):** Pause before any push, merge, or publish. Present the verified diff, evidence, PR draft, rulings made, and any out-of-scope architecture notes. After Gate 2, run `/retro` (and `/maintain-verification-skill` if the repo has a `verify-<app>` skill).

## Continuous Cross-Harness Checkpointing

Rate limits can interrupt any harness without warning. Maintain a write-ahead state ledger at `.agent-state/<track>/STATE.md` (where `<track>` is the short kebab-case project/feature slug) on **every phase and task transition**—never wait for the user to ask for a handoff:

```markdown
# Flow State — <track>

- **Status:** in_progress | awaiting_gate_2 | blocked | completed
- **Phase:** grilling | planning | executing | verifying | gate-2-review
- **Branch:** <branch-name>
- **Base Branch:** <base-branch>
- **Worktree:** <absolute-or-relative-path>
- **Spec:** <path-to-spec>
- **Plan:** <path-to-plan>
- **Execution Mode:** subagent-driven-development | executing-plans
- **Last Commit:** <short-sha> <commit-subject>
- **Next Skill:** <exact-skill-name>
- **Updated:** <YYYY-MM-DD HH:MM>

## Tasks
- [x] Task 1: <title> (<short-sha>)
- [~] Task 2: <title> (in progress)
- [ ] Task 3: <title>

## Rulings Made
- <decision> — <why> — <cost if wrong>

## Architecture Notes (Out-of-Scope)
- <deepening candidate deferred for /retro>

## Next Action
<One concrete sentence telling a fresh harness exactly what to do next.>
```

Update `STATE.md` **before** starting a task (`[~]`) and **immediately after** committing it (`[x]`). When a user switches harnesses after a rate limit and runs `/resume <track>`, this file + `git status/log` is how the new harness continues seamlessly.

## Skill Priority

Process skills come first—they set the workflow; discipline skills govern execution:

- **New feature / non-trivial change** → `/grill-with-docs` first, then the autonomous pipeline.
- **Bug / regression** → `systematic-debugging` first, then `test-driven-development` + `verification-before-completion`.
- **Resuming after rate limit / break** → `/resume [folder]`.
- **Unattended long run** → `/overnight` (requires a pre-grilled spec/plan).
- **Unsure which skill fits** → `/flow`.
- **Session complete** → `/retro`.

## Red Flags

These thoughts mean STOP — you are rationalizing:

| Thought | Reality |
|---------|---------|
| "This is simple, skip grilling" | Simple requirements hide misaligned assumptions. Check first. |
| "Let me explore the codebase before checking skills" | Skills govern how you explore. Invoke the skill first. |
| "I'll update STATE.md at the end" | Rate limits strike mid-turn. Write ahead on every transition. |
| "Should I ask the user if I can continue to Task 3?" | Gate 1 already authorized execution. Keep going until Gate 2. |
| "Tests passed earlier, good enough" | Run fresh verification (`verification-before-completion`) on HEAD. |

## Platform Adaptation

If your harness appears here, read its reference file for tool-mapping specifics:

- Antigravity: `references/antigravity-tools.md`
- Claude Code: `references/claude-code-tools.md`
- Codex: `references/codex-tools.md`
- Gemini CLI: `references/gemini-tools.md`
- Hermes Agent: `references/hermes-tools.md`
- Muse: `references/muse-tools.md`
- Pi: `references/pi-tools.md`

## User Instructions

Explicit user instructions (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, or direct prompts) take precedence over default skill rules. Only skip a Flow gate when the user explicitly tells you to.
