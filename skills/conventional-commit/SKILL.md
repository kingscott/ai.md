---
name: conventional-commit
description: Write or review Conventional Commit messages with clear type, scope, subject, and optional body/footer formatting. Use when creating commit messages, suggesting commit text, or checking commit message validity.
license: MIT
---

# Conventional Commit

Use this skill when the user wants help writing, revising, validating, or executing a commit message that should follow the Conventional Commits convention.

## Workflow

1. Identify the change type.
   - Prefer `feat`, `fix`, `refactor`, `docs`, `test`, `build`, `chore`, `ci`, `deps`, `perf`, `style`, or `revert`.
   - Pick the narrowest accurate type for the change.

2. Draft the header in this format:
   ```text
   <type>(<scope>): <subject>
   ```
   - Scope is optional.
   - Use imperative, present-tense wording.
   - Capitalize the first word of the subject.
   - Do not end the subject with a period.
   - Keep the subject to 70 characters or fewer.

3. Add an optional body when the change needs context.
   - Explain what changed and why.
   - Wrap lines to 100 characters or fewer.
   - Use plain text with real line breaks between sections.

4. Add an optional footer for breaking changes or issue references.
   - Use `BREAKING CHANGE:` for incompatible behavior.
   - Use references like `Closes #123` or `Fixes #456`.

5. Validate the final message before returning or using it.
   - Ensure the header is present.
   - Ensure the subject is specific and reviewable.
   - Ensure each section uses plain text only.
   - Ensure the message does not include literal escape sequences or HTML break tags such as `\n`, `\r`, `<br>`, or `<br/>`.
   - When multiple sections are needed, separate them with actual blank lines instead of encoded placeholders.

6. When executing `git commit`, preserve real paragraph breaks.
   - Prefer `git commit -F <file>` when writing a multi-line message.
   - If using `git commit -m`, use separate `-m` flags for header, body, and footer paragraphs.
   - Never build the commit message by embedding `\n`, `\r`, `<br>`, or `<br/>` inside the text passed to Git.

## Type Guide

- `feat` for a new feature
- `fix` for a bug fix
- `refactor` for internal code changes without behavior changes
- `docs` for documentation-only changes
- `test` for adding or correcting tests
- `build` for build system or dependency tooling changes
- `chore` for maintenance work outside product code
- `ci` for CI configuration or automation changes
- `deps` for dependency updates
- `perf` for performance improvements
- `style` for formatting-only changes
- `revert` for reverting an earlier commit

## Output Rules

- Return the commit message as plain text only.
- Use actual newlines if the message has a body or footer.
- Never include code fences around the final commit message unless the user explicitly asks for them.
- Never include encoded separators, markup, or transport artifacts inside the message text.
- Treat real blank lines as required formatting, not optional decoration.

## Examples

Feature:

```text
feat(auth): Add OAuth2 login support

Support Google OAuth2 sign-in for customer accounts.
This reduces password resets and speeds up onboarding.

Closes #234
```

Bug fix:

```text
fix(api): Handle null values in user profile

Avoid crashes when optional profile fields are missing.
Return safe defaults instead of raising an exception.
```

Breaking change:

```text
feat(api)!: Restructure response format

BREAKING CHANGE: API responses now use camelCase field names.
Update client applications to match the new contract.
```

### Commit trailer

Include the following trailer on commit messages **only when the commit contains AI-generated code**:

Co-Authored-By: Codex <noreply@openai.com> 

when using Codex models or 

Co-Authored-By: Claude <noreply@anthropic.com>

Do not include it for commits where you are only running git commands or summarizing human-written changes.

## Branch Naming Convention

Branch names should follow the pattern: `<type>/<short-description>`.

When a Jira ticket is known, prefer: `<ticket-number>/<subtask-number>/<short-description>`.

Examples:
- `JOB-133123/JOB-321456/add-new-button-to-form`
- `feat/add-user-auth`
- `fix/null-pointer-error`
- `docs/update-readme`
- `refactor/simplify-api`

## Core Principles

- **Single, stable change**: Each commit should represent one logical change.
- **Independently reviewable**: Commits should make sense on their own.
- **Working state**: The repository should remain in a working state after each commit.

## Red Flags

- Do not use past tense in the subject.
- Do not end the subject with a period.
- Do not exceed 70 characters in the subject.
- Do not exceed 100 characters on body or footer lines.
- Do not combine unrelated changes in one commit message.
- Do not include literal `\n`, literal `\r`, `<br>`, `<br/>`, or similar encoded line breaks.
- Do not pass a multi-line commit body to Git as a single shell string with embedded escape sequences.
