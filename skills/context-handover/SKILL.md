---
name: context-handover
description: Generate a structured context handoff document when the conversation window is getting long or the user wants to switch sessions. Use when the user says "handoff", "context dump", "save context", "wrap up", "pick this up later", "switching sessions", "running out of context", or any indication they want to preserve the current session's state for a future conversation. Also use proactively if the context window is clearly getting large and there's meaningful undocumented state.
---

# Context Handover

Generate a structured handoff document that captures everything a fresh session needs to continue the current work without losing context. The handoff is the bridge between "what happened in this conversation" and "what the next conversation needs to know."

## Why this exists

Context windows end. When they do, undocumented decisions, workflow constraints discovered mid-session, and the current state of in-progress work vanish. The next session starts cold and either re-derives everything (wasting time) or misses it entirely (introducing bugs). This skill captures what matters and produces a file the next session reads on startup.

## When to trigger

- User says anything about wrapping up, handing off, saving context, switching sessions
- User explicitly invokes `/context-handover`
- Context window is clearly getting large and there's meaningful state not captured in commits or planning docs

## What the handoff captures

The handoff is organized into sections. Not every section applies every time — include only what's relevant.

### 1. Session identity

- Date range (session start → now)
- Branch name
- Starting commit hash (HEAD when session began — from the git status snapshot in the conversation)
- Current commit hash

### 2. What happened (chronological)

A concise chronological list of what was done in this session. Not a git log — a narrative of the work: what was attempted, what succeeded, what was abandoned and why, what was deferred. Focus on decisions and outcomes, not individual edits.

### 3. Git diff summary

Run `git log --oneline {starting_commit}..HEAD` to show commits made during this session. If there are uncommitted changes, note them. The next session should know what landed and what's still in the working tree.

### 4. Undocumented decisions

Decisions made in conversation that aren't captured in commits, planning docs, or CLAUDE.md. These are the most perishable items — if they're not in the handoff, they're gone. Examples:
- "We decided X approach is wrong because Y"
- "We agreed to defer Z until after shadow mode"
- "The executor should NOT do X because we found Y"
- Architecture choices discussed but not yet implemented

### 5. Current state

Where is the work right now? What's the immediate next step? What's blocked? What's in progress? Be specific:
- Which plan/task/phase is active
- What's the executor doing (if applicable)
- What checkpoints are pending
- What's waiting on human review

### 6. Files the next session should read

Ordered list of files the next session should read to get up to speed. Not every file in the project — the specific files that are relevant to the current work. Include line ranges if specific sections matter.

### 7. Active constraints and guards

Workflow constraints that were established or reinforced during this session. These are the behavioral rules the next session needs to follow:
- Anti-gloss check requirements
- "Present before implementing" rules
- Human checkpoint gates
- Adversarial review requirements
- Anything the user corrected Claude on during this session

### 8. Open threads

Questions that were raised but not resolved. Investigations that started but didn't finish. Ideas that were filed as todos but need follow-up context. Things the user said "we'll come back to this" about.

### 9. Resume prompt

A copy-pasteable block the user can give to the next session to bootstrap context. The resume prompt MUST include the anti-gloss guard as a baseline behavioral constraint — every session starts with it, not just sessions where it was explicitly discussed. Structure:

```
Read these files in order before doing anything else:
1. [handoff file path]
2. [any other critical files]

Baseline execution discipline (every session, non-negotiable):
- Anti-gloss check: after writing or rewriting any file, stop and verify — what is source-derived vs inferred vs invented? Did you gloss over any contract, field list, or gate? Did you leave anything load-bearing to discretion? Fix before proceeding.
- Present before implementing: show your plan/findings/changes and get explicit approval before writing code or editing files
- Cross-reference claims against source: verify via git, file reads, or grep — do not present conversation memory or reasoning as confirmed fact

Session constraints:
- [key behavioral rules from section 7]
- [any session-specific guards]

Current state: [one sentence from section 5]
Next step: [the immediate action]
```

## How to generate

### Step 1: Gather source of truth FIRST

Before writing anything from memory of the conversation, establish what actually happened:

1. **Get the git state.** Run:
   - `git log --oneline {starting_commit}..HEAD` for session commits
   - `git status` for uncommitted changes
   - `git diff --stat` for uncommitted change summary

2. **Check planning state.** If `.planning/` exists:
   - Read STATE.md for current position
   - Read ROADMAP.md for phase status
   - Check for any active phase directories with in-progress plans

3. **Check for active threads.** If `.planning/threads/` exists, list any threads that were referenced or created during this session.

4. **Check for filed todos.** If `.planning/todos/pending/` exists, list any todos created during this session.

5. **Read key files that were modified.** For any file the commits touched, read the current state — don't describe it from conversation memory.

### Step 2: Cross-reference conversation against source

This is the critical guard. The conversation window is unreliable — it contains discussion, abandoned ideas, things that were proposed but rejected, and things that were discussed here but executed in a different window.

For every claim you're about to write in the handoff:

- **"We did X"** — verify X appears in a commit, a file on disk, or `git status`. If it was discussed but not committed, say "discussed but not landed" or "executed in another window — verify via commit."
- **"We decided Y"** — check if Y is captured in a planning doc, CONTEXT.md update, or thread file. If it's only in the conversation, flag it as "undocumented decision" (section 4) and be explicit that the source is conversation-only.
- **"Tool Z was fixed"** — check the actual tool code or the commit that claims to fix it. Don't say "Tool 6 matchup cascade was fixed" because you remember discussing it — verify `657bc1d` or whatever commit actually landed the change.
- **"The executor did W"** — if work happened in a different CLI window, you only know about it through commits and file state. Don't narrate executor work from your conversation memory of giving instructions — check what actually shipped.

The handoff must distinguish:
- **Committed:** landed in git, verifiable
- **On disk uncommitted:** in working tree, visible via `git status`
- **Discussed only:** exists in this conversation but nowhere else — the most perishable state, and the primary reason the handoff exists

Do NOT present discussed-only items as if they're done. That's the same execution bias pattern the project's incident log documents.

### Step 3: Write the handoff

Now write the handoff file to `.planning/references/workflows/handoffs/YYYY-MM-DD-HH-MM-handoff.md`, grounded in what you verified in steps 1-2.

### Step 4: Post-handoff

After writing the handoff, ask the user: "Want to run `/graphify` to update the knowledge graph before closing out?" — only if the project has a `graphify-out/` directory. Don't run it automatically.

## Output format

The handoff file should use this structure:

```markdown
# Session Handoff — {date}

**Branch:** {branch name}
**Commits:** {starting_hash}..{current_hash} ({N} commits)
**Uncommitted:** {yes/no + summary if yes}

## What happened

{chronological narrative}

## Git activity

{commit log}
{uncommitted changes if any}

## Undocumented decisions

{decisions not in commits or docs}

## Current state

{where work stands, next step, blockers}

## Read these files

{ordered list with line ranges where relevant}

## Active constraints

{behavioral rules for next session}

## Open threads

{unresolved questions, deferred investigations}

---

## Resume Prompt

```
{copy-pasteable bootstrap block}
```
```

## What NOT to include

- Full file contents (reference them, don't inline them)
- Git diffs (the commit log is enough — the next session can read diffs if needed)
- Information already captured in committed planning docs (reference the doc, don't duplicate)
- Ephemeral debugging state that won't matter next session
- Task lists (use the task system for that, not the handoff)
