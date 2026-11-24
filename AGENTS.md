# Flow Bootstrap Snippet (`AGENTS.md`)

Copy the block below into any project's `AGENTS.md` (or `CLAUDE.md` / `GEMINI.md`), or run `/setup` once per repository to add it automatically.

```markdown
## Flow SDLC

- Load `using-flow` at session start. Flow is a **Two-Gate SDLC**:
  1. **Gate 1 (`/grill-with-docs`)**: Relentlessly align on scope, domain terms (`GLOSSARY.md`), ADRs (`docs/adr/`), and deep module seams (`codebase-design`) before touching code.
  2. **Autonomous Execution**: Once Gate 1 is confirmed, run `using-git-worktrees` -> `writing-plans` -> `subagent-driven-development` (or `executing-plans`) with TDD, proactive `codebase-design`, automated code review, `blast-radius` on risky changes, `verification-before-completion` (tests + `verify-<app>` / `browser-verify`), and `pr` without mid-build check-ins.
  3. **Gate 2 (`finishing-a-development-branch`)**: Pause before push/merge to present the verified diff, proof artifacts, PR draft, rulings made, and out-of-scope architecture notes. Run `/retro` after shipping.
- **Continuous cross-harness checkpointing**: Update `.agent-state/<track>/STATE.md` before starting each task (`[~]`) and immediately after committing it (`[x]`), so hitting a rate limit in any harness can be resumed immediately in another harness via `/resume <track>`.
- **Project conventions**: Domain docs live in `GLOSSARY.md` and `docs/adr/`. Specs and plans live in `docs/flow/`. Runtime state (`.agent-state/` and `.worktrees/`) is gitignored.
```
