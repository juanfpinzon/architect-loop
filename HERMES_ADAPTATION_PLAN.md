# Hermes Adaptation Plan for architect-loop

## Status
- Fork created: `https://github.com/juanfpinzon/architect-loop`
- Local clone: `~/projects/architect-loop`
- Upstream remote configured
- Codex CLI verified: `codex-cli 0.140.0`
- Upstream validator verified locally: `python3 tests/validate_skills.py`

## Why this fork exists
We are not adopting upstream `architect-loop` as-is.

We are adapting it into a **Hermes-native heavy-dev workflow** that preserves the best upstream ideas:
- frozen acceptance gates
- fresh-context judgment
- isolated builder lanes / worktrees
- mandatory builder disagreement
- architect-run verification

...while fitting our operating system:
- **Linear** = execution truth
- **shared vault** = durable cross-agent memory
- **Notion** = polished research and documentation
- **repo-local handoff files** = slice-local execution artifacts only
- **gh-watchdog** = post-PR adversarial safety net

## Core adaptation principles

### 1. Do not create a second source of truth
Upstream says "the repo is the only memory."

Our version will not do that.

Instead:
- `docs/HANDOFF.md` may exist, but only as a **slice-local execution ledger**
- repo-local artifacts must never replace Linear for task status or shared vault for durable lessons
- Notion remains the home for polished research notes and decision memos when human readability matters

### 2. Keep architect-loop opt-in
This workflow is for **heavy, multi-file, architecture-sensitive changes**.

It is **not** for:
- trivial copy or CSS work
- Linear housekeeping
- quick watchdog follow-up fixes
- routine content edits
- urgent one-line fixes where overhead exceeds value

### 3. Make model roles configurable
Upstream hardcodes Claude Fable as architect and GPT-5.5 Codex as builder.

Our fork should support configurable defaults so the workflow can track our real stack and future model changes.

### 4. Integrate with Hermes, not around Hermes
This fork should complement Hermes orchestration, not compete with it.

That means documenting:
- how Hermes sessions should invoke architect-loop work
- how slice state maps to Linear
- how architect-loop hands finished work to `gh-watchdog`
- when to use architect-loop versus normal Hermes execution

## Execution backlog
Linear project: **Architect Loop**

Initial issues:
- `HER-225` — Define Hermes-native architecture and memory routing
- `HER-226` — Make architect and builder model roles configurable
- `HER-227` — Add Hermes and Linear operating-model integration guidance
- `HER-228` — Add project-scoped bootstrap and pilot guardrails
- `HER-229` — Prepare first pilot on AndyJuan Newsletter or Snowball/Replica

## Recommended implementation order
1. Architecture and memory routing (`HER-225`)
2. Model-role configurability (`HER-226`)
3. Hermes + Linear integration guidance (`HER-227`)
4. Pilot bootstrap + guardrails (`HER-228`)
5. Pilot repo and candidate slice selection (`HER-229`)

## Candidate first pilot
Do **not** pilot first on Marca-IA.

Preferred first pilots:
1. **AndyJuan Newsletter**
2. **Snowball / Replica**

Selection criteria:
- heavy enough to benefit from explicit gates and worktree isolation
- low enough commercial risk to learn safely
- bounded enough to evaluate success or failure clearly

## Immediate next artifacts to add
- A dedicated architecture note for memory-routing and system ownership
- A configuration note for architect / builder / reviewer role mapping
- A pilot checklist covering preflight, canary, rollback, and success criteria
- Optional: README section describing the Hermes-adapted operating model

## Decision for now
This fork is a **design and adaptation project first**.

We will make it ours before installing it into a production repo workflow.
