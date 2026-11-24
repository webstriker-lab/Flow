---
name: setup
description: One-time setup to initialize Flow in a repository (domain docs, .gitignore for .agent-state and .worktrees, AGENTS.md bootstrap pointer, @playwright/cli check, and optional /create-verification-skill).
disable-model-invocation: true
---

# Setup Flow

Run once per repository to wire up Flow cleanly across any coding harness.

## Steps

1. **Inspect existing repo layout** before changing anything:
   - Check for `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `GLOSSARY.md`, `docs/adr/`, `.gitignore`, and `.agents/skills/verify-*/`.
   - Check if `@playwright/cli` (`playwright-cli --version`) or `agent-browser` (`agent-browser --version`) is installed.

2. **Gitignore runtime state & worktrees:**
   - Ensure `.gitignore` includes:
     ```gitignore
     .agent-state/
     .worktrees/
     ```
   - Never overwrite existing `.gitignore` entries; append only what is missing.

3. **Initialize domain doc directories (lazily, without bloat):**
   - If `docs/adr/` does not exist, create `docs/adr/.gitkeep` (or note that `domain-modeling` creates `GLOSSARY.md` and `docs/adr/` on first use during `/grill-with-docs`).
   - Do not fabricate fake glossary terms.

4. **Wire `AGENTS.md` bootstrap pointer:**
   - If `AGENTS.md` does not exist (or lacks a Flow section), add a short, low-context-load section pointing any harness to Flow:
     ```markdown
     ## Flow SDLC

     - Follow `using-flow` at session start (Two-Gate SDLC: Gate 1 `/grill-with-docs` -> autonomous execution -> Gate 2 review before push/merge).
     - Maintain write-ahead state in `.agent-state/<track>/STATE.md` on every phase and task transition so `/resume <track>` works across any harness after a rate limit.
     - Domain docs live in `GLOSSARY.md` and `docs/adr/`. Specs/plans live in `docs/flow/`.
     ```
   - Keep `AGENTS.md` lean per `writing-for-agents` (pointers only, never essays).

5. **Check Fast Browser UI Driver (`browser-verify`):**
   - Report whether `playwright-cli` (preferred) or `agent-browser` is available.
   - If neither is installed and this repo has a web UI, show the one-line install command (`npm install -g @playwright/cli@latest`) and ask if the user wants to run it now.

6. **Offer Project Verification Skill (once):**
   - If `.agents/skills/verify-*/` does not exist and this repo has a runnable app/UI/CLI/API, offer once:
     *"Want me to generate a project-local verification skill (`.agents/skills/verify-<app>/`) with `/create-verification-skill` so agents can drive and prove the real app automatically?"*
   - On yes, invoke `/create-verification-skill`. On no, finish cleanly.

7. **Summary:** Report what was configured, what was already present, and remind the user they can start any feature with `/grill-with-docs` (or ask `/flow` if unsure).
