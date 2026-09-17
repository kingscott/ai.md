# Model routing

Single source of truth for which model does which work, and when work breaks out
to a subagent. Other config files (CLAUDE.md, AGENTS.md) point here rather than
restating these rules. If a skill or doc disagrees with this file, this file wins.

## When to break out to a subagent

Strong default: break out. Judgment allowed, but say why when you implement
directly instead.

The lead keeps:

- Planning and architectural judgment
- Decisions and trade-offs
- Review of anything a subagent produced
- User-facing synthesis (PR descriptions, replies, final reports)
- Anything where being wrong is expensive and hard to detect later

Break out by default:

- Implementation of bounded changes (code, prompts, docs) once the approach is
  decided. The lead specifies the change; a cheaper subagent writes it; the lead
  reads the diff.
- Bulk file reading and wide searches. The lead's context holds decisions, not
  transcripts. Subagents return conclusions, not raw output.
- Structured information gathering with a known output shape (inventory,
  extraction, survey against explicit questions)
- First drafts of anything mechanical
- Independent review passes where a second perspective is useful

Direct implementation by the lead is allowed for trivial edits (a few lines, no
design choices). When you implement directly anyway, state in one sentence why
you did not delegate.

If a subagent's output misses the bar, redo the work with a smarter model without
asking. Judge the output, not the price tag. Escalating costs less than shipping
mediocre work.

## Provider: Anthropic (Claude)

Runs via the agent/subagent `model` parameter.

| Model | Use for | Notes |
|---|---|---|
| fable-5 | Lead of long-horizon, ambiguous, multi-pillar goals (goal-craft runs). Outside goal runs, avoid for routine work: it is the most expensive and the goal-craft lead role is where it pays. | goal-craft's default lead. Do not use for ordinary sessions. |
| opus-5 | Maximum ability or long-running reasoning: ambiguous, technically difficult, tool-heavy, or costly-to-get-wrong work. Architectural, security, or adversarial reviews. Sustained-reasoning unsupervised repo changes. | Effort is the cost lever: `high` default, `xhigh` for demanding work. |
| sonnet-5 | Well-specified, locally verifiable work: bounded implementation, structured gathering, first drafts. The default for most subagent work. | Fast and cheap enough to use for information before spending a bigger model. |
| haiku-4.5 | Trivial single-fact lookups; pinned low-tier research roles (QRSPI tier agents, critic dispatches). | Approved only for these. Not for judgment or code. |

## Provider: OpenAI via Codex CLI

Claude model parameters cannot name these; use the Codex CLI. Skills
codex-implementation, codex-review, and codex-computer-use wrap the common flows.
For work they do not cover (investigation, data analysis), run
`codex exec -s read-only` directly with a self-contained prompt. Check
`~/.codex/config.toml` for the current CLI default before naming a model in a
prompt.

| Model | Use for | Notes |
|---|---|---|
| gpt-5.6-sol | Bulk or mechanical work with a clear spec: implementation, data analysis, migrations. Independent review perspective. Computer-use verification. | The near-free workhorse. Effectively the default for bounded implementation. |
| gpt-5.6-luna | Lighter OpenAI tasks when sol is unnecessary. | Current `~/.codex/config.toml` default (medium effort). |

Codex inside workflows and subagents (the `model` parameter only takes Claude
models, so wrap it):

- Spawn a thin Claude wrapper with `model: 'sonnet', effort: 'low'` whose prompt
  tells it to write a self-contained Codex prompt, run `codex exec` via Bash, and
  return the report.
- Label these wrappers with a `gpt-5.6-sol:` (or `-luna:`) prefix; the label is
  the only indication the real worker is a Codex model.
- Codex runs can exceed Bash's 10-minute timeout: pass an explicit timeout, or
  run in background and poll for the report file.
- Parallel Codex implementation agents must use isolated worktrees so their
  edits do not collide in a shared checkout.

## Provider: Ollama cloud

Runs via ollama. Models available: glm-5.3, kimi-k3. Use when a task benefits
from a different model family's perspective, or when Claude and OpenAI are
rate-limited. No standing routing rule: name the model explicitly when used, and
say why.

## Review policy

- Reviews of plans and implementations: opus-5 or fable-5. Optionally
  gpt-5.6-sol as one extra independent perspective (codex-review skill).
- Do not delegate review just to avoid reading the code yourself: the lead reads
  every diff it accepts.
- Treat any external reviewer's output as evidence, not authority: verify
  findings against the code before relaying them.

## What this file does not cover

- QRSPI phase agents pin their own models in frontmatter; those pins stand.
- goal-craft's lead-model choice happens at goal authoring time (Fable 5
  default, Opus 5 for well-bounded implementation goals).
- Cost is a tie-breaker only when axes conflict on shipping work:
  intelligence > taste > cost. Never let cost block the right model for a
  decision; use cheap models to gather information and draft, then spend the
  expensive model on judgment.