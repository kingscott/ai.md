---
name: file-pr
description: Create a concise pull request. Use when a user asks to file or create a PR.
---

# File PR

Before filing, check if there is an existing PR for this branch. Review the diff locally against the repo's main branch to ensure it matches the goal.

PR titles often come squashed commit messages; follow the repo's title conventions for the PR title. Look at recent PRs for examples. Bias towards a clear, human-readable title that explains why the change matters:

BAD
> fix: Enhance and optimize the overall user experience across webhook configuration and navigation menu components with improved styling and functionality

GOOD
> fix: Make webhook editor headers collapsible 

Start the description with a simple explanation of the problem based on the user's original prompt, followed by a brief explanation of the solution. Do not just list the tasks completed during implementation.

Commit messages follow the conventional-commit skill, not this one. Git history in a repo may use a different convention (e.g. `FLO-1234: summary`); that does not override conventional commit format when writing commits. Only the PR title mirrors the repo's existing PR conventions.

Here's some criteria for the PR description:
- Load template from `.github/PULL_REQUEST_TEMPLATE.md` when present. If missing, create a structure that matches repo conventions
- Keep tone factual, concise, and specific to real changes
- For net-new interfaces, use a clear before statement: `This functionality did not exist`
- If the template includes an image theme/prompt section and it is optional, remove that section. Never generate AI images or image-generation prompts.
- If the change should be tested with manual QA, include a section `## QA Instructions` that has a list of steps to verify the change
    - Always use markdown checkboxes, not bullets
- Assign the PR to user `kingscott`

Unless otherwise stated, open a "ready to review" PR so that the review bots and other checks run. 
