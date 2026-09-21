---
name: director-handover
description: Use when a director window — one that holds a portfolio of projects, each with its own manager session, and never touches code — needs to hand off to a successor. Triggers on "director handover", "hand off the director window", "/director-handover", or when a director window is compacting and its project map would otherwise be lost. For a project-level manager window use manager-handover; for a window that did the work use context-handover.
---

# Director Handover

Emit the **portfolio**, not the projects.

A director window holds the top line. It keeps the registry of live projects, keeps the
rollup board, issues tickets to project managers, and verifies what comes back. It does not
cut worktrees, review diffs, or implement. Each project's own manager holds that detail and
writes its own `manager-handover`.

So this handoff carries what no project manager can know: which projects are live, who is
driving each one, which tickets are outstanding, and which cross-project rulings bind.

## Which handover to write

| Kind | The window's unit of work |
|---|---|
| `context-handover` | A task. It did the work. |
| `manager-handover` | A branch or worktree. It orchestrated executors in one repo. |
| `director-handover` | A project. It dispatched managers across many repos. |

If a window did two of these, write the one matching its highest tier and link the rest.

## Inherit, do not restate

Read `manager-handover` and follow it for:

- **Step 1's claim classification.** Every claim gets labelled: committed, on disk
  uncommitted, delegated and reported, measured or read from source, reasoned, or
  conversation-only. A director window runs almost entirely on delegated reports, so this
  matters more here than anywhere else. "The manager says it shipped" and "it shipped" are
  different sentences.
- **Its sections on standing rulings, findings that live only in this window, retractions,
  open threads, known gaps, and next action.** They apply unchanged. Drop any that are empty.

This skill replaces only the three parts that assume a single repository.

## Step 1 — Gather

A director window cannot gather with `git`. Its working directory is usually not a repo.
Use these four sources instead, in order.

1. **The registry.** The list of live projects and their manager sessions. The charter names
   where it lives. If a project is not in the registry it is not in scope, however busy it looks.
2. **The rollup board.** Run the `board` skill in portfolio mode over the registered
   directories only. This is filesystem and git truth, per project.
3. **`ListAgents`.** Which manager sessions are actually alive right now. Compare against the
   registry and report both directions of drift: a registered project with no live session,
   and a live session for nothing registered. Session names are the address for
   `SendMessage`, so record them verbatim, including underscores and case.
4. **The decisions log.** Cross-project rulings already recorded, so the handoff links them
   instead of repeating them.

Then review the session itself for what none of the four can show: tickets you issued, what
came back, and which returns you checked against source rather than believed.

## Step 2 — The portfolio table

The single most useful thing in the document.

| Project | Manager session | Board | State | Last verified |
|---|---|---|---|---|

- **Project** — the directory, as the registry spells it.
- **Manager session** — verbatim, or `none` if no session is live.
- **Board** — path to that project's board, or `none` if it has never run one.
- **State** — one clause from the survey, not from the manager's last report.
- **Last verified** — the date you last checked this project against source. An old date is
  information, not a failure. Hiding it is the failure.

Follow the table with cross-project state: which projects are clean, which carry uncommitted
work, which sit on a non-default branch, and which are flagged for rescue.

## Step 3 — Tickets in flight

Not delegations to executors. Tickets to project managers, which is a different shape: you
cannot see the work, only the report.

For each, by id: the ask in one line, which manager holds it, what came back, and
**whether you verified the return or only received it**. That last column is the entire value
of this section. A ticket reported done and never checked is an open ticket.

Also record tickets that were issued and then overtaken by events, and say what overtook them.

## Where to write it

In the director's artifact repo, alongside the other handovers. The charter names the path;
do not assume one. Prefix the title so the kind is obvious: `# Director Handover — <date>`.

Commit it and push it. A handoff on one disk has failed at half its job, and a handoff in a
chat window has failed at all of it.

## Anti-patterns

- **Reporting project internals.** If a project manager could have written it, it belongs in
  that project's handover. Link it.
- **Laundering manager reports into verified state.** The portfolio table's State column comes
  from the survey. The manager's claim goes in the ticket row, marked as received.
- **Carrying a project that is not in the registry.** Either register it or leave it out. A
  half-tracked project is worse than an untracked one, because it looks covered.
- **A registry with stale session names.** The names are an address. A wrong one silently
  sends nothing.
- **Padding the portfolio table with dormant projects** to look comprehensive. The successor
  reads it once, cold.
