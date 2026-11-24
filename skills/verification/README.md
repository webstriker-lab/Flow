# Verification Skills

Hard runtime proof and fast daemonized browser UI verification.

## User-invoked
- [`create-verification-skill`](./create-verification-skill/SKILL.md) — Generates a harness-neutral `.agents/skills/verify-<app>/` skill + feature map and proves it live once.
- [`maintain-verification-skill`](./maintain-verification-skill/SKILL.md) — Source wave + live pass to keep `verify-<app>` in sync with the app.

## Model-invoked
- [`verification-before-completion`](./verification-before-completion/SKILL.md) — Evidence-before-claims gate (unit tests + `verify-<app>` / `browser-verify`).
- [`browser-verify`](./browser-verify/SKILL.md) — Fast daemonized UI verification (`@playwright/cli` primary, `agent-browser` / built-in fallback).
- [`blast-radius`](./blast-radius/SKILL.md) — Proves downstream safety of risky changes by running real code.
