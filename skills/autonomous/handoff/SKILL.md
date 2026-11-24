---
name: handoff
description: Optional manual checkpoint for unplanned or exploratory chats—writes a concise handoff note into .agent-state/<folder>/HANDOFF.md so any harness can pick up via /resume <folder>.
argument-hint: "<folder-name> [focus for next session]"
disable-model-invocation: true
---

# Handoff

Normal Flow pipeline work auto-checkpoints `.agent-state/<folder>/STATE.md` continuously so you do not need `/handoff` when hitting a rate limit. Use `/handoff` when you want to preserve rich nuance from an **exploratory or unplanned conversation** before switching harnesses or starting a fresh context.

## Steps

1. Choose `<folder>` (use the user's argument, the active track folder in `.agent-state/`, or a short kebab-case slug derived from the topic).
2. Ensure `.agent-state/` is gitignored, then write `.agent-state/<folder>/HANDOFF.md` containing:
   - **Goal & Current Status:** what we are solving and where we stopped.
   - **Key Decisions & Findings:** concise bullets with file/line or commit citations. Do NOT duplicate content already saved in specs, plans, ADRs, or diffs—point to them by path.
   - **Open Questions / Blockers:** anything still unsettled.
   - **Suggested Next Skill:** exact Flow skill to invoke next (e.g., `/grill-with-docs`, `systematic-debugging`, `/resume <folder>`).
3. Also update (or create) `.agent-state/<folder>/STATE.md` pointing to `HANDOFF.md` so `/resume <folder>` discovers it automatically in any harness.
4. Redact any secrets, API keys, tokens, or PII before writing.
5. Tell the user the exact command to run in the next harness: `/resume <folder>`.
