---
name: browser-verify
description: Fast, harness-agnostic browser UI verification using @playwright/cli (persistent daemon, compact accessibility refs, console/network checks, screenshots) with fallback to agent-browser or a built-in browser. Use whenever a change touches UI, frontend routes, forms, or visual layout.
---

# Browser Verify

Fast, reliable browser verification for UI changes—without heavy MCP bloat or flaky coordinate guessing.

## Driver Selection (Fastest First)

Pick the first available driver in this order:

1. **`@playwright/cli` (`playwright-cli`)** — **Preferred.** Runs a persistent browser daemon (no cold-start delay between actions) and returns compact accessibility-tree refs (`e1`, `e12`).
   - Check: `playwright-cli --version` (install if missing: `npm install -g @playwright/cli@latest`).
2. **`agent-browser`** (Vercel Labs) — Same daemonized CLI + ref-based interaction model.
   - Check: `agent-browser --version`.
3. **Existing repo E2E runner** — Targeted Playwright/Cypress spec (`npx playwright test <spec>`).
4. **Harness built-in browser tool** — Use only when no CLI browser driver is installed.

If the repo already has a project-local verification skill (`.agents/skills/verify-*/`), follow its Launch, Doctor, Drive, and Cleanup sections instead of improvising.

## Fast Verification Loop

### 1. Reuse or Launch the App (Never Blind-Drive)
- Check if the dev server is already running on the expected port (`curl -sf <url>` or health check). Reuse a warm server when safe; otherwise launch it in the background and wait for the ready log/health endpoint—never a blind `sleep`.
- Record any PID you started so cleanup kills **only** what you started.

### 2. Drive the Real User Path (`playwright-cli`)
```bash
# Open the target route (headless by default for speed; add --headed only if debugging)
playwright-cli open http://127.0.0.1:<port>/<route>

# Inspect compact accessibility snapshot to get element refs (e1, e5, ...)
playwright-cli snapshot

# Interact via refs — never guess raw pixel coordinates
playwright-cli fill e5 "sample input"
playwright-cli click e12

# Verify post-action state
playwright-cli snapshot
```

### 3. Assert All Three Signals
Never treat "the page loaded" as passing. Confirm all three:
1. **Observable UI state:** The expected DOM/accessibility state matches the feature's acceptance criteria (and side effects like saved records persist across reload when applicable).
2. **Zero unexpected console/page errors:** Run `playwright-cli console` (or inspect browser console output). Uncaught exceptions or React/hydration errors = FAIL.
3. **Zero unexpected failed network requests:** Run `playwright-cli network` (or check HTTP status codes). Unexpected 4xx/5xx responses = FAIL.

### 4. Capture Surviving Evidence
Save proof artifacts to `.agent-state/<track>/evidence/` (or `.agent-state/evidence/` if outside a track):
```bash
playwright-cli screenshot --filename .agent-state/<track>/evidence/<feature>-after.png
```
Confirm the screenshot file exists and is non-empty **after** cleanup.

### 5. Clean Teardown
- Close the browser session: `playwright-cli close`
- Stop only background server processes you launched in Step 1.
- Never delete `.agent-state/<track>/evidence/`.

## Reporting Contract

Hand back concise, verifiable proof:
- **Route & Flow Verified:** exact URL and user actions executed
- **Console / Network:** `0 uncaught errors, 0 unexpected 4xx/5xx` (or exact errors found)
- **Result:** PASS / FAIL with exact observable outcome
- **Evidence Path:** path to captured screenshot(s) or command output
