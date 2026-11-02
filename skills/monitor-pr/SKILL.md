---
name: monitor-pr
description: Use when the user asks to monitor, watch, or babysit a PR.
---

Repos that we work in have various AI review bots, example: `cio-claude-assistant`, `Cursor bugbot`, `Copilot`, etc and many others. Even though their comments aren't always correct, they are helpful.

If the current harness has built-in PR monitoring functionality, use it; otherwise poll the PR for new comments and checks.

Only act on checks and comments newer than latest push. Check every bot finding against codebase before changing the code. Fix real issues and CI failures, and distinguish these from infrastructure flakes. You can dismiss false positives as long as you post a message with the reason.

Screenshots and videos help as well, when you have a way to upload them.

Keep the PR at the size the user's goal needs. Review loops tend to grow a PR one cheap-looking fix at a time. Fix real bugs in the intended case. Decline the rest with a written reason, and list them as follow-ups: unneeded tests, guards for edge cases that do not matter, extra integration or compatibility, and small performance costs. Prefer the fix that deletes or narrows code.

## Reviews

If a review bot leaves feedback you don't think is worth addressing, reply and resolve the comment. Use the `leaving-pr-comments` skill for every comment posted on the user's behalf.

Don't let review feedback to expand beyond the scope of the PR beyond the user's original goal. Address real issues, avoid scope creep.

If nothing has changed, don't leave a comment for the sake of it. Stop monitoring the PR when the latest commit has green CI and bot reviews. When the user requests it, merge the PR; otherwise let the user know it's ready for merge. Post-merge, mark the related issue or ticket as complete.

## Base branch and merging
 
Keep an eye on the `main` or `master` branch of the repo, and keep this branch up to date. Don't monitor every merge, but don't let the PR drift too much from the main branch. 

Keep an eye on changes to main and rebase when needed. watch_pull_request reports conflicts only, so check for BEHIND yourself before you call the PR ready. If an overlapping PR makes this one obsolete, stop monitoring, report it to the user, and ask before closing the PR unless closure was explicitly authorized.

If nothing has changed, stay quiet rather than posting filler comments. Stop in T3 Code when the merge gate passes on the latest head. Merge only when the user explicitly requested it; otherwise report that the PR is ready. Right before a merge, run git fetch and check the PR again. Never merge when it is BEHIND or DIRTY, or when base commits that landed after the last green run touch this PR's files. Rebase, push, and wait for green again.

