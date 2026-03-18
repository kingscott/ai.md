# CLAUDE.md

## Commits
Include the following trailer on commit messages **only when the commit contains AI-generated code**:

Co-Authored-By: Claude <noreply@anthropic.com>

Do not include it for commits where you are only running git commands or summarizing human-written changes.

## Branch naming

- Assuming there is a Jira ticket given in the particular workspace
- When creating a branch on the origin remote, use the format `<Jira epic number>/<Jira ticket number>/<short phrase with description>`
  - For example: `JOB-123435/JOB-142494/add-new-button-to-form`

## Pull Requests

- For Jira-backed work, format PR titles as `<Jira ticket> <short phrase with description>`.
- Do not use a colon in the PR title.
- Derive the PR title descriptor from the same subtask Jira summary used for the branch, but keep it human-readable instead of slugified.
- Keep the PR title descriptor to at most the first 6 words of the subtask summary.
- Capitalize the first letter of the first word in the description.
- Example PR title: `JOB-151530 Fix line item export formatting`

## Plans

- Save every new plan as a markdown file directly under `~/workspace/scott`.
- Use a descriptive filename that ends with `-plan.md`.
- When a Jira ticket is known, prefer filenames like `<jira-ticket>-<short-topic>-plan.md`.
- When continuing or revising an existing plan, update the existing plan file when practical instead of creating duplicates.
- When sharing a plan in chat, include the saved file path so it can be referenced in future chats.
