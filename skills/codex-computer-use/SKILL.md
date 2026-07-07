---
name: codex-computer-use
description: Ask Codex CLI (gpt-5.5) to run local app verification that needs computer use, browser automation, simulators, screenshots, app launching, or independent runtime inspection. This is how gpt-5.5 is invoked for computer-use work. Use when the user asks Claude to test a flow, verify UI behavior, inspect a running app, capture screenshots, or report confirmation and feedback about implemented behavior that benefits from computer use functionality.
---

# Codex Computer Use

Use Codex as a separate local verification agent when the task needs real UI interaction, screenshots, simulator/browser/device state, or an independent runtime check outside Claude's current context.

Do not use this for ordinary code reading, typechecking, linting, or tests Claude can run directly. Launching apps, simulators, or browsers to verify the requested work is fine without asking; ask first only if the run could disrupt the user's environment beyond that (closing their apps, changing system settings, acting on real accounts or data).

## Workflow

1. Define what to verify: the flow to exercise, the expected behavior, and how to reach the app (URL, launch command, simulator target). Assume dev servers are already running; do not start them.
2. Create a temporary artifact directory for Codex's report and screenshots.
3. Run `codex exec` with full access, since computer use needs control outside the repo.
4. After Codex exits, read the report and view any screenshots yourself.
5. Relay what was verified, separating observed evidence from Codex's interpretation.

Use this command shape:

```bash
ARTIFACT_DIR="$(mktemp -d "${TMPDIR:-/tmp}/codex-computer-use.XXXXXX")"
REPORT="$ARTIFACT_DIR/report.md"
PROMPT="$ARTIFACT_DIR/prompt.md"

# Write a self-contained prompt to $PROMPT, then run:
codex exec \
  -C "$PWD" \
  --add-dir "$ARTIFACT_DIR" \
  -s danger-full-access \
  -o "$REPORT" \
  "$(cat "$PROMPT")"
```

`-s danger-full-access` is required here: app launching, browser automation, and simulator control operate outside the repo sandbox. Keep the prompt's allowed actions narrow to compensate.

Computer-use runs are slow and can exceed Bash's 10-minute timeout: pass an explicit timeout, or run in the background and poll for the report file.

## Prompt Requirements

Tell Codex:

- The exact flow to exercise and the expected behavior at each step.
- How to reach the app: URL, launch command, or simulator target.
- To save screenshots of each key state to the artifact directory.
- What it may interact with, and that everything else is off-limits (no closing user apps, changing system settings, or acting on real accounts or data).
- To report observed behavior, pass/fail against each expectation, screenshot paths, and anything it could not verify.

## Example Prompt

```text
You are verifying implemented behavior for Claude using computer use.

App: http://localhost:3000 (already running)
Artifact directory: /tmp/codex-computer-use.xxxxxx

Flow to verify:
- Open the command palette with Cmd+K.
- Press ArrowDown twice; the third item should be highlighted.
- Press Enter; the palette should close and the selected action should run.

Save a screenshot of each step to the artifact directory.

Constraints:
- Interact only with this app in the browser.
- Do not close other apps, change system settings, or touch real accounts or data.
- Do not edit any files in the repository.

Report:
- Observed behavior at each step, pass or fail
- Screenshot paths
- Anything you could not verify and why
```

## Reporting Back

View the screenshots yourself before relaying results; do not repeat Codex's pass/fail claims unverified. In the user-facing response, separate what the screenshots show from what Codex asserted.

If `codex` is not installed or the command fails, report the error and offer to verify the behavior directly instead.
