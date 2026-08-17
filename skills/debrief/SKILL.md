---
name: debrief
description: Produce a plain-language stakeholder debrief of work just completed or reviewed in a session — plans, executed phases, test results, code or plan reviews, spikes, incidents, migrations. Use whenever the user asks to "explain what just happened," "summarize this back to me in plain terms," "explain like I'm a PM / not in the weeds," "what does this mean for the project," "debrief me," or wants to relay progress to a manager, client, or teammate who wasn't watching. Also use when the user asks where work is strong or weak, whether risks are fixable, or what something accomplishes toward the larger goal — even if they don't say "debrief."
---

# Debrief — explain the work to someone who wasn't in the weeds

The reader is smart but wasn't watching. They will make decisions based on what you
write — fund it, ship it, pause it, escalate it — so the brief must be honest enough
to decide from and plain enough to read once. Losing truth is worse than losing polish;
losing the reader is worse than losing detail.

## Before writing

Ground the brief in artifacts, not memory. If the session produced or references files
(plans, review docs, test output, reports), re-check the load-bearing facts against
them — numbers, verdicts, counts, statuses. If the user invoked this cold ("debrief
the review"), find and read the artifact they mean before writing a word. Never pad a
gap with invented certainty: if something is unknown, the brief says when we'll know
("we find out when the full-size run happens"), which is itself useful information.

Identify the audience. Default: a PM — not technical, not naive, needs coherence
without the weeds. If the user names someone else (exec, client, new teammate),
shift altitude but keep every honesty rule below.

## Structure

Use these sections in this order. Rename headers to fit the work; keep the spine.

1. **What this is** — one breath of framing before any detail: what the work was,
   and why it exists in terms of the product or goal. Assume zero prior context.
2. **What was done** — the pieces, in the order they happen(ed), each in one or two
   plain sentences. For staged work, name what each stage proves, not just what it
   contains ("this proves all the plumbing connects before we invest in any piece").
3. **Where it's strong** — with the *reason* it's strong. Evidence, not adjectives:
   "the storage choice came from a head-to-head experiment with real measurements,"
   not "the storage layer is robust."
4. **Where it's weak — and whether that's a problem.** For every weakness, answer
   two questions explicitly:
   - **Observable?** If this goes wrong, will we see it (a measurement, a test, an
     audit trail) — or does it fail silently? Silent failures are worth more alarm.
   - **Fixable?** Is there a named fallback, a cheap edit, a contained blast radius —
     or does fixing it mean rework?
   And classify each one honestly: a *deliberate trade-off* someone chose and
   documented, an *open bet* that only running it will settle, or a *gap nobody
   noticed*. These deserve different reactions from the reader, so label them.
5. **What this buys us** — two horizons: what works *now* that didn't before
   (interim value), and what it *unlocks* toward the larger goal (which later work
   was waiting on this).
6. **Status and what's next** — where things stand and, if the reader owns a
   decision or action, name it plainly. Don't bury the ask.

For length: about a page as the default, scaling with the density of the input — a
twenty-finding review earns more room than a green test run, but every section must
still earn its place. Brevity comes from dropping details that don't change what
the reader thinks or does next — never from compressing prose into fragments,
abbreviations, or arrow chains.

## Translation rules

- **Consequence language, not mechanism language.** Say what a thing *does to the
  project*, not how it works: "a setting that would quietly drop data under load,"
  not the API name and its buffer policy. The mechanism goes in only when the
  consequence is unintelligible without it.
- **No codenames.** Internal decision IDs, ticket numbers, requirement codes, agent
  names, branch names — translate or drop. If an identifier will reach the reader's
  ears later anyway, introduce it once in plain words ("the storage decision —
  recorded as decision D-01 — …") and then stop using it.
- **Numbers survive only if they carry meaning** the reader can use ("a 2 GB file,"
  "cut memory from ~840 MB to flat"). Round them. Every surviving technical term
  gets an in-line gloss the first time.
- **Attribute claims.** A reviewer's finding is *their claim*, not established fact:
  "the external reviewer argues X — if right, Y fails." A measurement is a fact. A
  plan's promise is a promise. The reader must be able to tell which is which.
- **Never launder a weakness into a strength.** "Deliberately deferred" is only
  honest if someone actually deliberated. If a thing failed, say it failed and what
  happens next. If discipline was relaxed, say so and whether it was a documented
  choice. The fastest way to lose a stakeholder's trust is a brief that reads well
  the day before a surprise.

## Per-artifact notes

- **Plans:** the weaknesses section is about risk — which assumptions are bets,
  where the plan could be wrong, what's front-loaded to learn early vs deferred.
- **Executed work:** distinguish *verified* (tests ran, output checked) from
  *claimed* (the implementer says so). Say which is which.
- **Test results:** what the passing tests actually cover, what they don't, and
  what a pass therefore does and doesn't prove.
- **Reviews:** findings are the reviewer's position. Note where independent checks
  agreed, disagreed, or went silent — disagreement between reviewers is itself a
  finding worth surfacing.
- **Spikes / experiments:** lead with the verdict and what it settles; the whole
  point of a spike is the decision it enables.
- **Incidents / debugging:** what broke, who felt it, root cause in consequence
  terms, and what now prevents a repeat.

## Example of a weakness entry done right

> **The one real bet: memory on the full-size file.** The strategy for reading the
> 2 GB export is arithmetic-backed but unproven at full scale until we run it.
> **Observable:** yes — the plan runs the real file and produces a measurement
> report; there's no way to fake this. **Fixable:** yes — a named fallback approach
> is already scoped if the numbers disappoint. This is the phase's designed risk,
> and it's front-loaded so we learn early.

Three sentences, both verdicts explicit, classified as a designed risk. That is the
target density for every weakness.
