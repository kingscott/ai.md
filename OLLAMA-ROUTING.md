# Ollama cloud routing (opencode)

Applies only when opencode is the harness. Claude Code and Codex do not use this file.

Provider id is `ollama-cloud`. Models are named `ollama-cloud/<model>`; there is
no separate `ollama` provider in this setup, and naming a model `ollama/...`
fails at dispatch with `ProviderModelNotFoundError`. Use when an Ollama model is
driving the session, or when Claude and OpenAI are rate-limited.

| Model | Use for | Notes |
|---|---|---|
| glm-5.3 | Lead of a session; bulk or mechanical work with a clear spec: implementation, data analysis, migrations. Independent review perspective. Computer-use verification. | Strongest driver here. Effort `low`/`high`/`max`. |
| deepseek-v4.1-flash | Default for subagent or small, scoped pieces of work. | Very competent and very cheap. Effort `low`/`high`/`max`. Large output limit. |
| glm-5.3-flash | Subagent companion when glm-5.3 is leading. | Only use when explicitly called. Effort `low`/`high`/`max`. |

Also available but without a standing rule: kimi-k3, kimi-k2.7-code,
deepseek-v4-pro, minimax-m3, qwen3.5:397b, nemotron-3-ultra,
mistral-large-3:675b.

## Pin every subagent type you dispatch

The `task` tool takes only `subagent_type`; it has no model parameter. A
subagent's model therefore comes entirely from its agent definition, and the
built-in `general` and `explore` types pin nothing. Unpinned subagents inherit
the parent session's model, which silently makes them as expensive as the lead.

Pin them in `~/.config/opencode/opencode.jsonc`, where a pin wins over the
parent's model:

```jsonc
"agent": {
  "general": {
    "model": "ollama-cloud/deepseek-v4.1-flash",
    "description": "Default subagent for bounded implementation and scoped work."
  },
  "explore": {
    "model": "ollama-cloud/deepseek-v4.1-flash"
  }
}
```

Same applies to any other type without a `model:` line. `qrspi-*`,
`build-agent`, `verify-agent`, and `local-reviewer` already pin their own and
need no change.

Two consequences worth knowing:

- Editing an agent file does not affect a running session. Config is read once
  at startup, so restart the harness before expecting a new pin to take effect.
- `subagent_depth` defaults to `1`, so a subagent cannot dispatch further
  subagents. Plan a chain as a flat sequence of dispatches from the lead.

## Review

Use a different family from the one that wrote the code: `glm-5.3` reviewing
`deepseek-v4.1-flash` output, or the reverse.
