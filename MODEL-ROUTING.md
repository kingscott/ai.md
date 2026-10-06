# Model routing

Which model does which work, and when to hand work to a subagent. Loaded
globally from `~/code/ai.md/MODEL-ROUTING.md`. If a skill disagrees, this file
wins. Ollama/opencode routing lives in `OLLAMA-ROUTING.md`.

## Delegate or do it yourself

The lead keeps the approach, trade-offs, user-facing writing, and anything costly
to get wrong and hard to catch later. The lead reads every diff it accepts.

Delegate by default:
- Bounded implementation once the approach is decided.
- Wide searches and bulk reading. Subagents return conclusions, not raw output.
- Gathering with a known output shape (inventory, extraction, survey).
- Independent review passes.

Do trivial or tightly sequential edits yourself. If a subagent result misses the
bar, redo it with a stronger model without asking.

A `Routing hint` line from `hooks/route-hint.py` may appear in context. Treat it
as evidence; these rules decide.

## Claude (Agent `model` parameter)

- Sonnet 5: default. Unpinned subagents already use it (`CLAUDE_CODE_SUBAGENT_MODEL`).
- Opus 5.5: ambiguous, hard, or costly-to-get-wrong work; architecture, security, review.
- Haiku 4.5: single-fact lookups, the route-hint fallback judge in Claude Code (and pi on
  Anthropic) when Jev fails, and roles that pin it. Never for code or decisions.
- Fable 5.1: only when asked, or as goal-craft lead.
- Agents that pin a model in their definition keep that pin.

## OpenAI (Codex CLI)

- gpt-6-luna (CLI default): bounded implementation and scoped investigation. Also the
  route-hint fallback judge in Codex (and pi on OpenAI) when Jev fails.
- gpt-6.1-sol: complex implementation, code review, computer use.
- gpt-6-astra: only when asked.
- Use the codex-review and codex-computer-use skills. Otherwise run
  `codex exec -s read-only` (or `-s workspace-write` for edits) with a
  self-contained prompt, and pass `-m` when luna is not the right model.
- From a subagent: a Sonnet wrapper runs `codex exec`; prefix its description
  `gpt-6.1-sol:` or `gpt-6-luna:`.
- Codex can run past Bash's 10-minute limit: set a timeout or run it in the background.
- Parallel Codex implementers each get their own worktree.
- Codex does not commit, push, deploy, or edit global config.

## Review

- Plans and implementations: Opus 5.5, optionally gpt-6.1-sol as a second opinion.
- The reviewer must be a different model from the one that wrote the code.
- Check an outside reviewer's findings against the code before passing them on.
