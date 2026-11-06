# claude-manager-kit

A portable Claude Code workflow kit: one **manager window** at the top of a dev directory
orchestrates per-project sessions and agents — it writes instructions, verifies what comes
back, resolves merges, and rules on decisions. The pattern is hierarchical: the top-level
manager dispatches to per-project windows, and each project can run its **own manager one
level down** that orchestrates executors inside that project. The top-level manager stays
out of the code by preference — dispatch and review is its job — while project-level
managers may implement directly when that's the sensible move. One style rule runs all the
way down the line: **laconic communication** — lead with the answer, no padding, terse but
never vague. This repo carries the skills that make the pattern work and a bootstrap
prompt that stands it up on a fresh machine.

The pattern was grown on one machine at single-project scope, then generalized. The core of
it is discipline, not tooling: verify delegated claims before relaying them, classify every
claim as confirmed / inferred / unknown, hand off with a map instead of a narrative, and
close every phase with a plain-language debrief.

## What's inside

| File | What it is |
|---|---|
| `BOOTSTRAP.md` | Paste-ready prompts for the two orchestrating levels: a director above every repo, and a manager per repo or dev directory. The manager prompt is staged with hard gates — the agent surveys and reports before it writes anything. |
| `skills/board` | Rebuild THE BOARD from ground truth in any directory — portfolio mode at a dev root (one row per project) or project mode inside a repo. Optionally persists as a kanban `BOARD.md`: surveyed zone regenerated every run, curated cards never touched, contradictions flagged as drift. |
| `skills/unknowns` | Pre-fan-out unknowns pass: the four Rumsfeld quadrants walked against the dispatch being prepared, a domain primer when the owner is new to the territory, and each unknown routed to its cheapest resolution (interview, reference, probe, prototype) before tokens get spent. |
| `skills/handover` | One handover for every window kind (director, manager, working): saves state, chain, open threads and a resume prompt so a cleared session continues without loss. |
| `skills/debrief` | Explain finished work to someone who wasn't in the weeds. Every weakness gets two explicit verdicts — observable? fixable? — and an honest classification: trade-off, open bet, or unnoticed gap. |
| `skills/debug-no-bs` | Debugging discipline: no analysis until the full source trace is read end-to-end. Grep locates code; it is never evidence. |
| `agents/` | The four agent files: `worker`, `checker`, `orchestrator`, `Explore`. They set the model, tools and turn limit for each helper. Copy them to `~/.claude/agents/`. |
| `settings/` | `permissions.example.json`: ask rules for remotes, upstream pushes and synced-folder edits, plus an example deny block for a read-only repo. The merge-ask and first-push-ask hooks ship with the chain plugin repo, not here. |
| `templates/` | `charter.md` (a charter with placeholders), `tests.md` (ten test tasks), `apply-checklist.md` (the ten steps, and what to adapt). |

## Install on a new machine

```bash
git clone https://github.com/vnczrz/claude-manager-kit.git ~/claude-manager-kit
```

Then link the skills into Claude Code's user-level skills directory — a symlink means
`git pull` updates flow through without re-copying:

```bash
mkdir -p ~/.claude/skills
for s in ~/claude-manager-kit/skills/*/; do
  ln -sfn "$s" ~/.claude/skills/"$(basename "$s")"
done
```

(Prefer copies over symlinks? `cp -R ~/claude-manager-kit/skills/* ~/.claude/skills/` works
the same; you just re-copy after pulling updates.)

Verify: start any Claude Code session and ask it to list available skills — the seven above
should appear.

## The companion mod

This kit is how a window thinks: the skills and the bootstrap prose. Its companion,
[chain-of-command](https://github.com/vnc-ufc/chain-of-command), is what the terminal does:
the `chain` mod supplies `/spawn` to start sessions, the tree that shows who started whom,
the guards, and `/show`. The bootstrap prompts assume both. Install the mod at user scope:

```bash
claude plugin marketplace add vnc-ufc/chain-of-command
claude plugin install chain@chain-of-command --scope user
```

## Stand up a manager

1. Open a Claude Code session **in your parent dev directory** (e.g. `~/Dev`), not inside
   any project — the manager's root defines what it can see.
2. Paste the contents of `BOOTSTRAP.md`.
3. The agent audits the machine against the kit and shows a table, then stops. After your
   go, it surveys and presents THE BOARD, then stops. Everything after that
   is gated on your go: the charter, the skill check, tool installs, and finally a
   shakedown where you interrogate the charter (the bootstrap suggests a grill-style
   skill for this) and have the manager produce its first real handover.

## Run the tune-up on another machine

Clone the kit there, open a session in the dev root and paste `BOOTSTRAP.md`.
Stage 0 audits the machine read-only. Later stages install only what you approve at its gate.

## Deliberately not in this repo

- **GSD** (the planning/execution workflow system) and **superpowers** (obra's
  process-discipline skills) — install from their own sources; the bootstrap's tooling
  stage covers finding the current install method. One hard-won caveat travels with GSD:
  prefer project-level installs — a global install leaked absolute home-directory paths
  into subagent prompts.
- **Domain skill packs** (the iOS/Swift skills, apple-dev-docs, graphify) — they ship with
  their own tools and installers and aren't mine to republish.
- **Anything work-internal.** This repo is public; employer-specific skills never enter it.

## License

MIT — see [LICENSE](LICENSE).
