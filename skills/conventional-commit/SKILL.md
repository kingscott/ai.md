# Conventional Commit Messages

## Overview

This skill provides guidance on creating structured commit messages following the Conventional Commits specification. It helps maintain clear git history and ensures commits are meaningful and standardized.

## Prerequisites

Work on feature branches rather than main branches (e.g., `feat/add-user-auth`, `fix/null-pointer-error`).

## Format Structure

```
<type>(<scope>): <subject>

<body>

<footer>
```

- **Header**: Mandatory (type, optional scope, subject)
- **Body**: Optional (explanation of what and why)
- **Footer**: Optional (breaking changes, issue references)

**Important**: All lines must stay under 100 characters.

## Commit Types

- `feat` - A new feature
- `fix` - A bug fix
- `refactor` - Code change that neither fixes a bug nor adds a feature
- `docs` - Documentation only changes
- `test` - Adding missing tests or correcting existing tests
- `build` - Changes that affect the build system or external dependencies
- `chore` - Other changes that don't modify src or test files
- `ci` - Changes to CI configuration files and scripts
- `deps` - Dependency updates
- `perf` - Performance improvements
- `style` - Code style changes (formatting, missing semi-colons, etc.)
- `revert` - Reverts a previous commit

## Subject Line Rules

1. **Use imperative, present tense**: "Add feature" not "Added feature" or "Adds feature"
2. **Capitalize the first letter**: "Add" not "add"
3. **No period at the end**: "Add feature" not "Add feature."
4. **Maximum 70 characters**

## Body Guidelines

- Explain the **what** and **why**, not the how
- Use imperative, present tense (same as subject)
- Include motivation for the change
- Contrast with previous behavior when applicable
- Wrap lines at 100 characters

## Footer

- Use for breaking changes and issue references
- Breaking changes: Start with `BREAKING CHANGE:` followed by description
- Issue references: `Closes #123`, `Fixes #456`

## Breaking Changes

Indicate breaking changes in one of two ways:

1. Add `!` after type/scope: `feat(api)!: remove deprecated endpoints`
2. Add footer: `BREAKING CHANGE: deprecated endpoints have been removed`

Breaking changes correlate with major version updates in semantic versioning.

## Branch Naming Convention

Branch names should follow the pattern: `<type>/<short-description>`

Examples:
- `feat/add-user-auth`
- `fix/null-pointer-error`
- `docs/update-readme`
- `refactor/simplify-api`

## Core Principles

1. **Single, stable change**: Each commit should represent one logical change
2. **Independently reviewable**: Commits should make sense on their own
3. **Working state**: Repository should remain in a working state after each commit

## Examples

### Feature with scope
```
feat(auth): add OAuth2 login support

Implement OAuth2 authentication flow using Google provider.
This allows users to sign in with their Google accounts.

Closes #234
```

### Bug fix
```
fix(api): handle null values in user profile

Previous behavior would crash when optional fields were null.
Now gracefully handles missing data with default values.

Fixes #456
```

### Breaking change
```
feat(api)!: restructure response format

BREAKING CHANGE: API responses now use camelCase instead of snake_case
for all field names. Update client applications accordingly.
```

### Simple change
```
docs: update installation instructions
```

## Red Flags - Never Do These

- Use past tense in subject ("Added" instead of "Add")
- End subject with a period
- Exceed 70 characters in subject line
- Exceed 100 characters in body/footer lines
- Mix multiple unrelated changes in one commit
- Leave subject vague ("fix stuff", "update code")
- Skip the commit type

## License

MIT

## Version

1.0.0
