---
name: monitor-pr
description: Use when the user asks to monitor, watch, or babysit a PR.
---

Repos that we work in have various AI review bots, example: `cio-claude-assistant`, `Cursor bugbot`, `Copilot`, etc and many others. Even though their comments aren't always correct, 
they are helpful.

If the current harness has built-in PR monitoring functionality, use it; otherwise poll the PR for new comments and checks.

Only act on checks and comments newer than latest push. Check every bot finding against codebase before changing the code. Fix real issues and CI failures, and distinguish these from infrastructure flakes. You can dismiss false positives as long as you post a message with the reason.

Keep an eye on the `main` or `master` branch of the repo, and keep this branch up to date. Don't monitor every merge, but don't let the PR drift too much from the main branch.

If a review bot leaves feedback you don't think is worth addressing, reply and resolve the comment. Format comments left on Scott's behalf with the template:
```md
[MODEL-SLUG] Replying on behalf of Scott King
---

[reply]
```

Don't let review feedback to expand beyond the scope of the PR beyond the user's original goal. Address real issues, avoid scope creep.

If nothing has changed, don't leave a comment for the sake of it. Stop monitoring the PR when the latest commit has green CI and bot reviews. When the user requests it, merge the PR; otherwise let the user know it's ready for merge. Post-merge, mark the related issue or ticket as complete.