# Hermes Architecture Contract for architect-loop

## Purpose
This fork adapts `architect-loop` to the Hermes operating model.

This document applies to the **Hermes-native mode** described in
`HERMES_OPERATING_MODEL.md`. Portable laptop use can ignore most of the
owner-system rules here, but still inherits the shared Linear-ticket and
GitHub-PR boundary described in that operating-model doc.

Upstream assumes **the repo is the only memory**. That is not true in our stack.

Our rule is simpler and safer:

> **Every artifact gets one owner system.**
> Other systems may cache, summarize, or point to it, but they do not replace it.

This contract prevents `architect-loop` from becoming a second PM layer or a second memory system.

## Owner systems

| System | Primary role | What it owns | What it does **not** own |
|---|---|---|---|
| **Linear** | Execution truth | issue status, backlog order, current owner, project state, decision-needed flags, next actionable work | slice-local raw evidence, long-form research, repo implementation details |
| **Shared vault** | Durable cross-agent memory | durable lessons, reusable workflows, stable conventions, cross-repo learnings | per-slice task status, ephemeral lane output, polished human docs |
| **Notion** | Polished human-facing documentation | research notes, decision memos, synthesized evaluations, stakeholder-readable summaries | live execution state, raw builder telemetry, branch-level implementation progress |
| **Repo** | Slice-local execution evidence | frozen gates, lane reports, PRD/spec files, branch-local architecture notes, implementation history | cross-project task tracking, durable memory, business priority |
| **GitHub + gh-watchdog** | Code review and adversarial safety net | PRs, review comments, post-PR safety findings, merge-time verification feedback | product planning truth, durable memory, research canon |

## Operating rule: one owner per artifact

If two systems appear to disagree, trust the owner system.

Examples:
- If `docs/HANDOFF.md` says a slice is active but the Linear issue is Done, **Linear wins** for status.
- If a repo note says a workflow is preferred but the shared vault records a newer durable convention, **shared vault wins** for reusable practice.
- If a README summary and a Notion research memo diverge, **Notion wins** for the human-facing research record unless the repo explicitly superseded it.

## Artifact map

| Artifact / question | Owner system | Allowed mirrors / pointers | Notes |
|---|---|---|---|
| What should we work on next? | **Linear** | repo links, PR links, Notion references | Priority and sequencing live in Linear |
| Who owns the work right now? | **Linear** | GitHub assignee, repo note pointer | Linear is the execution source of truth |
| What is the accepted scope of this slice? | **Repo** | Linear issue description may summarize | The detailed slice contract lives with the code |
| What exact gates were frozen before builder work began? | **Repo** | Linear comment may link to gate file | Gate text must be read from the repo, not restated loosely elsewhere |
| What happened inside each builder lane? | **Repo** | Linear comment may summarize outcome | Raw evidence stays repo-local |
| What durable workflow lesson should future agents reuse? | **Shared vault** | Hermes memory, repo note, Notion memo | Only promote lessons that remain useful beyond this slice |
| What research or evaluation should a human read later? | **Notion** | README or Linear may link | Notion is for polished synthesis, not live ops state |
| What review findings blocked merge or triggered follow-up? | **GitHub + gh-watchdog** | Linear issue/comment may capture action item | Watchdog findings can create work, but they do not replace Linear planning |

## Allowed role of `docs/HANDOFF.md`

`docs/HANDOFF.md` is allowed in this fork, but only with a narrower role than upstream.

### It **is allowed** to be
- a **slice-local execution ledger**
- a short table of contents for active architect-loop work
- a pointer map to gate files, lane reports, PRD/spec docs, and freeze commits
- a compact record of open disagreements that must be resolved before the next work block
- a repo-scoped continuation aid for the next architect session

### It is **not allowed** to be
- the source of truth for backlog or priority
- the source of truth for issue ownership or status
- a durable cross-project memory file
- a place for polished research writeups
- a substitute for Linear comments, project state, or vault learnings
- a place to store secrets, credentials, or sensitive business notes

### Required content shape
`docs/HANDOFF.md` should stay compact and operational. It should include only:
- active slice name(s)
- freeze commit SHA(s)
- links/paths to `docs/gates/`, `docs/lanes/`, and PRD/spec files
- architect verdict from the last completed judgment pass
- open disagreements requiring the next ruling
- exact next-step commands or branch references when useful

### Content that must stay out
Do **not** put these in `docs/HANDOFF.md`:
- project roadmap or backlog ordering
- business rationale already owned by Linear or Notion
- durable lessons that belong in the shared vault
- duplicate copies of full gate text when the gate file already exists
- verbose builder prose when the lane report already contains the evidence

### Retention rule
`docs/HANDOFF.md` is **ephemeral and prunable**.

When a slice is complete:
- keep only the minimum pointer trail needed to understand what happened
- archive detail in the specific gate / lane / PRD files or git history
- remove stale operational clutter so the next architect session can parse it quickly

## How Linear interacts with architect-loop

Linear remains the execution control plane.

### Linear owns
- whether a slice should exist at all
- priority among slices
- current state (`Backlog`, `In Progress`, `Done`, `Duplicate`)
- assignment / delegation
- decision requests and blockers that matter outside the repo

### The repo owns
- how the current approved slice is executed
- what gates were frozen
- what evidence the builders produced
- what exact implementation constraints applied

### Practical rule
A Linear issue may summarize a slice, but it should link out to repo artifacts rather than copying them wholesale.

## How Notion interacts with architect-loop

Notion is the home for **polished synthesis**.

Use Notion when the output is meant to be read later by a human as:
- research
- evaluation
- recommendation memo
- design summary
- cross-project comparison

Do **not** use Notion for:
- live builder progress
- frozen gates
- lane-by-lane evidence
- ephemeral execution state

## How the shared vault interacts with architect-loop

The shared vault is where we promote **durable learnings** discovered while using this fork.

Promote to the shared vault only when the lesson is likely to matter again, such as:
- a stable workflow improvement
- a reusable caution about model behavior
- a recurring integration rule
- a cross-repo convention

Do **not** promote:
- slice-specific status
- one-off implementation outcomes
- temporary branch details
- raw lane telemetry

## How gh-watchdog interacts with architect-loop

`gh-watchdog` remains downstream of implementation, not upstream of planning.

Its role is:
- review PRs or merged changes adversarially
- flag correctness and integration problems that escaped the slice
- create or surface follow-up work when needed

It does **not** replace:
- architect judgment before dispatch
- Linear project planning
- shared vault memory
- Notion research synthesis

### Safety rule
If watchdog identifies follow-up work, the actionable record should land in **Linear**. Watchdog output may link to GitHub evidence, but the new work item belongs in the execution system.

## Recommended repo-local file policy

Inside the repo, use files with different lifetimes on purpose:

| File type | Lifetime | Role |
|---|---|---|
| `docs/gates/*` | frozen per slice | acceptance contract before execution |
| `docs/lanes/*` | per slice | raw evidence from builder lanes |
| `docs/prd/*` | medium-lived | slice or feature specification |
| `docs/HANDOFF.md` | short-lived | pointer map for active/just-finished work |
| top-level adaptation docs | long-lived | architecture contract and fork-specific operating model |

## Decision
For the Hermes fork of `architect-loop`:

1. **Linear is the execution truth.**
2. **The shared vault is durable cross-agent memory.**
3. **Notion is polished human-facing documentation.**
4. **Repo-local architect-loop files are slice-local execution artifacts only.**
5. **gh-watchdog is the post-implementation safety net, not a planning system.**

This is the load-bearing rule set for adapting `architect-loop` without letting it sprawl into a parallel operating system.
