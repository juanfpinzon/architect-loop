# Builder dispatch reference

Verified live against Codex CLI 0.139 (June 2026). Facts that correct common
misinformation: the model slug is `gpt-5.5` (not `gpt-5.5-codex` — the
-codex-suffixed line is deprecated); `--search` and `-a/--ask-for-approval`
are **TUI-only flags — `codex exec` rejects both** (exec is non-interactive
by design; the sandbox flag is the only permission control); Goal Mode's only
real subcommands are bare `/goal`, `/goal pause|resume|clear`.

**Preflight (once per environment):** run `codex --version`. Need ≥ 0.133.
On the first dispatch in a new environment, launch ONE canary run and confirm
it starts cleanly before fanning anything out — CLI flags churn between
versions.

## Canonical headless dispatch (architect-driven)

```bash
codex exec -C <repo-root> --sandbox workspace-write \
  -m gpt-5.5 -c model_reasoning_effort="xhigh" \
  --json -o .architect/last-run.md \
  "<BUILDER BLOCK>"
```

- Run in the background (multi-hour runs are normal); read
  `.architect/last-run.md` and the repo state afterwards.
- Pin the model explicitly — automations have been reported silently falling
  back to older models.
- Effort: `xhigh` default (best review-survival for unattended work);
  architect downgrades routine, tightly-specified slices to `"high"`.
- Same-slice follow-up (e.g. answering PHASE 0 disagreements after the human
  rules): `codex exec resume --last "<rulings + proceed>"`. Never resume
  across slices — every slice gets a fresh context.
- Optional: `--output-schema <schema.json>` to force a machine-checkable final
  report.
- Cross-model review gate: `codex review --base <branch>` (or `--uncommitted`),
  with custom focus text appended.
- Add `.architect/` to the repo's `.gitignore`.

## Manual alternative (human-driven)

Paste the builder block into an interactive `codex` session prefixed with
`/goal ` — Codex loops plan→act→test→review against the stopping condition
until done. Use when the human wants to watch or steer the run.

## Builder block template

```
Execute the architect spec below. Operating rules:

PHASE 0 — Before any code: reply with your plan and EVERY disagreement you have
with this spec, with reasons, citing real files in this repo. Silent compliance
is a failure. Silent scope additions are a failure. If you have no
disagreements, state what you checked before concluding the spec is sound.
Verify the named APIs/formats/versions against the live dependencies before
planning around them.

PHASE 1 — Freeze shared contracts (schemas/interfaces) in docs/ first. After
freeze they are read-only for everyone including you. The files under
docs/gates/ are read-only at all times — editing them fails the slice
regardless of results.

PHASE 2 — Spawn at most 3-4 lane agents, each on a disjoint file set (modules
that don't import each other), plus ONE reviewer agent that never writes
feature code: it checks every lane against the spec + tests + frozen docs and
returns APPROVE or a numbered defect list. Nothing merges without APPROVE.
No placeholder implementations — search the codebase before implementing;
full implementations only. Commit per lane with descriptive messages; push;
then update docs/HANDOFF.md with RAW results only — tables, numbers, commit
SHAs, test output — no interpretation, no "promising". Every status claim must
be backed by a command result from this run. Verdicts belong to the architect
and the human. Persist until the slice is fully handled end-to-end; do not
stop at analysis or partial fixes.

=== OBJECTIVE (and why) ===
...

=== OUTPUT FORMAT ===
...

=== TOOL GUIDANCE (verification commands; verify-against-reality list) ===
...

=== BOUNDARIES (may touch / must not touch / out of scope) ===
...

=== DISAGREEMENT RULINGS (from last session) ===
...

=== ACCEPTANCE GATES (frozen at docs/gates/<slice>.md — read-only) ===
...
```

## Builder-side standing setup (one time per machine/repo)

- `~/.codex/config.toml`: `model = "gpt-5.5"`; optionally
  `[features] multi_agent = true` and lane/reviewer agent definitions under
  `~/.codex/agents/` or `.codex/agents/` (TOML: `name`, `description`,
  `developer_instructions`, optional `model_reasoning_effort`, `sandbox_mode`).
- Repo `AGENTS.md`: exact build/test commands and repo gotchas only — the
  loop's PHASE rules stay in the dispatch block so they version with the skill.
- Subscription quotas are per-5h window + weekly cap; long runs draw the weekly
  pool. For overnight unattended runs that must not die mid-run, `CODEX_API_KEY`
  per-token billing avoids window exhaustion.
