---
name: tower-handoff
description: Hand implementation work to a new agent terminal in the current Tower space — make a git worktree and branch, write a self-contained brief, start a pi (or claude/codex) session in the worktree, and send it the brief. Only when the user asks for a tower handoff by name.
disable-model-invocation: true
---

# Tower handoff

Move work out of this session and into a fresh agent terminal that runs in its own
worktree, in the same Tower space. This session diagnoses and writes the brief. The new
terminal implements, tests, reviews, opens the PR and monitors it.

The new session starts with **none** of this conversation. The brief is the only thing
it gets, so it must carry every fact the work depends on.

**Stop at the first failed step** and tell the user which one failed and why. Don't
work around it. A half-made handoff (a worktree with no terminal, or a terminal with no
brief) is worse than none.

## Inputs

Work these out from the conversation. Ask only for what you can't infer:

- **repo**: the absolute path of the checkout the work belongs in
- **ticket and slug**: for example `FLO-4596` and `queued-send-import-wait`
- **agent**: `pi` by default; `claude` or `codex` if the user asks
- **base**: `main` unless the user names another branch

## Steps

### 1. Worktree and branch

```sh
repo=/Users/kingscott/code/<repo>   # absolute, no ~
base=main
slug=<ticket-lower>-<slug>
branch="kingscott/<TICKET>-<slug>"
wt="$repo-$slug"
git -C "$repo" fetch -q origin "$base" &&
git -C "$repo" worktree add -q "$wt" -b "$branch" "origin/$base" &&
git -C "$wt" rev-parse --abbrev-ref HEAD && git -C "$wt" rev-parse --short HEAD
```

If `$wt` or `$branch` already exists, stop and ask; don't reuse it. Check that the
printed branch is `$branch`, and record the short SHA for the brief.

**Carrying over work you've already started.** Copy it from the checkout it lives in,
not from whatever directory you happen to be in, and include binary changes and new
files:

```sh
src=<checkout with the changes>
git -C "$src" diff --binary HEAD | git -C "$wt" apply --index
git -C "$src" ls-files --others --exclude-standard   # copy any of these the work needs
git -C "$wt" status --short                          # compare with the source
```

Use this only when the source checkout was on the same base. Otherwise, say in the
brief which changes to recreate. Leave the source checkout as it is. Tell the user it
still holds the changes, and let them decide whether to clean it up.

### 2. Brief

Briefs are generated documents, so they go in `~/code/docs`, never `/tmp`: Tower
resumes agents after a reboot, and `/tmp` doesn't survive one.

```sh
mkdir -p "$HOME/code/docs/tower-handoff"
brief="$HOME/code/docs/tower-handoff/$(date +%F-%H%M%S)-$slug.md"
[ -e "$brief" ] && echo "exists: $brief" && exit 1
```

Follow `references/brief-template.md`. Before you send it, check that:

- the diagnosis has its evidence (IDs, counts, file:line), not just its conclusion;
- it says what is already done and what is **unverified**: tests never run, review not
  done;
- the **implementation** step states the behavior to build and what's out of scope,
  unless the brief says it's already written;
- every command is exact: package-targeted tests with a long timeout and output sent
  to a log file;
- it names the skills to use: `conventional-commit`, `file-pr`, `monitor-pr`;
- every done-when criterion can be shown in the agent's own output, because a goal
  evaluator reads no files.

### 3. Start the terminal

```sh
space=$(basename "$TOWER_SPACE_BRIEF" .md)
title="<TICKET> <short title>"
payload=$(jq -n --arg s "$space" --arg k "<agent>" --arg c "$wt" --arg t "$title" \
  '{spaceId:$s, kind:$k, cwd:$c, title:$t}')
id=$(tower api POST sessions "$payload" | jq -r '.id') && [ -n "$id" ] && [ "$id" != null ]
```

Build the JSON with `jq`, not by hand, so quotes in titles or paths can't break it.
`cwd` must be absolute.

Then wait for the agent's prompt. Poll about every 5 seconds, for up to 60 seconds:

```sh
tower api agents | jq -r --arg id "$id" '.panes[] | select(.sessionId == $id) | .snapshot'
```

If no prompt appears, stop and tell the user the session ID. Don't send into a pane
that's still starting.

### 4. Send the pointer

```sh
tower send "$id" "Read $brief and execute every step in it, in this worktree. It is the full brief: create a goal, do the work, open the PR, and monitor it until CI is green."
```

Send **one line**. `tower send` submits what it types, so a pasted multi-line brief
goes out in pieces. Check that the response says `"submitted": true`.

Then confirm pickup. Poll the pane about every 10 seconds, for up to 2 minutes, until
it shows the agent reading the brief or a goal banner. Until you see that, the work is
still this session's: don't announce the handoff, and if pickup never shows, tell the
user it's unconfirmed.

### 5. Leave a trail

Append to the space notepad at `$TOWER_DATA_DIR/notes/$space.md` (create it if it
doesn't exist yet):

```md
## <TICKET>: <title>
- Worktree: <path>, branch <branch>
- Brief: <brief path>
- Running in terminal "<title>" (session <id>)
```

### 6. Stop

Tell the user the terminal title, worktree, branch and brief path. If this session has
a goal for the same work, tell the user to clear it. The new terminal owns the work
now, so don't keep working on it here.

## Don't

- Don't start the terminal before the brief exists, or send the brief's contents
  instead of its path.
- Don't run `stack dev`, dev servers or long jobs in this session's own shell for the
  handoff. Their output belongs in the new terminal.
- Don't hand off without the evidence. "Do not re-investigate" only works when the
  brief shows why the diagnosis is right.
