---
name: wwcd
description: "What Would Codex Do" — implementation rigor checklist. Invoke before finalizing plans, committing migrations, claiming work is complete, or any time output is transitioning from generative (design, brainstorming) to precise (implementation, code changes, contracts). Enforces 11 discipline checks derived from real failures where Claude's completion bias produced confident-wrong plans and half-finished migrations. Use proactively whenever the work involves shared contracts, multi-file changes, format migrations, or anything where "good enough" is the enemy of correct.
---

# /WWCD — What Would Codex Do?

Implementation rigor checklist that forces discipline on Claude's generative output.

## Why this exists

Claude excels at 0-to-1: brainstorming, design, turning ambiguity into structure. Claude fails at 1-to-done: the seams, the edges, the homework. A design that was brilliant on paper becomes broken in implementation because Claude optimizes for completion velocity over contract integrity.

Codex has the opposite profile: slow to start, but treats every file as a node in a graph and every contract as binding. This skill bridges the gap by running Codex's discipline as a checklist against Claude's output.

The core failure mode: Claude reads fragments, infers the rest, presents inferences as facts, defers inconvenient scope as "follow-up," and ships plans that cover 40% of the actual blast radius while claiming completeness.

## When to invoke

Always. Every task. Not just plans and migrations. The checks scale to scope — a 2-file change runs fast, a 15-file migration runs thorough. The trigger is any moment where output shifts from generative to precise:

- Writing or finalizing an implementation plan
- Before committing any change that touches shared contracts
- Before claiming work is complete
- When something feels "good enough"
- After any design/brainstorming session transitions to execution
- When you catch yourself thinking "the rest is follow-up"

## The Eleven Checks

### 1. Dependency Graph Completeness
**Principle:** Graph propagation — a change to any node must propagate to all connected nodes or the graph becomes inconsistent.

A model ID, a type name, a field format — these are not isolated strings. They are nodes with edges to every file that reads, writes, compares, or displays them. Grep is discovery. Reading is verification.

**Check:** For every concept being changed, have you traced every edge? Not every file — every *concept*. Have you read (not grepped) the files you found?

**Failure example:** "I found 6 references to the model ID." But test fixtures, script arguments, artifact filenames, and log format strings also encode the model ID. The actual count was 28.

---

### 2. Contract Symmetry
**Principle:** Producer-consumer coupling — every output has a reader. Changing the producer without updating all consumers creates a silent protocol violation.

**Check:** For every output format change, identify every consumer. Tests that assert against the old format are consumers. Scripts that parse the output are consumers. Dashboards that display it are consumers.

**Failure example:** Updated `_meta.models.spec` to gateway format in the main path but left the gambling guard and pipeline-synthesized clarification paths emitting old-format IDs. Three emission paths, only one updated.

---

### 3. Scope as Measurement
**Principle:** Estimation theory — an estimate made before measurement is a guess. An estimate made after measurement is a forecast.

Plans written before reading the codebase are guesses. Plans written after reading every relevant file end-to-end are forecasts. If the scope grew after reading, the initial scope was a guess.

**Check:** Did you read every file that will change — end to end, not fragments? Can you list every change with a file path and line number? Did you also read the test files?

**Failure example:** Declared "4 files, 12 changes" based on grep results. After reading the files: 9 files, 28 changes, 6 test files with stale fixtures.

---

### 4. Absence as Finding
**Principle:** Quality engineering — the absence of a check is itself a defect.

Missing validation, missing tests, dead code paths, unused parameters — these are evidence of incomplete prior work or broken assumptions. They are findings, not background noise.

**Check:** Did you find anything that should exist but doesn't? Missing error handling? Dead POST fields that no route reads? Tests that don't assert what they claim to?

**Failure example:** The bench script POSTs `shadowMode` and `shadowOutputDir` but the chat route ignores both fields. Discovered during read, dismissed as "not in scope." It's a latent bug.

---

### 5. Defensive Interface Design
**Principle:** Strict internal contracts — reject invalid inputs loudly and immediately. The next developer doesn't know your migration happened.

Postel's Law ("be liberal in what you accept") creates maintenance nightmares at internal boundaries. Invalid inputs should throw with context, not silently pass through.

**Check:** Does every input boundary validate and reject? Does invalid input throw with context (what failed, what was expected, where it came from)? Or does it silently pass through and cause a confusing error three layers deeper?

**Failure example:** `if (SPEC_MODEL_ENV) return SPEC_MODEL_ENV` silently accepts legacy bare IDs like `gpt-5.4-mini` that will 404 at the gateway. vs. `assertAllowedModelId(override, 'SPEC_MODEL')` which throws immediately with the source of the invalid value.

---

### 6. Isolation Before Composition
**Principle:** Modular design — a module should be a leaf node when possible. When you need shared logic, create a new leaf module rather than adding exports to a high-traffic module.

**Check:** Before adding exports to an existing file, trace its import graph. Will any importer create a cycle? If A imports B and B imports A, extract the shared logic into C.

**Failure example:** Putting pricing and model resolution in `pipeline.ts` when `shadow-mode.ts` needs the same logic — but `pipeline.ts` already imports from `shadow-mode.ts`. Circular import. Solution: `model-catalog.ts` as a leaf module.

---

### 7. Tests as Specifications
**Principle:** Test-driven design — tests written before implementation define "correct." Tests written after implementation only verify what you already built.

**Check:** Are tests a task in the plan with specific assertions enumerated? Or a footnote that says "we should probably add tests"? Are failure modes covered, not just the happy path?

**Failure example:** Plan had zero test tasks. Mentioned "could add resolveSpecModel tests" in conversation. Codex's plan: 3 new test files, 3 existing test file updates, specific assertions listed, tests as their own steps.

---

### 8. Prior Work Integrity
**Principle:** Contract preservation — every committed artifact represents a decision. Test fixtures with hardcoded values are decisions about what "correct" looks like.

When you change the contract those values encode, the old fixtures now assert the wrong thing — and they pass silently because the code no longer produces what they check.

**Check:** After any format/schema/ID change, grep the test fixtures for the old format. Every match is a test that now verifies the wrong contract.

**Failure example:** Changed model IDs to `anthropic/claude-sonnet-4.6` but existing test fixtures still assert against `claude-sonnet-4-6`. Tests pass. Tests verify nothing.

---

### 9. Deference Budget
**Principle:** Context decay — the current context, where you just read every file and understand every edge, is the BEST context. Future you starts cold. Deferring work you can see right now is paying interest on a loan you didn't need to take.

**Check:** For every item marked "follow-up" or "not in this PR" — is it genuinely independent (different feature, different system), or are you deferring it because the plan is already long? If it uses the same concept you're changing, it belongs in this PR.

**Failure example:** "Scripts can be updated in a follow-up." Those scripts use the model ID format being changed right now. Deferring them ships a codebase with two incompatible formats and no one remembers to circle back.

---

### 10. Epistemic Honesty
**Principle:** Scientific categorization — observed (from source), inferred (derived from observations + reasoning), hypothesized (plausible but unverified). Mixing these categories without labeling is intellectual dishonesty.

**Check:** Does your output separate what you read from source code, what you inferred from patterns, and what you invented? If you removed the inferences, would the plan still be coherent?

**Failure example:** "The bench script doesn't create providers directly — it hits localhost." (Source-derived.) "So it won't break." (Inferred — did you verify the response format it parses hasn't changed?)

---

### 11. Anti-Gloss Gate
**Principle:** Verification at write-time — after writing or rewriting any artifact, stop and verify before proceeding.

This is not a one-time check. It runs three times:
1. Before writing — what are you about to claim? Is it source-derived?
2. After each edit — did you gloss over any contract, field list, or gate? Did you leave anything load-bearing to discretion?
3. Across the final combined diff — does the total changeset hold together, or did per-file edits create inconsistencies?

**Check:** What is source-derived vs inferred vs invented in what you just wrote? Did you gloss over any contract, field list, or gate? Did you leave anything load-bearing to discretion?

**Failure example:** Wrote a plan with PRICING table keys as `anthropic/claude-sonnet-4-6` (dashes). Actual gateway type definitions use `anthropic/claude-sonnet-4.6` (dots). Would have shipped a plan that produces runtime 404s. Caught only because the anti-gloss check forced verification against `node_modules/@ai-sdk/gateway/src/gateway-language-model-settings.ts`.

## How to run

When invoked, run each check against the current plan, changeset, or claim. For each:

- **Pass** — one sentence explaining what you verified and how
- **Fail** — cite the specific file, line, or concept that fails
- **Unknown** — you haven't read enough to answer. This is a finding, not an excuse. Go read the file.

**Three or more Fail or Unknown = the work is not ready.** Go read the files, update the plan, rerun.

Do not treat this as ceremony. Every check that fails is a bug you're about to ship.
