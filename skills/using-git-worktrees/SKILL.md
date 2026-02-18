---
name: using-git-worktrees
description: Workflow for creating and validating isolated git worktrees safely.
---

# Using Git Worktrees - Complete Skill Documentation

## Overview
This skill enables creation of isolated git worktrees with smart directory selection and safety verification. It's used when starting feature work that needs isolation from the current workspace or before executing implementation plans.

**Core principle:** "Systematic directory selection + safety verification = reliable isolation."

## Directory Selection Process

The skill follows a three-step priority:

### 1. Check Existing Directories
First, check for existing worktree directories in order:
- `.worktrees/` (preferred, hidden)
- `worktrees/` (alternative)

If found, use that directory. When both exist, `.worktrees` takes precedence.

### 2. Check CLAUDE.md
Search for worktree directory preferences in the project's CLAUDE.md file. If a preference is specified, use it without asking the user.

### 3. Ask User
If no directory exists and no CLAUDE.md preference is found, present two options:
- `.worktrees/` (project-local, hidden)
- `~/.config/superpowers/worktrees/<project-name>/` (global location)

## Safety Verification

### Project-Local Directories
**Critical requirement:** Verify the directory is ignored before creating worktrees using `git check-ignore`. If not ignored, apply Jesse's rule "Fix broken things immediately":
1. Add appropriate line to .gitignore
2. Commit the change
3. Proceed with worktree creation

This prevents accidentally committing worktree contents to the repository.

### Global Directory
No .gitignore verification needed since it's outside the project entirely.

## Creation Steps

### 1. Detect Project Name
Extract using: `basename "$(git rev-parse --show-toplevel)"`

### 2. Create Worktree
Determine full path based on location, then execute: `git worktree add "<path>" -b "<branch-name>"` and cd into it.

### 3. Run Project Setup
Auto-detect and run appropriate setup commands:
- Node.js: `npm install`
- Rust: `cargo build`
- Python: `pip install -r requirements.txt` or `poetry install`
- Go: `go mod download`

### 4. Verify Clean Baseline
Run project-appropriate tests to ensure a clean starting state. Report failures and ask for permission before proceeding if tests fail.

### 5. Report Location
Format: "Worktree ready at <full-path>\nTests passing\nReady to implement <feature>"

## Quick Reference Table

| Situation | Action |
|-----------|--------|
| `.worktrees/` exists | Use it (verify ignored) |
| `worktrees/` exists | Use it (verify ignored) |
| Both exist | Use `.worktrees/` |
| Neither exists | Check CLAUDE.md → Ask user |
| Directory not ignored | Add to .gitignore + commit |
| Tests fail during baseline | Report failures + ask |

## Common Mistakes to Avoid

**Skipping ignore verification:** Worktree contents can get tracked and pollute git status. Always use `git check-ignore` before creating project-local worktrees.

**Assuming directory location:** Creates inconsistency and violates project conventions. Always follow the priority: existing → CLAUDE.md → ask user.

**Proceeding with failing tests:** Cannot distinguish new bugs from pre-existing issues. Report failures and get explicit permission to proceed.

**Hardcoding setup commands:** Breaks on projects using different tools. Always auto-detect from project files.

## Example Workflow

```
User announces: "I'm using the using-git-worktrees skill"
[Check .worktrees/ - exists]
[Verify ignored via git check-ignore]
[Create: git worktree add .worktrees/auth -b feature/auth]
[Run npm install]
[Run npm test - 47 passing]

Output: Worktree ready at /path/.worktrees/auth
Tests passing (47 tests, 0 failures)
Ready to implement auth feature
```

## Red Flags - Never Do These

- Create worktrees without verifying ignore status (project-local)
- Skip baseline test verification
- Proceed with failing tests without asking
- Assume directory location when ambiguous
- Skip CLAUDE.md check

## Integration

**Called by:** brainstorming (Phase 4), subagent-driven-development, executing-plans

**Pairs with:** finishing-a-development-branch (for cleanup after work is complete)
