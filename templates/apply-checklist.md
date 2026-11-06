# Apply checklist

Ten steps, in order. Tick each one as you finish it.

- [ ] 1. Measure what loads in every session (`/context`). Write 10 real test tasks and run each twice. Start from `templates/tests.md`.
- [ ] 2. Cut your CLAUDE.md to one line per rule, each with a pointer. Plain text pointers, not `@` imports. Start from `templates/charter.md`.
- [ ] 3. Move multi-step procedures into skills: one for sending work out, one for handing over. The kit ships both (`skills/dispatch`, `skills/handover`).
- [ ] 4. Create `.planning/` in each project: BOARD.md, decisions.md, plans/, handoffs/, tickets/<id>/.
- [ ] 5. Write three agent files: worker (cheap model for mechanical jobs), checker (no Edit tool), orchestrator (dispatch skill preloaded, turn cap). Keep your policy file loaded in all of them. Start from `agents/`.
- [ ] 6. Tell the checker to refute only with a file and line or command output, and to ignore style.
- [ ] 7. Turn rules with zero exceptions, or a real failure behind them, into ask or deny rules. Leave judgment rules as prose. Start from `settings/permissions.example.json`.
- [ ] 8. Add a compaction guard and a start-up reminder that points at the latest handover. Make clearing the default.
- [ ] 9. Remove plugins and skills you do not use.
- [ ] 10. Rerun the 10 tasks. Change one thing at a time.

## What to adapt

| Item | Adapt it to |
|---|---|
| The compliance file and org guardrails | Your org's policy file |
| A read-only repo; a deploy-on-main repo; cloud-synced folders | Repos you must not change; rules with a real failure behind them |
| The Director window and project registry | Skip with one project |
| The chain plugin (spawn, chain-guard, the 50% nudge) | Your session tool, or plain settings hooks |
| Session and token counts from one owner's logs | Count yours |
| Model choices and the effort table | Pick per your plan |
| Trigger words such as "wash this session" and "handover" | Pick your own |
