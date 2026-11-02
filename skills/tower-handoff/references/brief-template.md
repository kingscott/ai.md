# Handoff: <TICKET> <one-line title>

You are in worktree `<absolute path>`, branch `<branch>` (off `<base ref> <short sha>`).
Read the repo's `AGENTS.md` first. Stay in this directory.

## Problem (already diagnosed, so do not re-investigate)

<What is wrong, from the user's side, in two or three sentences.>

<The cause, with file:line references.>

<The evidence: IDs, counts, timestamps, queries or logs that confirm it, so the
diagnosis can be checked without being redone.>

## Already done

<Uncommitted changes in this worktree, file by file, with what each change does.>

<What has been verified and what hasn't. Name anything not yet run: tests, review.>

## Steps

1. Create a goal covering steps 2 to 7.
2. Implement <the behavior to build: what changes, where, and the edge cases it must
   handle>. Out of scope: <what not to touch>. If "Already done" covers this, review
   that work and finish it rather than rewriting it.
3. <Add or extend tests that prove the behavior, with the cases listed. Then run the
   targeted tests, with exact commands. Redirect output to a log file and use a long
   timeout. Never run the whole suite or the linter unless the repo says to.>
4. <Any open design question, with the answer you expect and why.>:
6. Commit with the `conventional-commit` skill, push, and open a PR with the `file-pr`
   skill, linked to <TICKET>. The PR body states the evidence, the cause and the fix.
   No ticket numbers in code comments.
7. Monitor the PR with the `monitor-pr` skill until CI is green, or until any remaining
   failures are shown to be unrelated.

## Done when

- The PR is open and its URL is reported.
- The test commands above pass, with their summary lines shown.
- CI is green, or each remaining failure is explained as unrelated.
- Review feedback is addressed or answered.
