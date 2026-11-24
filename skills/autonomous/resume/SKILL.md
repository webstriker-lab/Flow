---
name: resume
description: "Resume an interrupted Flow track in any harness after a rate limit or break. Reads .agent-state/<folder>/STATE.md, reconciles against live git history and the plan file, and continues immediately."
argument-hint: "[folder-name]"
disable-model-invocation: true
---

# Resume Interrupted Flow Track

Pick up a Flow track seamlessly after a rate limit, crash, or harness switch—even if the previous harness died mid-turn before any manual `/handoff` was invoked.

## Step 1: Resolve Target `<folder>`

1. **If the user passed `<folder>`** (e.g. `/resume auth-refactor` or `/resume billing-app`):
   - Look for `.agent-state/<folder>/STATE.md` (and optional `.agent-state/<folder>/HANDOFF.md` or `overnight-log.md`).
   - If `.agent-state/<folder>/` does not exist, search subdirectories of the workspace for `<folder>/.agent-state/` (supports multi-project workspaces where each folder is a separate project!).
2. **If no `<folder>` argument was passed:**
   - Scan `.agent-state/*/` (ignoring internal `.sdd/`).
   - **Exactly 1 track folder found:** select it automatically and announce which track you are resuming.
   - **Multiple track folders found:** display a concise table (folder name, status, phase, branch, last updated, next action) and ask the user which `<folder>` to resume.
   - **No `.agent-state/` folders found:** fall back to cold git recovery (`git status`, `git worktree list`, `git log -n 10`, and latest file in `docs/flow/plans/`) and report what in-flight work you detected before continuing.

## Step 2: Reconcile State Against Ground Truth (Trust Git Over Memory)

The previous harness may have hit a rate limit **mid-task** before updating `STATE.md`. Never blindly trust `STATE.md`—verify against the live repository:

1. Read `.agent-state/<folder>/STATE.md` (and `HANDOFF.md` / `overnight-log.md` if present).
2. Inspect the live worktree/repo named in `STATE.md`:
   - `git status --short` (check for half-written uncommitted edits!)
   - `git branch --show-current` and `git log -n 10 --oneline`
   - Read the active Spec (`docs/flow/specs/...`) and Plan (`docs/flow/plans/...`) and any SDD ledger under `.agent-state/.sdd/*/progress.md`.
3. Reconcile discrepancies:
   - **Task marked `[~]` in `STATE.md`, but its commit already landed in `git log` and tests pass:** promote it to `[x]` and advance to the next task.
   - **Task marked `[~]` with partial uncommitted files in `git status`:** inspect `git diff`, check which step of the task brief was in progress, run its test command, and finish the task cleanly (or discard broken half-edits if a test proves they are off-track).
   - **Branch mismatch:** switch to the recorded worktree/branch before touching files.

## Step 3: Update State & Continue Immediately

1. Write a one-line reconciliation note + updated timestamp to `.agent-state/<folder>/STATE.md`.
2. Print a tight 4-line pickup summary for the user:
   - **Track & Branch:** `<folder>` on `<branch>` (`<worktree>`)
   - **Completed:** `<N>/<Total>` tasks (last commit `<sha>`)
   - **Recovered state:** clean / reconciled `<detail>`
   - **Resuming with:** `<next-skill>` → `<exact next step>`
3. **Invoke `<next-skill>` immediately** (`writing-plans`, `subagent-driven-development`, `executing-plans`, `verification-before-completion`, `finishing-a-development-branch`, or `overnight` Phase 1) and continue autonomously to Gate 2. Do not re-ask Gate 1 questions that the spec already settled.
