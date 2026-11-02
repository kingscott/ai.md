# Review: <TICKET> <one-line title>

You are a reviewer. You have none of the author's context. Work in `<absolute repo path>`
and stay in this directory. This is a read-only review: do not edit, write, commit,
push or run anything that changes state. Use `git diff`, `git log`, `git show`, file
reads and grep.

## Target

- Mode: <full | recheck>
- Diff: `git diff <base-sha>...<head-sha>` (base `<base ref> <sha>`, head `<branch> <sha>`)
- Files changed: <list, from `git diff --name-only`>
- PR: <url or none>

## Requirements

<Ticket text or the user's request, copied here. What the change must do, and what is
out of scope.>

## Risky areas

<Where the author is least sure, shared state touched, edge cases that matter.>

## Unverified

<Tests not run, behavior not checked in a running app.>

## Recheck only (delete for full mode)

Prior report: `<path>`. Do not do a fresh full review. For each prior finding, read the
new diff and report one line: resolved, partly resolved, or unresolved, with file:line
evidence. Report a new problem only if a fix introduced it.

## What to do

Review for bugs, regressions, missing tests, security issues and requirement mismatches.
Prioritize findings over summary.

Before you report a finding, open the cited code and confirm it is real. Drop findings
you cannot confirm. For each one that survives, give severity, file:line, the concrete
failure mode and a fix direction. If there are no substantive findings, say so and name
residual test gaps.

## Output

1. Write the report to `<report path>`. The path is outside the repo; the single
   exception to read-only is this one file.
2. Run: `tower send <lead session id> "Review done: <report path>"` and stop.
   One line. Do not paste findings.
