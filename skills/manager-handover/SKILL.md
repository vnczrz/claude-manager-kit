---
name: manager-handover
description: Use when a long-lived orchestrating/manager window needs to hand off to a successor — one that cuts worktrees, writes executor instructions, reviews PRs, and holds the project map rather than implementing. Triggers on "manager handover", "hand off the manager window", "/manager-handover", or when a manager window is compacting and its map would otherwise be lost. For ordinary working sessions use context-handover instead.
---

# Manager Handover

Emit the **map**, not the narrative.

A manager window orchestrates: it cuts worktrees, writes instructions for executor windows,
reviews work coming back, resolves merges, and rules on decisions. It does not implement. Its
successor therefore needs a completely different handoff from a working session's — it needs to
know *what exists, who is doing what, and which rulings bind*, not what changed in which file.

Phase summaries, commit messages, and phase logs already carry the code narrative. **Do not
duplicate them.** If a fact is durably recorded in the repo, link to it and move on.

## When to use this instead of `context-handover`

| Use `manager-handover` | Use `context-handover` |
|---|---|
| The window orchestrated others' work | The window did the work |
| Output was worktrees, instructions, reviews, rulings | Output was commits in one branch |
| Successor needs the map | Successor needs to resume a task |

If the window did both, write this one and link the phase artifacts for the implementation half.

## Step 1 — Gather. Never write from memory.

A manager window's memory is the least reliable thing about it — it has been summarizing others'
work all session. Establish ground truth first.

```bash
git worktree list
git branch -vv
git log --oneline -12
git status --porcelain
git log --oneline origin/master..master   # unpushed
gh pr list --state all --limit 10          # if the repo has a remote
```

Also check, if the project has them: `.planning/STATE.md` for the current position and standing
todos, `.planning/todos/pending/` for deferred work with triggers, and any charter document
describing the manager role.

**Then review the session itself** — the repo cannot tell you this part. Look for: investigations
that produced a finding recorded nowhere, questions whose answers constrain future decisions, and
claims this window made and later corrected. This is the material that dies with the window, and it
is the reason the handoff exists at all.

**For every claim you are about to write, classify it:**

- **Committed** — in git, verifiable
- **On disk, uncommitted** — visible in `git status`
- **Delegated and reported** — an executor said it did this; you have its report, not the work
- **Measured / read from source** — a number or fact established by running or reading something
- **Reasoned** — a plausible inference not verified against source; say so, every time
- **Conversation-only** — exists nowhere but this window, and is the entire reason this file exists

Never present a delegated report as verified fact unless you independently checked it. If you did
check it, say what you checked and what you did not.

## Step 2 — Write the map

Use these sections. Drop any that are empty rather than padding them.

### 1. Role and boundary

One line on what this window was doing, and what it deliberately refused. If a charter exists,
link it rather than restating it.

### 2. The board

A table, because this is the single most useful thing in the document:

| Worktree | Branch | Commit | State | Who's driving |
|---|---|---|---|---|

Then: what is merged to trunk, what is unpushed, what branches are preserved deliberately.

### 3. Delegations in flight

For each one: **who** (which executor window/tool), **what** they were told to do, **where** the
instruction lives, and **status**. If a delegation was reported complete but not independently
verified, say so plainly — that distinction is the whole value of the entry.

### 4. Standing rulings

Decisions made in this window that bind future work. For each: the ruling, the reason, and
**where it is durably recorded**. A ruling that exists only in this handoff is fragile — flag it
and say where it should be written.

Keep this to rulings that *constrain future choices*. Ordinary decisions belong in phase artifacts.

### 5. Findings that live only in this window

A manager window answers questions. Some of those answers cost real effort and are recorded
nowhere — a one-off question turns into an investigation, produces a genuine finding, and then the
conversation moves on. That knowledge dies with the window unless it is carried.

**Prefer landing it durably over carrying it here.** If a finding constrains future work, write it
into the repo — a doc, a todo with a trigger, a phase artifact — and note *where* in this section.
The handoff is the last resort for knowledge, not its first home.

Carry a finding when the answer to either question is yes:

- Would the successor **waste effort re-deriving** this?
- Would the successor **make a worse decision** without knowing it?

If neither, drop it. A passing explanation is not a finding.

For each: what was asked, what was established, and **how it was established** — measured, read
from source, or reasoned. A measured number and a plausible inference deserve different trust and
the successor cannot tell them apart later.

### 6. Retractions

Claims this window made and then corrected. Unusual to include, and valuable precisely because it
is: a successor inheriting only the final state does not know which plausible-sounding conclusions
have **already been tested and found wrong**, and will cheerfully re-derive them.

For each: what was claimed, what turned out to be true, and what made the difference. Keep it to
corrections that changed a decision or would mislead if repeated — not every slip.

### 7. Open threads

Each with an **owner** and a **trigger** — the condition that makes it actionable. A thread with
neither is a wish, not a thread. Say so or drop it.

### 8. Known gaps and where they bite

Things accepted rather than fixed, and the phase or moment where the cost lands. Distinguish:
a **deliberate trade-off** someone chose, an **open bet** only running it will settle, and a
**gap nobody noticed**. These deserve different reactions.

### 9. Next action

One sentence. What the successor does first.

## Step 3 — Resume prompt

A copy-pasteable block. It must carry:

- Which files to read first — this handoff, the charter if one exists, the project's state file
- The role: **this is a manager window; orchestrate, review, and decide — do not implement here**
- Baseline discipline: verify against source rather than memory; classify confirmed vs inferred vs
  unknown; present before acting on anything irreversible; don't audit values a spec deliberately
  left open
- Any project-specific law that a fresh window would otherwise violate — data handling rules,
  branch/push policy, non-interference with other running agents
- Current state in one sentence, and the immediate next action

## Where to write it

Alongside the project's other handoffs if a convention exists (commonly
`.planning/references/workflows/handoffs/YYYY-MM-DD-HH-MM-handoff.md`). Prefix the title so the
kind is obvious at a glance: `# Manager Handover — <date>`.

Commit it. A handoff that lives only in a chat window has already failed at its one job.

## Anti-patterns

- **Restating what phase summaries already say.** Link them.
- **Narrating code changes.** Not this window's contribution and not what the successor needs.
- **Laundering a delegated report into a verified claim.** "Codex reports X" and "X is true" are
  different sentences.
- **Listing threads with no owner or trigger.** They read as work and never get done.
- **Turning Findings into a transcript.** It is not "questions the owner asked this session." It is
  the small set whose loss would cost the successor real effort or a worse decision. If a finding
  belongs in the repo, put it there and link it — carrying it here instead is how durable knowledge
  ends up in a file nobody reads twice.
- **Padding to look thorough.** The successor reads this cold, once. Every line earns its place or
  costs attention that the real content needed.
