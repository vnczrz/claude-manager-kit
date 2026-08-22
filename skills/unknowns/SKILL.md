---
name: unknowns
description: Pre-fan-out unknowns pass — run in a manager window BEFORE dispatching agents into any metaworkflow (superpowers, GSD, plan mode, custom fan-out). Surfaces the four quadrants of unknowns, teaches the owner what they're unclear on (domain primer), and routes each unknown to the cheapest resolution pattern before tokens get spent. Use when the user says "unknowns", "preflight", "blind spot pass", "primer", or is about to dispatch work whose instruction hasn't been pressure-tested.
---

# Unknowns — the pre-fan-out pass

A fan-out bets real tokens on the quality of one instruction. This skill is the gate: find
what the instruction doesn't know BEFORE the agents do, while it's still cheap. The goal
state it serves: the owner talks only to the manager; everything else fans out already
carrying the answers.

## Step 0 — the starting point

Establish, by asking if not evident: where is the owner in their thought process (vague
itch / direction chosen / decision made)? What's their experience with this domain and this
codebase (expert / familiar / never touched it)? Work as a thought partner from that
calibration — an expert gets terse checks; a newcomer gets the primer (below) woven in.

## The four quadrants

Walk them in order, against the actual dispatch being prepared. Ground in the repo/docs —
never assess the instruction from memory of it.

1. **Known knowns** — essentially what's in the prompt. *What do I tell the agent I want?*
   Check: is it actually IN the instruction, or in the owner's head?
2. **Known unknowns** — *what haven't I figured out yet, but I'm aware that I haven't?*
   These become: questions to the owner (decide now) or explicitly deferred calls the
   instruction marks as open (never silently delegated).
3. **Unknown knowns** — *what's so obvious I'd never write it down, but would recognize it
   if I saw it?* The agent can't recognize what was never written. These become references
   (attach the real file, mockup, or precedent — real artifacts over prose) and tripwires
   ("if X, STOP and report").
4. **Unknown unknowns** — *what haven't I considered at all? What knowledge am I not aware
   of? Do I know how good something can be?* Actively hunt: what does this dispatch assume
   that nobody verified (map vs territory)? What would an expert in this domain check
   first? What's the ceiling — is the owner aiming low because they don't know what good
   looks like here?

## The primer (when the domain is new to the owner)

If step 0 revealed unfamiliarity (new domain, new framework, first time in this territory),
teach before dispatching: the handful of concepts THIS dispatch touches, plain language,
consequence-first — what each thing does to the project, not how it works inside. Only
what changes a decision the owner is about to make; skip the rest. One analogy, held
consistently. This is how unknown unknowns become known: the owner can't steer what they
can't see.

## Route each unknown to the cheapest resolution

| Unknown found | Cheapest pattern |
|---|---|
| Vague intent, competing directions | **Brainstorm / prototype** — generate 3–4 cheap variants, owner points |
| Ambiguity only the owner can resolve | **Interview** — Claude asks targeted questions, architecture-affecting first |
| "I'd know it if I saw it" | **Reference** — attach the real file/asset/precedent to the instruction |
| Unverified assumption | **Probe** — a small, cheap experiment BEFORE the fan-out bets on it |
| Complex multi-step work | **Implementation plan** — reviewed before code, focused on likely-to-change parts |
| Owner can't evaluate the domain | **Primer** (above), then decide |

During flight: agents keep **implementation notes** (deviations from plan, logged not
improvised). After: **explainer/pitch** for what landed; a **quiz** only if the owner must
be able to defend the change without Claude in the room.

## Output — dispatch readiness

End with a short verdict the owner can act on in one glance:

- **Goes in the instruction:** the knowns, stated; references attached; tripwires named
- **Owner decides now:** the known unknowns that block
- **Probe first:** any assumption cheaper to test than to bet on
- **Primer given / not needed**
- **CLEAR TO FAN OUT** — or what's missing

Every unresolved item is either closed, consciously accepted (owner's word), or carried
into the instruction as an explicit open question. Silent gaps are the failure mode.
