# Hermes Model Roles for architect-loop

## Purpose
This fork should preserve the **role split** from upstream without hardcoding one permanent model pairing.

The load-bearing design decision is:

> **Judgment roles and execution roles stay separate.**
> Specific model names are defaults, not architecture.

HER-226 defines the configuration contract for those roles before we introduce any bootstrap or runtime automation.

## Current problem
Upstream `architect-loop` bakes specific model names into the public description, skill descriptions, and command examples.

That is useful as a worked example, but too rigid for the Hermes fork.

We need three things instead:
1. a stable **role contract**
2. a lightweight **config surface**
3. a clear **Hermes default pairing**

## Hermes default pairing
Until a pilot repo proves otherwise, this fork should assume:

| Role | Default runtime | Default model |
|---|---|---|
| **Architect** | Claude Code | **Claude Opus 4.8** |
| **Builder** | Codex CLI (`codex exec`) | **GPT-5.4** |
| **Reviewer** | Claude Code | **Claude Opus 4.8** |
| **Researcher** | Codex CLI (`codex exec` read-only) | **GPT-5.4** |

### Review note
The **reviewer** default is Opus 4.8 because the Hermes operating model wants the final judgment layer to stay with the architect/reviewer path.

An optional adversarial pass from Codex (`codex review --base ...`) is still encouraged for high-stakes slices, but it is a **secondary review lane**, not the owner of the final verdict.

## Behavioral defaults travel with the roles

Model selection is configurable, but the preferred behavior for each role is
not arbitrary. In the Hermes fork, role defaults also imply a preferred skill
stack and philosophy:

| Role | Preferred behavioral overlay |
|---|---|
| **Architect / Claude I** | Follow the Karpathy-guidelines and use `interview-me` for ambiguity, `idea-refine` for slice-shape pressure testing, and `spec-driven-development` before freezing the slice. Use `planning-and-task-breakdown` for decomposing specs into small verifiable tasks with acceptance criteria and dependency ordering. |
| **Builder / Codex** | Follow the Karpathy-guidelines and use `incremental-implementation`: smallest meaningful change, verify each step, no speculative abstractions. |
| **Reviewer / Claude II** | Follow the Karpathy-guidelines and use `code-review-and-quality` first, then `code-simplification` once correctness is established. |
| **Researcher** | Keep the same think-before-coding discipline, but stay read-only and evidence-gathering only; synthesis and final judgment remain with the architect. |

See `HERMES_SKILL_ALIGNMENT.md` for the durable phase-by-phase contract.

## Hardcoded assumption inventory (pre-adaptation)
These were the places where the repo encoded a fixed model pairing before HER-226 normalized the role-map contract:

| File | Prior hardcoded assumption | Treatment in HER-226 |
|---|---|---|
| `README.md` | Fable plans/reviews, GPT-5.5 implements/researches | Convert to configurable role language + Hermes defaults |
| `DESIGN.md` | intro, roles table, and command examples name specific models as if permanent | Preserve the source-backed rationale, but distinguish evidence from configurable defaults |
| `skills/architect/SKILL.md` | architect and builder named as fixed model choices | Change to configured-role wording with Hermes defaults |
| `skills/architect/dispatch.md` | dispatch examples use `-m gpt-5.5` directly | Keep the verified syntax, but make the model value configurable |
| `skills/architect/research.md` | research examples pin GPT-5.5 | Make the researcher model configurable and document the Hermes default explicitly |
| `skills/architect-research/SKILL.md` | research fan-out examples pin GPT-5.5 | Same treatment as above |
| `install.sh` | mentions Codex builder only, no role-map guidance | Add pointer to the role-map contract and example template |

## Proposed config surface
We do **not** need a runtime config parser yet.

For now, we need a committed contract that future pilot repos can carry and humans/agents can read before dispatching work.

### Proposed file
Use a repo-local committed file named:

`architect-loop.roles.yaml`

Why this path:
- committed and reviewable
- not hidden inside `.architect/`, which is intentionally ephemeral / gitignored
- project-scoped rather than machine-scoped
- simple enough for future bootstrap automation to consume later

### Proposed shape

```yaml
version: 1

architect:
  runtime: claude-code
  model: "Claude Opus 4.8"
  effort: high

builder:
  runtime: codex-exec
  model: gpt-5.4
  reasoning_effort: xhigh

reviewer:
  runtime: claude-code
  model: "Claude Opus 4.8"
  effort: high
  optional_secondary_runtime: codex-review
  optional_secondary_model: gpt-5.4

researcher:
  runtime: codex-exec
  model: gpt-5.4
  reasoning_effort: high

canary:
  codex_min_version: 0.133.0
  require_single_lane_canary: true
```

## Semantics of each role key

### `architect`
The model that owns:
- arbitration
- slice specification
- frozen-gate judgment
- integration decisions
- final verdicts

### `builder`
The model that owns:
- implementation
- lane execution
- raw results reporting
- disagreement surfacing during PHASE 0

### `reviewer`
The model that owns:
- final correctness judgment when a second named reviewer is needed
- adversarial read of high-stakes slices
- requirement/invariant checking beyond raw gate pass

In the Hermes default, the architect and reviewer are the same family by default.
That is acceptable because the repo still preserves fresh-context review and may add an optional Codex adversarial pass.

### `researcher`
The model that owns:
- scout passes
- parallel web-research lanes
- slice-level fact gathering when `/architect` triggers inline research

### `canary`
Compatibility guardrails for Codex dispatch:
- minimum supported Codex CLI version
- requirement to launch one canary before multi-lane fan-out

## What becomes configurable now
Even before runtime automation exists, these things should be treated as configuration, not doctrine:

- architect model name
- builder model name
- reviewer model name
- researcher model name
- builder reasoning effort (`high` vs `xhigh`)
- researcher reasoning effort (`high` default)
- whether a Codex secondary review pass is required for a given repo or slice

## What stays fixed for now
HER-226 is intentionally narrow. These are still architecture constants:

- judgment and execution remain separate roles
- Codex CLI must be compatible with the verified dispatch flow
- one canary run is required before broad fan-out in a new environment
- builder lanes remain worktree-isolated
- final status claims still require architect/reviewer verification

## Codex compatibility and canary rule
The fork should continue to document and assume:

- **Codex CLI minimum:** `>= 0.133.0`
- verified current VM state for Hermes research/testing: `0.140.0`
- one single-lane canary run before multi-lane fan-out in any new environment

The canary remains important even if the model changes, because the fragile part is usually CLI behavior, flags, and environment wiring — not just the model selection.

## Implementation rule for this repo
When a command example needs a model value:
- use a placeholder like `<builder-model>` or `<researcher-model>`
- document the Hermes default immediately next to it
- avoid pretending a future project is permanently tied to today's preferred model

## Non-goals for HER-226
This issue does **not** introduce:
- a runtime parser for `architect-loop.roles.yaml`
- automatic provider switching
- bootstrap scripts that rewrite Claude/Codex config
- full pilot-repo installation flow

Those belong to later adaptation work.

## Deliverables for future pilot repos
Before piloting this fork in another repo, we should be able to do all of the following cleanly:
- copy `architect-loop.roles.example.yaml` to `architect-loop.roles.yaml`
- review/update the default role map in the PR
- substitute the configured model values into dispatch commands and review expectations
- keep the rest of the loop unchanged

## Decision
For the Hermes fork, the architecture is **role-stable but model-configurable**.

- The split between architect, builder, reviewer, and researcher stays.
- The specific model pairings become defaults, not hardcoded doctrine.
- The Hermes default pairing is **Opus 4.8 for architect/reviewer** and **GPT-5.4 for builder/researcher**.
