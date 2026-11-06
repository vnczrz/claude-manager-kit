The dispatcher picks the git shape from the shape of the work.

## Pick the shape
| Work                                   | Branch                    | Review              | Merge             |
| One-off edit you asked for             | short branch              | none                | fast-forward      |
| One scoped ticket, one worker          | branch per ticket         | checker PASS + tests| fast-forward      |
| Orchestrator, pieces ship on their own | branch per piece          | one PR per piece    | PR, code review   |
| Orchestrator, pieces depend on each    | integration branch;       | one PR at the end   | PR, code review   |
|   other or must ship together          | workers branch off it     |                     |                   |
Docs and planning files may go straight to main where the project allows it.

Always a PR with code review, whatever the size:
- schema or data-shape changes, migrations
- deploy config, auth, secrets handling, CI
- instruction surface: CLAUDE.md, skills, hooks, settings
- anything going to main in a project where main deploys

## Worktrees
- One worktree per worker that edits files in parallel. Readers need none.
- Location: <repo>/.claude/worktrees/<ticket-id>. Branch: <type>/<ticket-id>-<topic>.
- Never commit into another agent's worktree.
- After the merge: git worktree remove, then delete the branch.

## Who merges
- The project's manager merges into main. Workers and orchestrators never do.
- Director does not merge project code.
- Push freely once a branch has an upstream. A first push asks (settings rule).

## Hygiene
- Check the branch before every commit. Stage named paths.
- No force push, no commit -a, no git add -A (chain-guard blocks them).
- Pull with --ff-only. Never reset, clean or discard another session's work.
- Weekly: list merged branches and stale worktrees; remove them with the owner's OK.
- Repos the charter marks read-only stay read-only.