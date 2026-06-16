# Hermes Operating Model for architect-loop

## Purpose
This document explains how the Hermes fork of `architect-loop` should be used in **two explicit modes**:

1. **Portable mode** — plain Claude Code + Codex on a normal laptop
2. **Hermes-native mode** — the same core loop plus Linear, shared vault, Notion, and gh-watchdog

This split is load-bearing.

The fork should remain **usable like the original repo from Claude Code on a laptop**.
The Hermes stack adds operating discipline and surrounding systems, but it must not become a runtime dependency for the core workflow.

## The two modes

| Dimension | Portable mode | Hermes-native mode |
|---|---|---|
| Primary user | Solo developer on laptop | Hermes working inside Juan's operating stack |
| Required tools | Claude Code, Codex CLI, git | Claude Code / Hermes, Codex CLI, git, Linear, GitHub, shared vault, Notion as needed |
| Install target | `~/.claude/skills` or `./.claude/skills` | Same skill install path; Hermes overlays sit outside the repo |
| Source of task truth | repo + human | **Linear** |
| Durable cross-session memory | repo files / git history | **shared vault** |
| Human-facing synthesis | ad hoc notes | **Notion** |
| Review safety net | human review / normal PR review | **GitHub + gh-watchdog** |
| Runtime dependency on Hermes | **none** | optional operating layer |

## Portable mode

Portable mode is the **minimum supported use case** for this fork.

If you clone the repo to a laptop, install the skills, and sign into Claude Code + Codex, the workflow should still work.

### Minimum setup
1. Clone the fork.
2. Run `./install.sh` or `./install.sh --project`.
3. Install Codex CLI and sign in.
4. Optionally copy `architect-loop.roles.example.yaml` to `architect-loop.roles.yaml` and set your preferred models.
5. Use `/architect` or `/architect-research` from Claude Code.

### What portable mode assumes
- the repo is the working execution surface
- the human decides scope and priority directly
- the developer may or may not use project-management tooling outside the repo
- no Linear, vault, or watchdog integration is required

### Portable mode rule
If a workflow step depends on Hermes infrastructure, it is **not required** for portable mode unless the docs explicitly mark it as optional.

## Hermes-native mode

Hermes-native mode keeps the same architect/builder loop but layers it into Juan's broader operating stack.

### Additional owner systems
- **Linear** — execution truth
- **shared vault** — durable cross-agent memory
- **Notion** — polished human-facing synthesis
- **GitHub + gh-watchdog** — downstream adversarial review and merge-time safety

### Important boundary
These systems are **owner systems around the loop**, not replacements for the loop.

- architect-loop still owns slice-local execution evidence
- Hermes does **not** replace the repo handoff, frozen gates, or lane evidence
- Linear does **not** replace gate files or lane reports
- gh-watchdog does **not** replace architect planning or judgment

## Hermes invocation pattern

When Hermes uses architect-loop for heavy development work, the recommended sequence is:

1. **Choose the work in Linear**
   - confirm the slice deserves architect-loop rather than a normal inline coding session
   - ensure the issue description is good enough to support a scoped slice

2. **Move the issue to In Progress**
   - use Linear as the execution truth
   - branch from the current repo trunk

3. **Spec the slice in the repo**
   - create or update the PRD/spec
   - freeze gates in repo files before builder work starts
   - keep execution evidence in repo-local artifacts

4. **Run architect-loop**
   - architect judges and dispatches
   - builders or researchers execute in isolated contexts
   - repo files capture the slice-local evidence

5. **Open the PR**
   - GitHub becomes the review surface
   - Linear may link the PR, but should not duplicate the execution record

6. **Let gh-watchdog review downstream**
   - watchdog acts on the PR and surfaces adversarial review findings
   - follow-up work lands back in Linear if anything new is required

7. **Close the loop**
   - merge or finish the change
   - mark the Linear issue Done
   - promote only durable learnings to the shared vault
   - use Notion only if a polished human-facing writeup is needed

## Linear execution flow

The Hermes fork should describe Linear flow in a way that works even if a team does **not** add a dedicated `In Review` state.

| Phase | Linear | Repo | GitHub / watchdog |
|---|---|---|---|
| Candidate work | Backlog | no active slice yet | none |
| Active slice | In Progress | spec, PRD, gates, handoff, lane evidence | optional branch only |
| PR open / under review | usually still In Progress, with PR link/comment | slice artifacts remain the detailed record | PR review + gh-watchdog findings |
| Approved and merged | Done | repo history becomes lasting implementation record | PR closed / merged |
| Rejected / superseded | Duplicate or follow-up issue | old slice may remain as historical evidence | PR comments may explain why |

### Practical rule
Use **Linear states for execution control** and **GitHub for review**.
Do not force Linear to become a second PR review system unless the team explicitly wants that.

If a workspace later adds an `In Review` state, it is an optional overlay — not a requirement for this fork.

## Handoff from architect-loop to gh-watchdog

The handoff is simple:

- architect-loop gets the slice to branch / PR form
- GitHub hosts the review surface
- gh-watchdog acts after the PR exists
- any actionable fallout becomes Linear work again

### Safety rule
Watchdog findings may create follow-up tasks, but they do **not** replace:
- architect judgment
- repo-local gates
- Linear planning truth
- shared-vault memory promotion rules

## When **not** to use architect-loop

Do **not** use architect-loop when:
- the task is trivial, single-file, or obviously smaller than a slice
- the work is a quick hotfix where setup overhead dominates
- the problem is still too ambiguous to spec cleanly
- there is no meaningful gate you can freeze before builder work
- the task mostly needs human taste, stakeholder feedback, or product clarification
- a normal Claude Code or Hermes session would clearly be faster and safer

## Load-bearing portability guarantee

This fork is successful only if both of these remain true:

1. **A laptop user can install and run it with Claude Code + Codex alone.**
2. **Hermes can layer its operating systems on top without changing the runtime surface.**

If a future change breaks condition 1, it should be treated as a regression.

## Relationship to other docs

- `README.md` — concise entry point and install/use summary
- `HERMES_ARCHITECTURE.md` — owner-system and memory-routing contract for Hermes-native mode
- `HERMES_MODEL_ROLES.md` — role map and model configuration surface
- `architect-loop.roles.example.yaml` — project-level role-map template

## Non-goals

This document does **not** define:
- first-pilot bootstrap steps
- canary / rollback checklist details
- project-specific install recommendations for the first trial repo

Those belong to **HER-228**.
