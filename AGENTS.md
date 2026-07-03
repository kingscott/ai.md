# AGENTS.md

## Commits
Include the following trailer on commit messages **only when the commit contains AI-generated code**:

Co-Authored-By: Codex <noreply@openai.com>

Do not include it for commits where you are only running git commands or summarizing human-written changes.

## Pull Requests
When creating a PR, use the second slash-delimited chunk of the branch name, followed by a
concise human-readable summary derived from the final branch segment.

Do not simply remove dashes or concatenate words. Convert the final branch segment into a
natural-language title.

When writing or updating a PR description, use the `write-pr-description` skill at
`/Users/kingscott/.codex/skills/write-pr-description/SKILL.md` when it is available.

Example:
- Branch: `JOB-133104/JOB-151524/update-nginx-quote-templates`
- PR title: `JOB-151524 update nginx for quote templates`
