# claude-manager-kit

A portable Claude Code workflow kit: one **manager window** at the top of a dev directory
orchestrates per-project sessions and agents — it writes instructions, verifies what comes
back, resolves merges, and rules on decisions. It never implements. This repo carries the
skills that make the pattern work and a bootstrap prompt that stands it up on a fresh
machine.

The pattern was grown on one machine at single-project scope, then generalized. The core of
it is discipline, not tooling: verify delegated claims before relaying them, classify every
claim as confirmed / inferred / unknown, hand off with a map instead of a narrative, and
close every phase with a plain-language debrief.

## What's inside

| File | What it is |
|---|---|
| `BOOTSTRAP.md` | Paste-ready prompt that stands up a manager window at your dev root. Staged with hard gates — the agent surveys and reports before it writes anything. |
| `skills/manager-handover` | End-of-session handoff for an orchestrator window: the board, delegations in flight, standing rulings, retractions, open threads — the map, not the narrative. |
| `skills/context-handover` | End-of-session handoff for a working window: what landed vs what was only discussed, undocumented decisions, a copy-pasteable resume prompt. |
| `skills/debrief` | Explain finished work to someone who wasn't in the weeds. Every weakness gets two explicit verdicts — observable? fixable? — and an honest classification: trade-off, open bet, or unnoticed gap. |
| `skills/debug-no-bs` | Debugging discipline: no analysis until the full source trace is read end-to-end. Grep locates code; it is never evidence. |
| `skills/wwcd` | "What Would Codex Do" — an eleven-check rigor gate run before finalizing plans, committing migrations, or claiming completeness. |

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

Verify: start any Claude Code session and ask it to list available skills — the five above
should appear.

## Stand up a manager

1. Open a Claude Code session **in your parent dev directory** (e.g. `~/Dev`), not inside
   any project — the manager's root defines what it can see.
2. Paste the contents of `BOOTSTRAP.md`.
3. The agent surveys the machine and presents THE BOARD, then stops. Everything after that
   is gated on your go: the charter, the skill check, tool installs, and finally a
   shakedown where you interrogate the charter (the bootstrap suggests a grill-style
   skill for this) and have the manager produce its first real handover.

## Deliberately not in this repo

- **GSD** (the planning/execution workflow system) and **superpowers** (obra's
  process-discipline skills) — install from their own sources; the bootstrap's tooling
  stage covers finding the current install method. One hard-won caveat travels with GSD:
  prefer project-level installs — a global install leaked absolute home-directory paths
  into subagent prompts.
- **Domain skill packs** (the iOS/Swift skills, apple-dev-docs, graphify) — they ship with
  their own tools and installers and aren't mine to republish.
- **Anything work-internal.** This repo is public; employer-specific skills never enter it.
