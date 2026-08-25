---
name: weekly-brag-doc
description: Create or update Scott's dated weekly brag doc to reflect the most recently completed week
---

Maintain Scott King's brag doc for his role as a Senior Full-Stack Software Engineer.

This document is Scott's single, durable source-of-truth growth artifact — it drives all of his growth and performance conversations, and the entire point of this automation is that he can stop manually tracking his own work because he trusts it to find and document everything. Thoroughness is the priority here, not efficiency or token/tool-call cost. Do not treat any part of this as a cheap, single-pass search job: search each source deeply, follow up on partial or ambiguous signals instead of leaving them unresolved, and be willing to spend significantly more tool calls and time than a typical automation would. Missing a real accomplishment is a much worse outcome than spending extra effort to find it.

## Identity

- GitHub: `kingscott`
- Email: `scott.king@customer.io`
- Slack user ID: `U0BJU3C7Q4B`
- In other sources, infer Scott from the authenticated user/profile.

## Target week

Default to the most recently completed Monday-Sunday week before this run. If the invocation names an explicit week or date range (e.g. "this week", "week of May 4", "last two weeks"), honor that instead. Use the target week's Monday date for the weekly filename.

## Evidence gathering

For the target week, gather evidence from all available connected sources: GitHub, Slack, Notion, Linear, Gmail, Google Drive, and Google Calendar.

Prefer configured connector/plugin/MCP tools for Slack, Notion, Linear, Gmail, Google Drive, and Google Calendar: they provide the required authentication, permissions, typed data access, richer source metadata, and lower-friction workflow inside automation runs. Do not spend task time trying to make CLIs work for those sources merely to save connector context; repeated sandbox, auth, or token-scope friction costs more than the context it saves.

**GitHub is the exception.** There is no GitHub connector on this machine, and the `gh` CLI is installed and already authenticated as `kingscott`. Use `gh` as the primary GitHub path — `gh search prs`, `gh search commits`, `gh pr list`, `gh pr view`, `gh api` for review comments and issue timelines. If a GitHub MCP/connector is added later, prefer it and fall back to `gh`.

For Notion, `notion-fetch` only accepts Notion page/database IDs or Notion URLs — to read a Slack permalink use the Slack MCP tools, never `notion-fetch`; and Notion exposes no MCP resources, so use `notion-search`/`notion-fetch`, not `ReadMcpResourceTool`.

Connector usage should be read-only, but not minimal: for every source, run multiple searches from different angles (varied keywords, date-boundary variations, author/participant/channel filters) rather than a single query, paginate fully through result sets instead of stopping at the first page, and read every follow-up thread/message/event/file that could plausibly support or contextualize a claim — not just the minimum needed for the claim you already suspect. If a first pass looks thin for a source that's normally active for Scott, treat that as a signal to search again with different terms before concluding there's nothing there.

If a source prompts for permission, lacks auth, times out, or cannot access the network, do not stall the whole automation; record `Source unavailable: reason if known` in Follow-Ups and continue with the remaining sources. Prefer direct evidence such as links, titles, dates, PRs, commits, comments, issue changes, docs, meetings, messages, and screenshots when available. Constrain every source search to the target week first; only widen the window for linked context needed to explain a target-week artifact. If a source is unavailable or returns no relevant evidence, note that in Follow-Ups as "Source unavailable: reason if known" or "No relevant evidence found".

## Evidence standards

Every accomplishment claim must be backed by evidence. Evidence can be a Slack message link, PR link, review or issue comment link, commit link, Linear issue link, Notion or Google Drive link, calendar/email reference, screenshot, or a mix of those sources. Include enough evidence in the Evidence Log to substantiate the Executive Summary, Impact Highlights, System And API Work, Collaboration And Leadership, and Performance Review Draft Bullets. If a claim is plausible but cannot be backed up, either omit it or explicitly move it to Follow-Ups as missing evidence. Do not present unsupported claims as facts.

For Slack evidence specifically, always cite the actual message/thread permalink (e.g. `https://<workspace>.slack.com/archives/<channel-id>/p<ts>`), not just a channel name plus a timestamp or date. A channel-and-timestamp reference forces a reader to go hunt for the message; a permalink lands them on it directly. If a permalink cannot be obtained for a given message, say so explicitly in that Evidence Log row rather than substituting a channel/timestamp reference as if it were equivalent.

Slack search must be exhaustive: pull literally every message Scott sent during the target week, not just whatever a first query happens to surface. Search `from:U0BJU3C7Q4B` bounded to the exact target week, across all channel types (public channels, private channels, DMs, and group DMs), and paginate through every page of results (follow `cursor`/`nextPageToken`) until the result set is fully exhausted. Do not stop after one query or one page just because it returned some hits. Message volume is not a reason to stop early — spend as many tool calls as it takes. For every substantive message found (i.e. not a one-word reaction, ack, or throwaway aside), if it is part of a thread, read the full thread rather than just the one message — a single message read out of thread context routinely understates or misrepresents what was actually going on. Use that thread context, together with the cross-source narrative-reconstruction guidance below, to correctly identify the larger initiative, decision, or effort each message was actually part of before deciding what it's evidence of.

## Cross-source narrative reconstruction

Do not read any single source in isolation and conclude what happened from it alone, especially GitHub. A ship-then-revert PR pair, a lone bot-config commit, or any other ambiguous signal must be cross-checked against Slack (and Notion/Linear/Gmail where relevant) for the surrounding narrative before you decide what it means: an announcement of intent beforehand, discussion of the tool/feature/bot by name, and any follow-up on why something was reverted or whether it was later reinstated. A revert does NOT necessarily mean a feature was abandoned or didn't work — it can mean a real feature hit a rollout/config snag, was pulled back temporarily, then fixed and reinstated later (check for a later re-merge of the same feature by name, even outside the target week, before concluding it "didn't work as intended").

When Scott builds and launches a new automated agent, bot, tool, skill, or internal capability — including standing up supporting infrastructure like a taggable Slack usergroup for routing reviews, or announcing it to the team — that is a first-class, high-value accomplishment in its own right, often more brag-worthy than the individual PRs that implemented pieces of it. Actively look for these launches (new bots/apps added to a channel, new Slack usergroups, announcement messages, "I built X to do Y" framing) rather than only noticing the scattered PRs and reverts that are its visible trace. When you find one of these clusters, reconstruct the full arc across sources (built → announced → issue hit → reverted → fixed → reinstated, as applicable) and lead with the actual capability delivered in the Executive Summary and Impact Highlights — not just a list of the PRs that touched it.

## Source-specific rules

**Gmail.** Use `scott.king@customer.io`. Focus on stakeholder threads, planning, coordination, decisions, and recognition. Ignore forwarded bulk/announcement emails from lists such as `engineering@customer.io`. Do not count those as Scott's work or evidence unless Scott separately replied, acted on, or was directly discussed in the thread.

**GitHub.** Only count activity in repositories under the `customerio` org. Ignore and exclude from evidence any activity in personal or other non-`customerio` repositories (e.g. Scott's own `ai.md` skills repo or other side projects); do not cite it, even as a Follow-Up, unless the user asks about it directly. Prefer activity authored by, assigned to, reviewed by, mentioning, or directly involving Scott.

**Google Calendar.** Before or alongside other evidence gathering, check the target week for all-day events indicating time off (PTO, vacation, sick day, health day, out of office, or similar). If such an event covers part or all of the target week, treat that as a strong signal and explicitly note the out-of-office period in the Executive Summary and Follow-Ups (e.g. "Scott was on PTO on [dates]"), and scope the rest of the entry's claims to the days he was actually working. If an all-day time-off event covers the entire target week, keep the entry short: note the time off as the primary fact and do not manufacture a full week of accomplishments around incidental system activity (e.g., a PR merged by someone else, or automated on-call notifications) that occurred while he was out. Also use the calendar for meetings Scott attended as evidence of collaboration, cross-functional alignment, decisions, and technical leadership, citing the meeting title and date.

**Delegated AI agents.** *(Placeholder — not yet configured.)* Scott intends to stand up a named delegated agent that opens PRs and takes Slack actions under his direction. Until that exists, there is no bot account to attribute work to, and agent-assisted work simply lands as Scott's own commits and messages. When it does exist, this section must specify: the exact bot/App account name, the exact attribution marker required in the PR body (author alone is never sufficient — shared bot accounts serve many agents), and the corroborating signal required on top of it (Scott directing the agent, Scott requesting review, or Scott merging). Evidence Log rows for agent actions must state explicitly that the action was performed by an agent under Scott's direction, so attribution stays transparent. Until this is filled in, ignore bot-authored activity rather than guessing.

## AI coding-agent sessions

Capture knowledge worth publishing. Review the target week's AI coding-agent sessions — Codex under `~/.codex/sessions` and Claude Code under `~/.claude/projects` — for substantial questions Scott answered, investigations he ran, or reusable knowledge he produced that is NOT already captured as a merged PR, Notion page, or other durable artifact — the kind of thing that would make a good internal doc, runbook, team FAQ, or reusable skill. This work is easy to lose because it leaves no artifact, so surfacing it is a primary goal of this automation, not an afterthought. Note each candidate by topic and where it came from; summarize sensitive content rather than quoting it.

## What counts

Focus the digest on performance-review value for a senior full-stack engineer. Weight most heavily:

1. **System and API design across the stack** — architecture spanning frontend and backend, service boundaries, data modeling, API contracts, migrations, and the reasoning behind those choices.
2. **Leadership and unblocking** — mentorship, code reviews that changed outcomes, cross-functional alignment, planning, decision-making, ownership, and removing obstacles for other people.

Also credit, at lower weight: user and business impact, code quality, reliability, performance, developer experience and AI-assisted development leverage, technical debt reduction, and accessibility.

## Output location

Write to this Google Drive folder:

`/Users/kingscott/Google Drive/My Drive/Brag-doc`

Before long evidence gathering, run quick local preflight checks that the target folder is readable and writable and that the recovery repository can run status:

- `test -d "/Users/kingscott/Google Drive/My Drive/Brag-doc"`
- `test -w "/Users/kingscott/Google Drive/My Drive/Brag-doc"`
- `git --git-dir="$HOME/.local/state/brag-docs.git" --work-tree="/Users/kingscott/Google Drive/My Drive/Brag-doc" status --short`

macOS TCC frequently blocks shell access to Google Drive with `Operation not permitted` even when the path exists; this requires Full Disk Access for the terminal/app and a restart. If the preflight fails for any reason, do not stall: continue gathering all evidence, then write the entry to the local fallback folder `~/Documents/brag-docs/` (creating it if needed), and state clearly in the run response and in Follow-Ups that Drive was unwritable, the specific error, and that Full Disk Access is the likely cause. Never end a run having gathered evidence but written nothing.

Use one Markdown file per Monday-Sunday week. Name it `YYYY-MM-DD.md` using the target week's Monday date, for example `2026-08-03.md`. Use the same filename in the Google Drive folder and the local fallback folder.

If the weekly file does not exist, create it. If it already exists, read it first and replace its existing entry for that same Monday-Sunday range instead of appending a duplicate. A weekly file must contain exactly one week's entry and use the heading "Week of MMM D-MMM D, YYYY". Do not append new entries to legacy quarterly files such as `2026-Q3.md`, and do not migrate or delete legacy files unless the user explicitly requests it.

## Entry structure

## Week of MMM D-MMM D, YYYY

### Executive Summary
One concise paragraph summarizing the week in performance-review language, based only on the evidence listed below.

### Impact Highlights
- 3-6 outcome-oriented bullets with source references.

### System And API Work
- Bullets covering full-stack implementation and architecture, API and service design, data modeling, quality, reliability, performance, developer experience, reviews, or technical debt work. Every bullet must cite or clearly map to Evidence Log entries.

### Collaboration And Leadership
- Bullets covering mentoring, unblocking, planning, cross-functional alignment, documentation, decision-making, and ownership. Every bullet must cite or clearly map to Evidence Log entries.

### Evidence Log
| Date | Source | Evidence | Why it matters |
| --- | --- | --- | --- |

### Performance Review Draft Bullets
- 2-5 reusable first-person self-review bullets backed by the evidence above. Every draft bullet must be traceable to one or more Evidence Log rows.

### Publishable Knowledge
- Candidate items from the week worth turning into a team doc, runbook, internal FAQ, or plugin/skill: one line each with the topic, why it is reusable or valuable to others, and where it came from (session, thread, PR, or investigation). Draw especially on the AI-agent sessions reviewed above. If there is nothing genuinely worth publishing this week, write "None this week" rather than padding the list.

### Follow-Ups
- Missing context, unavailable sources, unresolved threads, missing evidence, or items worth checking next week.

## Writing standards

Be specific, cite sources with links when available, and avoid filler. Prefer fewer high-confidence accomplishments over a long undifferentiated activity list. If evidence is sparse, say so and produce a short factual entry; do not inflate or invent accomplishments. Summarize sensitive internal content instead of copying private messages or emails verbatim. Escape `|` characters in Evidence Log cells, or simplify evidence text so the Markdown table remains valid.

## Recovery commit

After successfully writing the weekly entry, commit the Brag-doc working tree to its local recovery repository if there is a diff. Use this Git repository/worktree pair so the `.git` data stays outside Google Drive sync:

- `git --git-dir="$HOME/.local/state/brag-docs.git" --work-tree="/Users/kingscott/Google Drive/My Drive/Brag-doc" status --short`
- if changed: `git --git-dir="$HOME/.local/state/brag-docs.git" --work-tree="/Users/kingscott/Google Drive/My Drive/Brag-doc" add --all`
- then: `git --git-dir="$HOME/.local/state/brag-docs.git" --work-tree="/Users/kingscott/Google Drive/My Drive/Brag-doc" commit -m "Update brag doc for week of YYYY-MM-DD"`, replacing YYYY-MM-DD with the target week Monday.

If the bare repo does not exist yet, create it with `git init --bare "$HOME/.local/state/brag-docs.git"` before the first commit. If there is no diff, do not create an empty commit. There is no remote and no push step — Google Drive provides off-machine backup. If the commit fails, leave the Markdown update in place and record the specific Git failure in Follow-Ups; a commit failure is non-fatal. When the run is complete, verify `git --git-dir="$HOME/.local/state/brag-docs.git" --work-tree="/Users/kingscott/Google Drive/My Drive/Brag-doc" status -sb` shows no uncommitted changes. If anything remains uncommitted, record the exact reason in Follow-Ups.
