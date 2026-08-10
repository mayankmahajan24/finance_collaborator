# Part 1 — qualitative read

10 seeds, 5 quant / 5 fundamental, across three buckets. Plans and critiques
generated blind (`PROVENANCE.md`); each seed scored by an independent subagent
against a pre-written gold flaw (`scorecard.md`).

## The headline result contradicts the assignment's premise

The brief says Claude "produces plans that *look* reasonable but often wouldn't
survive a desk review: they overfit the question, ignore regime dependence,
propose validation that leaks, anchor on the management narrative, or miss the
obvious confounder."

That is not what we measured on Opus 5.

| axis | result |
|---|---|
| Critique recall | 9 caught / 1 partial / **0 missed** of 10 |
| Plan quality | 7 of 10 plans **avoided the known flaw unprompted** |
| Critique precision | **2.6 manufactured objections per critique** |
| Right-sizing (dead ideas) | critique proposed shrinking in **3 of 3**; plan in only 1 of 3 |

Every named failure mode in the brief was tested and mostly did not reproduce:

- **Validation that leaks** (S3) — the plan partially leaked; the critique caught
  it precisely: KPI correlation used both to match institutions and to validate
  the match.
- **Anchors on the management narrative** (S9) — the plan *refused the seed's
  frame outright* and pre-committed to the bear null. There was no anchoring left
  to catch.
- **Misses the obvious confounder** (S10, S4) — S10's plan named the
  legal-constraint confound as its own primary hypothesis and got the buyer/COGS
  direction right. S4's plan led with the deconsolidation null rather than
  reaching for strategic intent.
- **Can't tell mechanism from correlation** (S8) — the plan flagged that round
  lots may be the algo-child population and gated the project on proving it in
  week one.

## Where it actually breaks

**1. Precision, not recall.** 26 manufactured objections across 10 critiques.
The critique step reliably finds the real problem *and* several that aren't
problems — S4 and S9 drew 4 apiece. Two representative failures: an S5
objection that GICS cells are degenerate at 9 firms (reached by reading "sector"
as "sub-industry"), and an S6 claim that a mechanical-bias sign is backwards,
asserted unconditionally when it holds only in one case. Both are confidently
argued and wrong. A PM reading these has to adjudicate every finding, which is
exactly the work the tool was supposed to save.

**This is the finding that matters for Part 2.** A discriminator eval built only
on flawed seeds cannot see this failure at all — a model that reflexively
generates five objections per plan scores perfectly on recall. Precision needs
sound plans in the eval set to be measurable, which is why the recall/precision
framing the brief asks for is the right one.

**2. The plan step over-scopes; the critique step is the corrective.** On the
three obviously-flawed seeds, the plan proposed killing or shrinking the idea
only once (S6). The critique did it all three times, and aggressively: S7's plan
built a month of machinery to price a published broker note, and the critique cut
it to half a day. S5's plan said in its own pitfalls section that the signal is
mined, then budgeted eight weeks to confirm it anyway — the critique named that
sequencing inversion directly.

So Claude *knows* the idea is weak while writing the plan and scopes a full
project regardless. The knowledge is present; it doesn't reach the
recommendation. Asking for a critique recovers it.

**3. Self-critique works, which is the more surprising half.** The brief asks
"does it catch its own mistakes?" On this set, yes — consistently, and the
critique is the stronger artifact. The most valuable single output in the run was
S5's critique deriving that the plan's own IR 0.5 target could not clear its own
t>3 hurdle on any subsample: an internal contradiction found by arithmetic, not
by pattern-matching a known pitfall.

## Caveats worth stating in the report

- **The gold flaws are my read, not a market outcome.** "Caught" means the
  critique matched a flaw written in advance by one reviewer. The `your_verdict`
  column in `findings-table.md` is deliberately empty — the assignment calibrates
  Claude against *your* instinct, and that column is where the actual Part 1
  judgment lives.
- **Recall is measured against flaws the plan usually already handled.** Seven of
  ten plans avoided the flaw unprompted, so the critique was often ratifying
  rather than rescuing. That makes the recall number less impressive than it looks
  and the precision number more damning.
- **One model, one effort setting, n=10.** No claim about scaling across model
  size — that is what Part 2's eval is for.
- **Mixed harness.** Six seeds via bare API contexts, four via Claude Code
  subagents after the API credit ran out. Scores straddle both sources with no
  visible gap, but see `PROVENANCE.md`.

## What this implies for Part 2

Design the discriminator eval around **precision under a plausible-looking plan**,
not recall against a broken one. The interesting discriminating item is a pair
where plan A and plan B are both competent and the difference is whether the
critique invents a reason to prefer one. On this evidence, that is where models
separate — and a recall-only eval would score Haiku and Opus identically at
ceiling.
