# Hermes Skill Alignment for architect-loop

## Purpose

This fork keeps upstream `architect-loop`'s separation of judgment and execution,
but it now adopts two explicit overlays as the preferred thinking model for the
Hermes fork:

1. **Karpathy-guidelines** as the loop-wide philosophy
2. **agent-skills** as the preferred phase-specific workflow stack

This is a **behavioral contract**, not a new runtime dependency.
The loop must still run on a normal laptop with Claude Code + Codex alone.
If the external skills are installed, load them explicitly. If they are not,
the fork's prompts should still mirror the same behavior.

## Loop-wide philosophy: Karpathy-guidelines

Across Claude I, Codex builders, and Claude II, the preferred behavior is:

- **Think before coding**
  - state assumptions explicitly
  - surface confusion instead of guessing
  - push back when the slice is underspecified or unnecessarily complex
- **Simplicity first**
  - smallest viable slice
  - no speculative abstractions
  - no extra flexibility that was not requested
- **Surgical changes**
  - every changed line should trace to the slice
  - no side quests, no adjacent cleanup, no unrelated refactors
- **Goal-driven execution**
  - turn asks into frozen gates
  - express work as verifiable outcomes, not vague intent

## Preferred phase stack

| Phase | Owner | Preferred skills | Expected behavior |
|---|---|---|---|
| **Claude I** | architect / planner | `using-agent-skills`, `interview-me`, `idea-refine`, `spec-driven-development`, `planning-and-task-breakdown` | clarify ticket intent, pressure-test the slice shape, write the reviewable slice contract, decompose it into small verifiable tasks with acceptance criteria and dependency ordering, freeze gates before builder work |
| **Codex builders** | implementation lanes | `incremental-implementation` | work in thin increments, verify each meaningful step, keep changes inside lane boundaries, prefer the simplest working implementation |
| **Claude II** | architect / reviewer | `code-review-and-quality`, `code-simplification` | run the gates, judge the diff against the spec, check readability / architecture / security / performance, then apply a final simplicity lens |

## Claude I contract

Claude I is responsible for **turning a Linear ticket into a frozen builder
contract**.

Preferred sequence:

1. Use `interview-me` when the ticket leaves ambiguity around outcome, user,
   success criteria, constraints, or out-of-scope lines.
2. Use `idea-refine` when the ticket is directionally right but the slice shape,
   decomposition, or tradeoffs still need pressure-testing.
3. Use `spec-driven-development` to produce the actual slice contract:
   objective, boundaries, output format, tool guidance, lane plan, and frozen
   gates.
4. Use `planning-and-task-breakdown` to decompose the spec into small
   verifiable tasks with explicit acceptance criteria and dependency ordering
   before dispatching builder lanes.

Claude I should not skip directly from vague ticket text to builder dispatch.

## Codex builder contract

Codex lanes should behave like disciplined implementers, not improvisers.

Use `incremental-implementation` as the default builder workflow:

- make the smallest meaningful change first
- run the relevant verification after each meaningful increment
- keep the repo buildable within the lane's scope
- avoid speculative abstractions and future-proofing
- stop and report blockers rather than inventing around them

## Claude II contract

Claude II is not just "the gate runner". It is the quality judgment layer.

Preferred sequence:

1. Apply `code-review-and-quality` first:
   - correctness
   - readability
   - architecture
   - security
   - performance
   - verification evidence
2. Apply `code-simplification` second:
   - if the change is correct but heavier than necessary, flag it
   - do not silently bless overbuilt work just because tests passed

Claude II's job is to protect the simplicity bar as well as the correctness bar.

## Portability rule

This alignment must **not** turn the fork into a hard dependency on either
external repository.

Portable-mode rule:

- if the external skills are installed, load them explicitly
- if they are absent, the architect-loop prompts and docs still enforce the same
  behaviors directly
- the workflow remains runnable from Claude Code on a laptop with Linear +
  GitHub + Codex, just like the rest of the Hermes fork contract

## Related files

- `README.md` — concise public summary
- `HERMES_OPERATING_MODEL.md` — portable vs Hermes-native usage flow
- `skills/architect/SKILL.md` — architect behavior contract
- `skills/architect/dispatch.md` — builder dispatch contract
