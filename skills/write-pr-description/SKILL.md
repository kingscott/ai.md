---
name: write-pr-description
description: Draft and refine GitHub pull request descriptions from branch diffs using the repository PR template. Use when asked to write or improve a PR body from current branch changes, including merge-base diff analysis, before/after section decisions, QA checklist-safe formatting, and image-theme prompt writing.
---

# Write PR Description

## Overview

Draft a PR description that is accurate to the code diff, aligned with the repository template, and ready to paste into GitHub.

## Workflow

1. Load `.github/PULL_REQUEST_TEMPLATE.md` when present. If missing, create a structure that matches common repo conventions.
2. Determine the target base branch (typically `master` or `main`) and diff from merge base, not from base HEAD alone.
3. Prioritize the actual code diff (equivalent to squash-merge output) over commit messages.
4. Fill sections with concrete behavior, user impact, and implementation notes that are directly supported by changed files.
5. Remove sections that are irrelevant rather than filling them with placeholders.

## Content Rules

- Keep tone factual, concise, and specific to real changes.
- Treat before/after as contextual sections, not mandatory UI screenshots.
- Include before/after when UI, UX, API contract, or another consumer-facing interface changed.
- For net-new interfaces, use a clear before statement such as: `This functionality did not exist.`
- If the change is backend-only (for example refactors, models, migrations, internal plumbing), omit before/after entirely.
- Never write `N/A` in before/after.
- If uncertain whether an interface changed, inspect touched files and prefer omission unless there is a clear interface delta.
- Never leave image theme text as `disabled` when the field exists.
- Write a witty or imaginative image-generation prompt tied to the change; if direct relevance is weak, use a fantasy metaphor.
- Prefer either realistic photography style or animation-studio ink style for image-theme prompts.
- Return prompt text only for image theme, never generate an image.

## Formatting Rules

- Avoid adjacent markdown lists across section boundaries when templates contain checklists.
- If a section before `## QA Instructions` ends in list items, insert a blank line, then one plain-text transition sentence, then the QA checklist.
- Ensure QA checklist items render as markdown checkboxes, not bullets.

## Output Rules

- Return the full PR body in exactly one fenced code block.
- Do not include commentary outside that code block.
- If nested code fences are needed, escape them correctly.
- Keep the output directly pasteable into GitHub.

## Quality Checklist

- Every important claim maps to real diff evidence.
- Template sections are complete or intentionally removed.
- Before/after is included only for concrete interface changes.
- Net-new interface changes use explicit before context (`This functionality did not exist.`).
- QA checklist renders correctly and is visually separated from prior lists.
- Final answer is one fenced code block only.
