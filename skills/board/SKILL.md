---
name: board
description: Rebuild THE BOARD from ground truth for the current directory — git state, in-flight work, planning maturity — and optionally persist it as a kanban BOARD.md. Works at a dev root (one row per project) or inside a single project (worktrees, branches, delegations). Use when the user says "board", "/board", "what's in flight", "status across projects", or at the start of any manager session.
---

# The Board

One table that answers "what is the state of play right now" — rebuilt from ground truth
every time it runs, never from memory of a previous session. A board built from memory is
the least reliable artifact a manager can produce; this skill exists so that never happens.

## Step 1 — Detect mode

- The current directory **is a git repo** → **project mode**.
- The current directory is not a repo but **contains git repos one level down** →
  **portfolio mode** (a dev root).
- Both (a repo that contains child repos) → ask which view the user wants, once.
- Neither → say so and stop; there is nothing to survey.

## Step 2 — Survey (read-only; change nothing)

Everything comes from `git` and the filesystem. Never from what a previous session said.

**Portfolio mode** — per child project:

- current branch; dirty or clean (`git status --porcelain`); unpushed commits
  (`git log --oneline @{u}..` where an upstream exists); date of last commit
- worktrees beyond the main checkout (`git worktree list`)
- planning maturity: presence of CLAUDE.md / AGENTS.md / `.planning/` / `docs/`
- detectable in-flight work: dirty trees, branches ahead of origin, non-main checkouts

**Project mode:**

- `git worktree list`, `git branch -vv`, `git status --porcelain`
- unpushed commits; open PRs via `gh pr list` if the repo has a remote and `gh` is present
- project state files if the project keeps them (e.g. `.planning/STATE.md`, pending todos)
- stale branches: merged into the default branch but still present

## Step 3 — Present

Render the board in chat, laconic: one-line verdict first ("3 projects clean, 1
mid-flight, 1 unpushed"), then the table, then **open threads** — anything that looks
mid-flight, each with a suggested owner and the trigger that makes it actionable. Do not
resolve anything; the board reports, the human rules.

## Step 4 — Persist (the kanban)

- **No `BOARD.md` in the current directory** → ask once: "Want this as a persistent kanban
  board here (`BOARD.md`)?" Yes → create it in the format below. No → chat output only;
  ask again next invocation only if the user brings it up.
- **`BOARD.md` exists** → refresh it without asking, following the refresh rules.

### BOARD.md format — two zones, one hard seam

```markdown
# THE BOARD — <directory name>

_Surveyed: <YYYY-MM-DD HH:MM>. Above the seam is regenerated every run; below it is
curated by hand — the survey never rewrites it, only flags drift._

## Ground truth

<the surveyed table>

## Open threads (detected)

<auto-detected threads, each: what · suggested owner · trigger>

---
<!-- SEAM — curated zone. The survey appends drift flags here but never moves or deletes cards. -->

## In flight

## Waiting on a ruling

## Blocked

## Done (recent)
```

Cards in the curated zone are one-liners: what · who's driving · next action or trigger.

### Refresh rules

1. Regenerate everything **above** the seam, including the survey timestamp.
2. Never move, edit, or delete anything **below** the seam. The human moves cards, not the
   survey.
3. **Reconcile instead**: when a curated card contradicts surveyed truth (a card sits in
   "In flight" but its branch is merged; a card says "waiting" but the PR landed), append
   one line under that card — `⚠ drift: <what the survey found, dated>` — and surface the
   same list in chat. Remove a drift flag only when the survey that added it no longer
   observes the contradiction.
4. If the curated zone is missing (file was hand-edited down to the table), recreate the
   empty column headings and say so.

## Laws carried

- Ground truth only — git and the filesystem, never a previous session's narrative.
- Read-only until the user opts into persistence; the only file this skill ever writes is
  `BOARD.md` in the invoked directory.
- Laconic output: verdict first, no padding, threads with owners and triggers or not at all.
