# Approach

- When writing code, consider "yagni" principles and avoid scope creep.
- Read existing files before writing. Don't re-read unless changed.
- Thorough in reasoning, concise in output.
- Skip files over 100KB unless required.
- No sycophantic openers or closing thoughts.
- No emojis or em-dashes.
- Do not guess APIs, versions, flags, commit SHAs, or package names. Verify by reading code or docs before asserting.

# Personal preferences

## Writing style
- The language used for writing deliverables (PR descriptions, responses to PR comments, review comments, etc) should be simple and straightforward, easily understood by a human
- Avoid the use of bespoke acronyms, they don't make things easier to understand

## TypeScript

- Never use `any` unless there's not another typed solution or specifically instructed

## Commands

- Don't run dev server commands (ex: `pnpm run dev`) - assume it's running already. Exception: `cio-wt` commands are allowed, see "Local dev at Customer.io" below
- Don't run build commands unless specifically told to
- Focus on checking commands like linting and typecheck

## Code style

- Always strive for concise, simple solutions
- If a problem can be solved a simpler way, propose it

## General preferences

- If asked to do too much work at once, stop and state that clearly
- If computer use is helpful for completing or verifying work, shell out to gpt-5.6-sol with Codex for it
- If you're commenting on a pull request, use the following template:
```md
> [!NOTE]
> 🤖 [MODEL-SLUG] replying on behalf of Scott King


[reply]
```

## Branch prefix

When working is being completed in a branch or worktree branch, always use `kingscott/` as the branch prefix.


## Pull Requests

When writing or updating a PR description, use the `write-pr-description` skill at
`/Users/kingscott/.agents/skills/write-pr-description/SKILL.md` when it is available.

# Local dev at Customer.io (stack + cio-wt)

Two tools, and they compose: `stack` runs the backend (one local instance, shared), `cio-wt` runs
any number of `ui`/`hydra`/`parcel` worktrees in parallel and proxies backend paths through
stack's HAProxy on `localhost:3002`.

Agents MAY run `cio-wt` commands to verify frontend work, including starting dev servers. This
overrides the "don't run dev server commands" rule above, which still applies to bare
`pnpm run dev` / `npm run dev` / `stack dev`.

- Verify a worktree with `cio-wt up <name> --json`, then drive the headless Chrome the stack
  already runs (CDP on host port 9223).
- From that container the view is at `http://host.docker.internal:<browse_port>/`, using
  `browse_port` from `cio-wt up --json`. Never use the `*.cio.localhost` hostname: `.localhost`
  resolves to 127.0.0.1, which inside the container is the container itself.
- Leave the view running when done and report its localhost URL. I sweep periodically.
- Never run `stack dev <repo>` and `cio-wt up <repo>` for the same UI. `stack dev` fails HAProxy
  over to a fixed port (14201 for Journeys); cio-wt allocates its own. They disagree.

The local stack is shared by every cio-wt view, so agents must not mutate it:

- Allowed: `stack status`, `stack links`, `stack logs`, `stack ps`, `stack doctor local`.
- Not allowed: `stack dev`, `stack config`, `stack up`, `stack down`, `stack seed`, `stack clean`.
  Ask me. I run the backend myself when a backend change is in flight.

Remote stacks: when I ask for one for a worktree, use `cio-wt view share <view>`, which derives
`--build-path` from the view's bindings. Don't hand-write build paths. It omits `--no-use`, so it
changes the active stack context: run `stack env use local` afterward and tell me the new stack
name. `cio-wt view delete` tears the shared stack down too, unless `--leave-stack`.

`view share` is frontend-only (maps hydra/journeys/design-studio worktrees). Backend changes ship
as their own PR; use the `remote-stack:pr-services-<N>` guest label on the ui PR when both halves
need to be seen in one environment. Plain `remote-stack` on a PR creates `pr-<repo>-<N>`, and
`skip-design-studio` makes it boot faster.

# # Subagent routing

- Avoid using Fable
- Prefer Sonnet-5 for cost and speed whenever the task is well-specified and locally
  verifiable.
- Use Opus-5 for maximum ability or long-running reasoning, including ambiguous,
  technically difficult, tool-heavy, or costly-to-get-wrong work.
- For unsupervised repository changes, use Sonnet-5 when straightforward and Opus-5 when
  they require sustained reasoning or higher capability.
- For architectural, security, or adversarial reviews, use Opus.

Codex mechanics:

- Parallel Claude implementation agents must use isolated worktrees and sonnet-5
- Only use Opus-5 when complex orchestration is required