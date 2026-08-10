# Part 1 — qualitative read on Claude as research planner and critic

10 seeds, 5 quant / 5 fundamental, across three difficulty buckets. Every plan and
critique was produced by a model that saw only the seed sentence — no bucket
label, no known flaw, no sibling seed (`PROVENANCE.md`). Two independent readings
followed: a human desk verdict per item (`findings-table.md`) and a subagent
scored against a pre-written gold flaw (`scorecard.md`).

---

## The headline

**Claude over-applies generic research methodology and under-applies finance
domain knowledge.** It knows the catalogue of ways research goes wrong — and
recites it whether or not the entries apply. What it misses is almost always an
institutional or mechanical fact about the instrument, the data, or how the
market prices information.

That inverts the brief's framing of the question. The failure is not primarily
"doesn't know how research goes wrong." It is closer to "doesn't know the
finance," and the methodological fluency is what disguises it.

*(An earlier draft of this file argued the opposite, on the automated scoring
alone. The human verdicts overturned it — see "Where the two readings disagree".)*

## Answering the brief's four questions

**Where does it break?** On precision, not recall, and on domain specificity, not
method. The human review found a defect in **10 of 10 critiques** — every one
either missed something a desk would raise or raised something that does not
apply here. Five of ten contained an outright overreach.

**"Doesn't know the finance" or "doesn't know how research goes wrong"?** The
misses and the overreaches split cleanly, and the split is the finding:

| The **misses** are domain facts | The **overreaches** are method boilerplate |
|---|---|
| deposits ≠ active trading (S1) | tertiary confounding variables (S1) |
| sales headcount correlates with visitors (S2) | survivorship bias, 12-name universe (S2) |
| negative→positive earnings break a multiple; revenue-based vs EBITDA-based valuation as a company matures (S6) | a statistical analysis that is not possible (S3) |
| round-lot profitability decays in **minutes**, not days (S8) | minimum event count set far too high (S9) |
| renegotiation flow-through timing to margins (S10) | scope expanded to irrelevant analyses, e.g. scale estimation (S1) |
| the shortage must be diffed against existing disclosure and buy-side consensus (S9) | |

Every left-column item requires knowing how an instrument, a dataset, or a market
actually behaves. Every right-column item is a canonical concern recited without
checking whether it bites. **This is why the two failures are the same failure:**
reaching for the generic checklist is what a model does when the domain-specific
insight is not there.

**On the discretionary ideas — "anchors on the narrative" or "can't tell
mechanism from correlation"?** Neither, mostly. Of the five fundamental seeds,
only S4 was judged *"too anchored."* S9's plan explicitly refused the seed's
management frame and pre-committed to the bear null. The discretionary failures
were instead about **context and implementability** — S8, *"why would an asset
manager care about this?"*; S9, *"underassumes firm's market data tooling."* The
model reasons about mechanism competently and misjudges who it is working for.

**Does it catch its own mistakes when asked to critique?** Partially, and
consistently in one direction: the critique is the stronger artifact. Human
verdicts note *"better simplification"* (S6), *"better scoped"* (S10), *"picked up
on the wrong construct and overengineering"* (S3). On the three obviously-flawed
seeds the plan proposed killing or shrinking the idea once; the critique did it
three times. **Claude knows an idea is weak while writing the plan and scopes a
full project regardless** — S5's plan says in its own pitfalls section that the
signal is mined, then budgets eight weeks to confirm it. The knowledge is present
and does not reach the recommendation.

## Where the two readings disagree

| | Human desk verdict | Subagent vs gold flaw |
|---|---|---|
| Plans acceptable | **4 / 10** | 7 / 10 avoided the flaw |
| Critiques clean | **0 / 10** | 9 / 10 caught the flaw |
| Manufactured objections | 5 / 10 critiques | 26 total (2.6 per critique) |

The gap is the most useful thing in Part 1. A subagent applying a written rubric
scored these plans nearly twice as favourably as a practitioner reading them.
**Rubric-following is not judgment**, and the difference is concentrated exactly
where the rubric could not reach: whether a standard objection applies *here*,
whether the horizon suits the phenomenon, whether the firm could execute it.

Two consequences. First, gold for Part 2 has to be human — a model-generated
standard would have certified six plans the desk rejected. Second, the automated
recall number (9/10) is close to meaningless on its own; the same critiques the
scorer called "caught" were, to a reader, uniformly defective.

## What the numbers are worth

- **Recall is measured against flaws the plan usually already handled.** Seven of
  ten plans avoided the known flaw unprompted, so the critique was often
  ratifying rather than rescuing. That makes 9/10 less impressive than it looks
  and 2.6 manufactured objections more damning.
- **The manufactured objections are not noise.** They are canonical concerns —
  survivorship, sample size, look-ahead, confounders — recited without checking
  applicability. That is a specific, addressable failure, not general sloppiness.
- **One model, one effort setting, n = 1 per call.** No re-rolls, so run-to-run
  variance is unmeasured. Ten items; treat any single-item difference as noise.
- **Gold is one reviewer.** Where Claude disagrees, the honest reading is
  "disagrees with this desk," not "wrong."
- **Mixed harness.** Six seeds via bare API contexts, four via Claude Code
  subagents after the API credit ran out. Measured effect: none detectable
  (`PROVENANCE.md`).

## What this implies for Part 2

**Build the eval around precision under a plausible-looking plan, not recall
against a broken one.** A discriminator eval on flawed plans measures recall
only, and every model scores near-perfect — the ceiling this data already shows.

Three design consequences, all carried into `part2/`:

1. **Both plans in a pair must be competent.** Precision is only measurable when
   some plans are sound; otherwise a model that manufactures five objections
   apiece scores perfectly and its noise is invisible.
2. **Every objection must carry its own justification.** The `why_it_applies_here`
   field exists because of the four verdicts above — a justification that would
   read identically against any plan in the asset class is a misapplied objection,
   and that is far easier to detect than adjudicating the objection itself.
3. **Judge fit, not rigor.** The domain misses cluster around firm context,
   horizon, and what the data measures. Those became tenets 2, 5 and 6, and the
   error-asymmetry tenet exists so that "more thorough" is not a free win.
