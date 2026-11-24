---
name: flow
description: Route to the right Flow skill for your current situation. Use when unsure which skill to invoke next.
argument-hint: "What are you trying to do right now?"
disable-model-invocation: true
---

# Flow Router

Help the user pick the right Flow skill for their situation. Keep the answer short: name the exact `/command` (or pipeline sequence), explain in one sentence why it fits, and offer to start it immediately.

## Situation → Skill Map

| Situation | Skill | Notes |
|-----------|-------|-------|
| Starting a new feature, refactor, or design in a codebase | `/grill-with-docs` | Gate 1: grills every branch, updates `GLOSSARY.md`/ADRs, writes spec, then runs the autonomous pipeline to Gate 2. |
| Stress-testing a non-code idea, strategy, or doc | `/grill-me` | Relentless branching interview without touching code or repo docs. |
| Picking up work after a rate limit or switching harnesses | `/resume <folder>` | Reads `.agent-state/<folder>/STATE.md` + git + plan and continues from the exact step. |
| Capturing context from an unplanned exploratory chat | `/handoff` | Writes a manual checkpoint to `.agent-state/<folder>/HANDOFF.md`. |
| Running a pre-grilled plan unattended while away / sleeping | `/overnight` | 2-min pre-flight + live permission probe, then zero-question AFK execution + morning report. |
| Fixing a bug or performance regression | `systematic-debugging` | 4-phase root-cause loop → failing regression test → fix → `verification-before-completion`. |
| Checking what a risky/subtle diff might break elsewhere | `blast-radius` | Proves safety by running real code, not arguing from grep. |
| First time using Flow in a repository | `/setup` | Initializes `GLOSSARY.md`, `docs/adr/`, `.agent-state/` gitignore, checks `@playwright/cli`, offers `/create-verification-skill`. |
| Repo has no scripted way to drive & verify the real app | `/create-verification-skill` | Generates `.agents/skills/verify-<app>/` + feature map and proves it live once. |
| App changed and `verify-<app>` feature map drifted | `/maintain-verification-skill` | Source wave + live pass to keep `verify-<app>` honest. |
| Session finished — harden the repo & agent environment | `/retro` | Severity-ranked upgrades to checks, standards, navigation, and `verify-<app>` drift. |
| Learning a new technology or concept over multiple sessions | `/teach` | Stateful tutor workspace (`MISSION.md`, `RESOURCES.md`, spaced repetition). Run in a dedicated learning folder. |
| Writing or editing skills, `AGENTS.md`, or agent docs | `writing-for-agents` | Information hierarchy, context pointers, leading words, skill-testing checklist. |
| A Flow skill misfired, got skipped, or burned tokens | `/diagnosing-flow` | Audits session transcript with line-level evidence. |

## The Full Two-Gate SDLC

```text
Gate 1: /grill-with-docs
   ↓ (autonomous — zero mid-build check-ins; auto-checkpoints .agent-state/<track>/STATE.md)
using-git-worktrees → writing-plans → subagent-driven-development / executing-plans
  (with TDD + proactive codebase-design + automated review + blast-radius)
   ↓
verification-before-completion (tests + verify-<app> / browser-verify) → pr
   ↓
Gate 2: finishing-a-development-branch (human review before push/merge) → /retro
```
