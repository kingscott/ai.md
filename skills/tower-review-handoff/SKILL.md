---
name: tower-review-handoff
description: Hand a code review to a fresh read-only reviewer terminal in the current Tower space, with a self-contained brief, instead of reviewing on this thread. Use when a review is warranted (the user asks, or consequence is 2+ or shared state is touched at PR seal time), or to re-check prior findings after fixes.
---

# Tower review handoff

Run the review in a separate terminal so this thread does not pay for the reviewer's
reading, and does not pay for verifying its claims. The reviewer verifies its own
findings and writes one report file. This session reads that file and applies fixes.

The reviewer starts with none of this conversation. The brief is all it gets.

Stop at the first failed step and tell the user which one failed. Do not fall back to
running the review on this thread.

## When to use it

- The user asks for a review: always.
- At PR seal time: once per PR, when consequence is 2 or higher or shared state is
  touched (shared branches, deploy config, global agent config, schemas, anything pushed
  or deployed without a further review gate).
- Below that, do not review. The lead's own read of the diff covers it. "Put up a draft
  PR" and "tackle 1-3 as commits" do not imply a review.
- After fixes, use a re-check (below). A second full review needs the user's say-so.

If the user did not ask and the threshold is met, propose the handoff in one line and
wait. Do not start it unasked.

## Inputs

- **repo**: absolute path of the checkout holding the branch. The reviewer reads it in
  place, read-only. No worktree.
- **target**: PR URL, or `base...head` SHAs. Record both short SHAs.
- **requirements**: ticket text or the user's request, copied into the brief.
- **mode**: `full`, or `recheck` with the path of the prior report.
- **reviewer model**: `gpt-6.1-sol` by default. Use Opus 5.5 when a GPT model wrote the
  code. The reviewer must differ from the author.
- **lead session id**: `$TOWER_TERMINAL_ID`, written into the brief so the reviewer can
  send the pointer back.
- **effort**: `medium` when the diff is under a few hundred lines (`git diff --shortstat`),
  `high` above that.

## Steps

### 1. Brief

Write it under `~/code/docs/tower-review/` (not `/tmp`; Tower resumes agents after a
reboot):

```sh
mkdir -p "$HOME/code/docs/tower-review"
slug=<ticket-lower>-<slug>
brief="$HOME/code/docs/tower-review/$(date +%F-%H%M%S)-$slug.md"
report="${brief%.md}-report.md"
[ -e "$brief" ] && echo "exists: $brief" && exit 1
```

Follow `references/review-brief-template.md`. Give the exact diff command and the file
list so the reviewer does not explore the repo. Put in the requirements, risky areas,
and what is unverified.

### 2. Start the terminal

Flags cannot be passed through `kind: pi`, so start a shell session and run pi in it
with a stripped setup. Pi loads about 9k tokens of skills, extensions, MCP schemas and
context files by default; the flags below leave about 1k.

```sh
space=$(basename "$TOWER_SPACE_BRIEF" .md)
payload=$(jq -n --arg s "$space" --arg c "$repo" --arg t "Review <TICKET> <short title>" \
  '{spaceId:$s, kind:"shell", cwd:$c, title:$t}')
id=$(tower api POST sessions "$payload" | jq -r '.id') && [ -n "$id" ] && [ "$id" != null ]
```

If the API refuses `shell`, stop and report it. Do not start `kind: pi` with defaults
without telling the user, because that gives up the token saving.

Wait for the shell prompt (poll `tower read "$id" --lines 5` about every 3 seconds, up
to 30 seconds), then send one line:

```sh
tower send "$id" "pi -ne -ns -np -nc --no-mcp --tools read,grep,find,ls,bash --model <model> --thinking <effort> @$brief 'Follow the brief.'"
```

`--tools` excludes `edit` and `write`, so the reviewer cannot change files. It keeps
`bash` for `git diff`, `git log` and test reads. The brief tells it to run read-only
commands only. Check the response says `"submitted": true`.

### 3. Confirm pickup

Poll the pane about every 10 seconds, up to 2 minutes, until it shows the reviewer
reading the brief. Until then the review is unconfirmed: say so.

### 4. Leave a trail

Append to `$TOWER_DATA_DIR/notes/$space.md`:

```md
## Review: <TICKET> <title>
- Target: <PR or base...head>, mode <full|recheck>, reviewer <model>
- Brief: <brief path>
- Report (written by the reviewer): <report path>
- Terminal "Review <TICKET> ..." (session <id>)
```

### 5. Stop, then read one file

Tell the user the terminal title and the report path, and stop working on the review.
Do not poll in a loop. The brief tells the reviewer to send a one-line pointer back with
`tower send` when it finishes. When the pointer arrives, read the report once.

## Using the report

- Findings arrive already verified by the reviewer. Spot-check only those that drive a
  design change. The lead keeps the judgment on what to fix.
- Fix the real findings, then, if a re-check is warranted, run this skill again in
  `recheck` mode with the report path. The re-check covers only the prior findings
  against the new diff.
- A re-check that comes back clean ends the loop. Do not start a third review.
- Reviewers write findings only. They never push fixes.

## Don't

- Don't run `codex-review` or a reviewer subagent on this thread instead.
- Don't paste the diff or the findings into the brief or into chat. Give paths and refs.
- Don't send the report contents back through `tower send`. One line with the path.
