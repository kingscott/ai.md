---
name: codex-review
description: Ask Codex CLI (gpt-6.1-sol) for an independent code review of uncommitted changes, a branch diff, a commit, or a specific implementation. Use when the user asks Claude to have Codex or gpt-6.1-sol review work, when MODEL-ROUTING.md calls for a gpt-6.1-sol review perspective, or when Codex should audit a diff, find bugs or regressions, or compare Claude's implementation against requirements. For a review by Claude itself, use the normal review process instead.
---

# Codex Review

Use Codex as an independent reviewer when the user wants a second-pass review or when a change is broad enough that another agent's perspective is useful.

Prefer Claude's normal review process for small local checks. Do not delegate review just to avoid reading the code yourself. Treat Codex's output as evidence, not authority.

## Workflow

1. Identify the review target: uncommitted changes, base branch, commit SHA, PR checkout, or specific files.
2. Create a temporary artifact directory for the Codex report.
3. Run Codex with a focused review prompt.
4. Read Codex's report and verify important claims against the code before presenting them.

`codex review` does not accept custom instructions together with `--uncommitted`, `--base`, or
`--commit`. Use it only for a default review; for a focused review, use `codex exec` read-only
and name the target in the prompt.

```bash
ARTIFACT_DIR="$(mktemp -d "${TMPDIR:-/tmp}/codex-review.XXXXXX")"
REPORT="$ARTIFACT_DIR/report.md"
PROMPT="$ARTIFACT_DIR/prompt.md"

# Focused review. The prompt names the target, e.g. "Review the uncommitted changes to
# hooks/ (run git diff and git status)" or "Review git diff main...HEAD".
codex exec -m gpt-6.1-sol -s read-only -C "$PWD" -o "$REPORT" "$(cat "$PROMPT")" </dev/null

# Default review, no custom instructions (swap in --base main or --commit <sha>).
codex -C "$PWD" review -c model='"gpt-6.1-sol"' --uncommitted > "$REPORT" </dev/null
```

Close stdin (`</dev/null`) or Codex waits for more input. Reviews can exceed Bash's 10-minute
timeout: pass an explicit timeout, or run in the background.

## Review Prompt

Ask Codex to use a code-review stance:

```text
Review these changes for bugs, regressions, missing tests, security issues, and requirement mismatches.

Prioritize findings over summary. For each finding include:
- severity
- file and line reference
- concrete failure mode
- suggested fix direction

Do not edit files. If there are no substantive findings, say so and name any residual test gaps.
```

Add task-specific context when useful: requirements, risky areas, expected behavior, relevant tests, or files Claude is unsure about.

## Reporting Back

Before relaying a Codex finding, inspect the cited code or diff enough to decide whether the finding is real. In the user-facing response, separate confirmed issues from Codex suggestions you did not verify.

If Codex finds nothing, say that clearly and mention what review target it inspected.

If `codex` is not installed or the command fails, report the error and offer to review the changes directly instead.
