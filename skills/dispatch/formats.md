One folder per ticket, in the project that does the work:
.planning/tickets/<id>/
  ticket.md         the dispatch (below)
  return.md         done report or scope return
  verification.md   the checker's verdict
The board holds one line per ticket: status and a pointer to the folder.

## Ticket  (ticket.md)
Ticket <id>, from <sender> to <receiver>.
Scope: <what to do, which files or repo>.
Stop condition: if this needs <a new dependency / anything beyond X>, stop and report.
Pass criteria: from <plan file>, not from the builder's summary.
Return: return.md in this folder, on a branch, not pushed.

## Scope return  (return.md)
<id> STOPPED. Hit: <stop condition>. Options: <a>, <b>. Need: <owner decision>.

## Done report  (return.md)
<id> DONE. Pointer: <commit or file>. Checker: <name, not the builder>.
| claim | evidence (file:line or command output) | severity |