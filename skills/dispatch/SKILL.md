---
name: dispatch
description: Sends work to a helper or another session. Use when work leaves this
  session - a subagent, a chain spawn, or a ticket to another project's manager.
---

0. Plan in this session, on its own model (Opus). Never delegate planning to a worker.
   - Small: the ticket is the plan (scope, stop condition, pass criteria).
   - Multi-step: plan mode (/plan), then save it to .planning/plans/<id>.md.
   - Large or unclear: superpowers brainstorming, then writing-plans.
   The checker's pass criteria come from that plan.
1. Scout first. Read the plan and list the work. Do small work yourself.
2. Check .planning/tickets/ first. Do not re-run finished work.
3. Pick the vehicle.
   - Subagent: nobody needs to talk to it.
   - chain spawn: someone will talk to it, or it outlives this session.
   - Ticket: the work belongs to another project's manager.
   Then pick the git shape (branch, PR or not, worktrees) from git.md.
4. Pick model and effort for each job.
   | Job                                   | Model  | Effort |
   | commit, move, rename, format          | haiku  | low    |
   | scoped change with a clear spec       | sonnet | medium |
   | several files, data logic, tests      | sonnet | high   |
   | unclear bug, design choice            | do it yourself     |
5. Write .planning/tickets/<id>/ticket.md from formats.md, in the project that
   does the work. Add one board line pointing at the folder.
   Do not paste the charter; helpers load it.
6. Send finished work to the checker agent.
7. Read return.md and verification.md in the ticket folder, not the summary.
   Move the board line.