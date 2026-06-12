# Launcher post (single long post, NOT a thread — link-free main post)

> Post this, attach `stats.png` (or `loop-diagram.png`), and quote/attach
> the X Article. Repo link goes in the FIRST REPLY (X penalizes external
> links 50–90% in the main post). Chart goes in the second reply.

---

Your Fable quota isn't too small. Your harness is wasting it on typing.

Fable is the best judgment model ever shipped — $10/$50 per MTok — and most
coding sessions burn it reading files, running tests, and writing
boilerplate. Work a flat-rate model does just as well.

So I built the harness that splits the job, as two open-source Claude Code
skills. Fable judges. GPT-5.5 Codex types. The repo remembers.

The loop is:

1. Fable specs a one-PR slice + freezes the acceptance gates in the repo — committed BEFORE any code exists
2. A fresh Codex process builds for hours on xhigh (flat-rate ChatGPT plan). It must argue with the spec before writing code — silent compliance = failure
3. Fable runs the gates itself and judges raw numbers. Builder claims are hearsay
4. Not in docs/HANDOFF.md = didn't happen. State lives in the repo, which is why minutes of Fable per block is enough
5. Repeat

Quality goes up, not down: cross-vendor review kills self-grading, and if
the builder so much as edits a gate file, git diff catches it and the slice
auto-fails. Goalpost-moving is structurally impossible.

Measured splits like this cut top-model spend 58–74%. Judgment minutes on
Fable. Typing hours on the flat rate. No API keys.

Full writeup in the article below. Repo in the first reply.

---

# First reply

The repo (MIT, install in 30 seconds):

github.com/DanMcInerney/architect-loop

git clone https://github.com/DanMcInerney/architect-loop
cd architect-loop && ./install.sh
npm i -g @openai/codex@latest

Then /architect-research to explore an idea, /architect to build it.

---

# Second reply (attach effort-chart.png)

The one place this harness spends MORE per token: the builder runs xhigh
(~2.2× cost of high) because the gap isn't test-pass — it's surviving
review. 38% → 69% review-pass, 69% → 88% match-the-human-PR.

Spend aggressively where it compounds. Spend nothing where it doesn't.

---

# 24–48h follow-up (quote-tweet the launcher)

Update: ran the loop on [project] overnight — [N] slices, [N] gates passed,
[$ or quota %] of Fable spent vs [estimate] if Fable had done the typing.

The disagreement protocol is the underrated half: the builder caught [thing]
in my spec before burning a single building token on it.
