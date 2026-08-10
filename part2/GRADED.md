# Part 2 — graded against human gold

Ten base items (S1–S10) have gold and are scored here. The four context-flip
items are not yet filled and are excluded by name in the output rather than
silently dropped: `scripts/score.py`, full run in `graded-output.txt`.

The design-intent check passes first. `forks.json` was built with M1 as the
intended-stronger methodology on all ten seeds. **Gold preferred M1 on 5 of 10** —
the construction prior is fully washed out of the answer key, so the eval is not
scoring models against my own generation bias.

---

## Headline — accuracy is only interpretable against a per-split baseline

Gold prefers A on 6 of 10 items, so *always answer A* scores 60% while
discriminating nothing. The strong and weak splits have very different baselines,
and reporting a single aggregate hides the entire result.

| | Haiku 4.5 | Sonnet 5 | Opus 4.8 | Opus 5 | baseline |
|---|---|---|---|---|---|
| All calls (n=20) | 55% | 55% | 50% | **65%** | 60% |
| **Strong items** (n=12) | 58% | 67% | 67% | **75%** | **50%** |
| **Weak items** (n=8) | 50% | 38% | 25% | 50% | **75%** |
| Both orderings agree with gold | 40% | 50% | 40% | **60%** | — |

**On the aggregate, three of four models fail to beat a coin that always says A.**
That is the number a careless writeup would report, and it is close to meaningless.

**On strong items the capability ladder is clean and monotone:** 58 → 67 → 67 → 75
against a 50% baseline. Where the human reviewer was confident, the models track
model quality in the expected order, and every one of them beats chance.

**On weak items every model is far below its 75% baseline**, and the ordering
roughly inverts — Opus 4.8 is worst at 25%. Where gold itself was torn, more
capable models are not better and the whole panel does worse than answering
blind. Eight calls, so treat the ordering as noise; the *level* is not noise, and
it is consistent across all four models.

This is the answer to the brief's sense-check. The worry was *"if Haiku scores
90%, the items are too easy."* Haiku scored 55%, below the trivial baseline.
The items are not too easy. The open question is now the opposite one — whether
the weak items are discriminating at all, or are simply items where one
reviewer's call is not recoverable from the plans.

**Recommendation: report strong-item accuracy as the headline metric** and carry
weak items as a separate diagnostic, not folded into one number.

## Where the models agree with each other and not with gold

| Item | Gold | Models correct |
|---|---|---|
| S4, S10 | B strong / A weak | **4/4** |
| S1, S7 | A strong | 3/4 |
| S6 | B strong | 2/4 |
| S2, S8, S9 | B strong / A weak / B weak | 1/4 |
| **S3, S5** | A weak / **A strong** | **0/4** |

**S5 is the sharpest item in the set.** Gold is *strong* for A; all four models
chose B under both orderings. Unanimous, confident, and wrong against a confident
human — that is a shared blind spot rather than model noise, and it is worth more
than any aggregate here. S3 is the same shape at weak strength.

The inverse case is also informative: Haiku is the *only* model correct on S2 and
S9, while being wrong on S1 and S7 where the other three are right. Its 55% is
not a weak version of the others' behaviour — it is differently wrong. Aggregate
accuracy cannot see that.

---

## Two instrument defects found by the gold pass — these invalidate two metrics

Both were caught by scoring, not by inspection, and both mean a number that looks
like a model failure is actually a defect in my own instrument. Neither
`goal_type` nor `error_asymmetry` accuracy is reportable as model capability.

**1. `error_asymmetry`: the schema offers a value the prompt forbids.**
The enum is `type_i_dominant / type_ii_dominant / symmetric`. Gold uses
`symmetric` on 3 of 10 items. But the prompt says:

> *"Capped downside with unbounded upside (venture), or **a symmetric payoff run
> with breadth** (most quant), makes **Type II** the expensive error."*

The only place the guidance mentions symmetric payoffs, it routes them to
`type_ii_dominant`. Nothing anywhere states when to return `symmetric`. Result:

| | Haiku | Sonnet | Opus 4.8 | Opus 5 |
|---|---|---|---|---|
| `symmetric` emitted, 20 calls each | **0** | **0** | **0** | **0** |

Zero in 80 calls. The 0/6 score on gold's symmetric items is **compliance with
the prompt**, not a failure of judgment. SCORING.md calls error asymmetry the
first of four discriminating axes; that axis is currently broken.

**2. `goal_type`: none of the five labels is defined anywhere.**
`grep` for each of `single_name_kpi`, `broad_market_effect`, `mechanism_test`,
`blind_fit_oos`, `capability_build` across `pointwise.md`, `pairwise.md` and
`TENETS.md` returns **zero occurrences of all five**. The schema property carries
no `description` either. Models received a bare enum of five snake_case strings
and had to infer my private semantics from the identifiers.

Measured accuracy was 15–45% against a 40% baseline. That is a measurement of
label-guessing, not of framing ability. `blind_fit_oos` — gold's label on 3 of 10
items — was emitted 0 times by Haiku and Sonnet.

This defect is worse than it looks, because SCORING.md makes Group A conditional:
*"most later fields are conditional on it… a `breadth` finding only means
something once `goal_type` says breadth applies."* The conditioning field was
undefined.

**Fix before any re-run:** write a one-line definition of each `goal_type` into
the schema `description`, and either define when `symmetric` applies or remove it
from the enum and from gold. Both change the instrument, so both invalidate
cross-run comparability and require re-running all four models.

## What survives

| Metric | Status |
|---|---|
| Pairwise preference accuracy | **valid** — no definitional dependency |
| Self-consistency, position bias | **valid** — gold-free |
| `decisive_tenets` overlap | **partly** — categories are defined; tenets 1 and 13 have no category, so it is a lower bound |
| `goal_type` accuracy | **invalid** — labels undefined |
| `error_asymmetry` accuracy | **invalid** — prompt contradicts the enum |

`decisive_tenets`, on the lower-bound reading: Haiku 60%, Sonnet 50%,
Opus 4.8 55%, Opus 5 65% hit at least one of gold's stated deciding tenets. The
spread is narrow and this does not separate models.

## Still outstanding

Pointwise **recall and precision are not computed here.** They require semantic
matching of each model issue against gold's prose critiques — 65 gold issue
paragraphs across the 10 items — which is a judgment call per issue and belongs
in a separate scorer, not in deterministic comparison. That is the remaining
piece of the SCORING.md design, and it is where the Part 1 finding predicts the
real separation lives (recall was at ceiling; precision was the weakness).
