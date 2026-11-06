<!--
Charter template. Copy this file to <dev-root>/CLAUDE.md and replace each placeholder.
<dev-root>              the directory that holds your repos
<org policy file>       your org's compliance and writing-style rules (for example ~/.claude/CLAUDE.md)
<read-only repo>        a repo where Claude must never commit, push or open a PR
<registry file>         the file that lists each project and its manager session
<director protocol file>  the Director's rules for returns, cards and its own work (skip with one project)
<public repo>           a repo that is public; <org> is the name that must not appear in it
Delete this comment when done.
-->
# Dev charter

Applies to every session under `<dev-root>`. `<org policy file>` (compliance, writing style)
and the org guardrails come first. A project's own `CLAUDE.md` wins on project internals; say
so in one line where it contradicts this file.

Tags: `[all]` everyone; `[orch]` whoever orchestrates at this level; `[dir]` the Director only.

## Proportionality

Small, reversible work: just do it. No dispatch, plan or debrief. Small work skips the
ceremony, not the branch. Ceremony scales with stakes. Laws 1-3 hold on every task, because
they protect honesty and other agents' work. If a rule costs more than it saves, change this file.

## Style `[all]`

Lead with the answer. Plain words, short sentences, no emojis. Style rules live in
`<org policy file>`. Helpers load this file; do not paste it into prompts. Explore and Plan
do not load it, so name a rule in their prompt if it matters. Padded reports get sent back.
"Help me understand X": plain language, one analogy, consequence over mechanism, no codenames.

## Laws

1. `[all]` Verify before relaying. A report is a claim until checked against source, once,
   at the lowest level that can see the work.
2. `[all]` Label claims confirmed, inferred or unknown.
3. `[all]` Never commit into another agent's worktree. Stage named paths, never `git add -A`.
4. `[all]` Code work branches; check the branch before every commit. `<read-only repo>` is
   read-only: no commits, pushes or PRs. -> `dispatch` skill, `git.md`
5. `[all]` Docs state the end state. Supersede by deleting.
6. `[orch]` Every dispatch names its scope and a stop condition. -> `dispatch` skill
7. `[orch]` Checks go to the `checker` agent, which writes `verification.md`. The checker is
   never the builder; pass criteria come from the plan.
   -> `dispatch` skill; Director's side: `<director protocol file>`
8. `[orch]` Mechanical work runs on a cheap model, with the model set. -> `dispatch` skill
9. `[orch]` Hand over before a clear, a compaction or the end of the day. -> `handover` skill
10. `[orch]` Decide with real numbers, files or images in front of the owner.
11. `[orch]` Close non-trivial work with a debrief. -> `debrief` skill
12. `[orch]` Keep a decisions log for work being shaped. -> `.planning/decisions.md`

## Channels `[all]`

Workers report to their project's manager. Managers report to Director. Director messages
managers only. The registry `<registry file>` names each manager verbatim; a project not
on it is out of scope. Ticket and report shapes: the `dispatch` skill's `formats.md`.
Director's rules for returns, cards and its own work: `<director protocol file>`.

## Project layout `[all]`

`docs/` is for outside readers, end state only. `.planning/` is working memory: `BOARD.md`,
`decisions.md`, `plans/`, `handoffs/`, `tickets/<id>/`, `spikes/`, `notes/`, `comms/`, and
gitignored `local/`. The repo root holds only run files. Migration steps: `<director protocol file>`.

## Risks `[dir]`

`<public repo>` is public: no `<org>` content. Plaintext credentials: flag, stop, rotate.
