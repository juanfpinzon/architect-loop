# Hermes Operating Model for architect-loop

## Purpose
This document explains how the Hermes fork of `architect-loop` should be used in **two explicit modes**:

1. **Portable mode** — the upstream architect loop run from Claude Code on a normal laptop, starting from a Linear ticket and ending at a GitHub PR
2. **Hermes-native mode** — the same core loop, but Hermes may proactively source the Linear ticket and operate inside the broader Linear / vault / Notion / gh-watchdog stack

This split is load-bearing.

The fork should remain **usable like the original repo from Claude Code on a laptop**.
The Hermes stack adds operating discipline and surrounding systems, but it must not become a runtime dependency for the core workflow.

## The two modes

| Dimension | Portable mode | Hermes-native mode |
|---|---|---|
| Primary user | Solo developer on laptop | Hermes working inside Juan's operating stack |
| Required tools | Claude Code, Codex CLI, git, Linear, GitHub | Claude Code / Hermes, Codex CLI, git, Linear, GitHub, shared vault, Notion as needed |
| Install target | `~/.claude/skills` or `./.claude/skills` | Same skill install path; Hermes overlays sit outside the repo |
| How work starts | human points Claude / Hermes to a **Linear ticket** | human or Hermes selects a **Linear ticket** |
| Source of task truth | **Linear ticket** | **Linear ticket** |
| Durable cross-session memory | repo files, PR history, Linear comments | **shared vault** |
| Human-facing synthesis | PR + Linear update | **Notion** when needed |
| Review safety net | **GitHub + gh-watchdog** | **GitHub + gh-watchdog** |
| Runtime dependency on Hermes | **none** for the loop itself | Hermes is the operating surface and may proactively orchestrate |

## Portable mode

Portable mode is the **minimum supported use case** for this fork.

If you clone the repo to a laptop, install the skills, and sign into Claude Code + Codex, the workflow should still work.
The important correction is that **portable does not mean disconnected from Linear or watchdog**.
It means the loop can run from Claude Code on a laptop without requiring Hermes-native automation around it.

### Minimum setup
1. Clone the fork.
2. Run `./install.sh` or `./install.sh --project`.
3. Install Codex CLI and sign in.
4. Optionally copy `architect-loop.roles.example.yaml` to `architect-loop.roles.yaml` and set your preferred models.
5. Start from a **Linear ticket** that already contains the slice details.
6. Use `/architect` or `/architect-research` from Claude Code.

### What portable mode assumes
- the repo is still the working execution surface
- the work starts from a **Linear ticket**, even when the loop is run from a laptop
- the human points Claude / Hermes to that ticket directly rather than relying on proactive Hermes orchestration
- **Claude Opus 4.8** is the default architect/reviewer for slice design, frozen gates, gate runs, judgment, PR writeup, and writing back to Linear
- **GPT-5.4** is the default builder for implementation lanes
- after the PR is raised, **gh-watchdog** performs the final review pass
- shared vault and Notion are optional surroundings, not required runtime dependencies

### Portable mode rule
Portable means the loop is runnable from Claude Code on a laptop.
It does **not** mean Linear disappears, PR review disappears, or gh-watchdog disappears.
The required difference is only that portable mode does not rely on Hermes-native proactive orchestration or broader memory-promotion behavior.

## Hermes-native mode

Hermes-native mode keeps the same architect/builder loop and the same default model pairing, but layers it into Juan's broader operating stack.

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
   - either the human points Hermes to the ticket, or Hermes identifies the ticket proactively
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
   - Claude / architect judges and dispatches
   - Codex builders execute in isolated contexts with the configured builder model
   - repo files capture the slice-local evidence

5. **Open the PR and write back to Linear**
   - GitHub becomes the review surface
   - the PR should be well documented
   - the architect path writes the completed work back to the Linear ticket

6. **Let gh-watchdog review downstream**
   - watchdog acts after the PR exists and performs the final review pass
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
2. **That same laptop-driven loop can still start from a Linear ticket and end at a watchdog-reviewed PR.**
3. **Hermes can layer its broader operating systems on top without changing the core runtime surface.**

If a future change breaks condition 1 or 2, it should be treated as a regression.

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
