---
name: simplify-code
description: Review recent code changes for reuse, quality, and efficiency, then fix issues directly. Use when asked to simplify a diff, clean up recent edits, remove duplication, reduce hacky patterns, or improve performance in changed files.
---

# Simplify: Code Review and Cleanup

Review all changed files for reuse, quality, and efficiency. Fix issues directly.

## Phase 1: Identify Changes

1. Determine whether staged changes exist.
2. Run `git diff HEAD` when staged changes exist.
3. Run `git diff` when staged changes do not exist.
4. Review the most recently modified files mentioned by the user or edited earlier in the conversation when no git changes exist.

## Phase 2: Launch Three Review Agents in Parallel

1. Launch all three review subagents concurrently in a single message using the available delegation tool.
2. Pass the full diff to each subagent so each review has complete context.

### Agent 1: Code Reuse Review

1. Search for existing utilities and helpers that can replace newly written code, checking utility directories, shared modules, and files adjacent to the changed files.
2. Flag every new function that duplicates existing functionality, and recommend the existing function to use instead.
3. Flag inline logic that should use existing utilities; common candidates include hand-rolled string manipulation, manual path handling, custom environment checks, and ad-hoc type guards.

### Agent 2: Code Quality Review

1. Flag redundant state, including state that duplicates existing state, cached values that can be derived, and observers/effects that should be direct calls.
2. Flag parameter sprawl when new parameters are added instead of generalizing or restructuring the existing function shape.
3. Flag copy-paste variants where near-duplicate blocks should be unified behind a shared abstraction.
4. Flag leaky abstractions that expose internal implementation details or break existing abstraction boundaries.
5. Flag stringly-typed code where constants or existing string unions should be used.
6. Flag unnecessary JSX nesting, including wrapper `Box` or element layers that add no layout value when inner component props such as `flexShrink`, `alignItems`, or `justifyContent` already solve the layout need.
7. Flag unnecessary comments; remove comments that explain obvious WHAT behavior, narrate the change, or reference task/caller context, and keep only non-obvious WHY comments (for example hidden constraints, subtle invariants, or required workarounds).

### Agent 3: Efficiency Review

1. Flag unnecessary work, including redundant computations, repeated file reads, duplicate network/API calls, and N+1 patterns.
2. Flag missed concurrency when independent operations run sequentially.
3. Flag hot-path bloat when new blocking work is introduced into startup paths, per-request paths, or per-render paths.
4. Flag recurring no-op updates in polling loops, intervals, or event handlers that fire unconditionally; require change-detection guards so downstream consumers are not notified when nothing changed, and verify wrapper helpers that accept updater/reducer callbacks preserve the no-change signal (for example same-reference returns).
5. Flag unnecessary existence pre-checks (TOCTOU anti-pattern); operate directly and handle the error instead of checking before operating.
6. Flag memory risks, including unbounded data structures, missing cleanup, and event-listener leaks.
7. Flag overly broad operations, including reading whole files when only a portion is needed or loading all items when filtering for one.

## Phase 3: Fix Issues

1. Wait for all three subagents to complete.
2. Aggregate findings and fix each valid issue directly.
3. Note false positives or lower-value findings and skip them without arguing.
4. Summarize what was fixed, or confirm the code was already clean.
