---
name: debug-no-bs
description: Use when debugging any bug whose behavior emerges across multiple files, pipeline stages, or call chains — architecture issues, data flow, temporal logic, state propagation, multi-stage transformations, or any investigation where reasoning from grep, graph views, file names, comments, or partial snippets would produce a plausible-but-wrong answer. Enforces end-to-end source reading before any analysis, conclusion, or recommendation is presented. Use proactively whenever a bug investigation touches more than one file or the trace traverses multiple stages.
---

# debug-no-bs

Debugging discipline that prohibits analysis until the full source trace has been read end-to-end. Designed for the class of bug where plausible-sounding inference from partial reads produces confident-wrong conclusions.

## Why this exists

When a bug crosses file boundaries, the failure mode is not missing information — it is fabricating inferences from navigation tools (grep, graph views, file names, comments) and presenting them as evidence. The pattern: read a snippet, extrapolate what "probably" happens elsewhere, deliver a confident root-cause analysis that is internally consistent but wrong at the stages that were never read.

This skill exists to enforce a single rule: no claims until the code has been read. Source is the authority. Everything else is navigation.

## Primary rule

Do not present any analysis, conclusion, recommendation, hypothesis, root cause, or partial finding until the full set of relevant source files has been read end-to-end.

## Workflow

1. Identify the full set of relevant files. This includes the entrypoint, every function it calls, every helper those functions call, every downstream consumer of the data being traced, and any tests that define the intended behavior.
2. Read each relevant file completely. Not just the matched snippet. Read the function in its surrounding context, the imports that resolve its dependencies, and the exports that define its contract with callers.
3. If a function calls a helper, read the helper. Do not assume helpers behave the way their names suggest.
4. If behavior depends on tests (regression tests, fixture behavior, snapshot contracts), read the relevant tests completely.
5. Only after the full trace is complete may the answer be presented.

The only acceptable early response is a hard blocker: a specific path that cannot be read, and the exact reason it cannot be read.

## Behavior rules

- No interim analysis. If reading is not complete, do not share speculation.
- No "likely," "probably," or architecture guesses before the trace is complete.
- No claims unless directly grounded in code that was actually read.
- No line references unless those lines were read in their surrounding context.
- If reading is not complete, do not answer yet.

## Evidence hierarchy

- **Source code** is the authority. If the code does X, the answer is X, regardless of what comments or docs claim.
- **Tests** are secondary authority for intended behavior. They reveal what the code was meant to do when its behavior is ambiguous.
- **Docs and comments** can clarify intent but cannot override code behavior.
- **Graphs and grep output** are for locating code only. Never evidence.

## Required output

After reading is complete, the analysis must contain every section below. If a section is empty, say so explicitly — do not omit it.

### 1. Full call chain

From the origin (entrypoint, user action, scheduled job, etc.) to every downstream consumer of the data being traced. No stage omitted. A consumer is "downstream" if it reads or mutates the state being analyzed, even transitively.

### 2. Stage table

One row per stage in the chain:

| Stage | Function | file:line | Inputs received | Temporal / state / context fields read | Derives or mutates | Fallback behavior | Silent drift possible? | Exact reason |
|---|---|---|---|---|---|---|---|---|

- **Silent drift possible?** asks: can this stage produce output inconsistent with its input without any signal to downstream consumers? Yes or no.
- **Exact reason** must be grounded in specific code that was read — cite the condition, the fallback branch, the unchecked assumption.

### 3. Confirmed

Only claims directly supported by code read end-to-end. Cite file:line for each claim.

### 4. Inferred

Any interpretation that is not directly proven by code — behavior deduced from structure, intent read from naming, patterns assumed from other parts of the codebase. Flag each item as inferred, not confirmed.

### 5. Unknown

Anything not provable from source or tests that were read. Examples: runtime-only behavior, external service contracts, data-dependent behavior that was not exercised. List these explicitly — do not leave silent gaps.

### 6. Architecture recommendation

A concrete contract for the canonical execution target or source of truth, and how each downstream consumer should use it. Not a list of vague suggestions — a contract specific enough that someone could implement it without further clarification.

## Failure conditions

Any of these invalidate the analysis:

- Answering before reading the relevant files.
- Presenting guesses as confirmed.
- Reasoning from abstractions instead of source.
- Omitting a downstream consumer that touches the traced context.
- Using graph or grep output as proof.

If any of these is caught mid-analysis, stop, read the missing code, and restart the affected section.

## Tone

Direct, technical, restrained. Precision over speed. Verification over plausibility. No reassurance, no hedging vocabulary, no throat-clearing. If the answer is "I don't know yet — still reading X," say that. If the answer is "this behavior is confirmed at file:line," say that. Anything between those two states does not appear in the output.
