# Can Claude tell good research from bad?

**Mayank Mahajan · Finance Domain Research Lead take-home**
Repo: `github.com/mayankmahajan24/finance_collaborator` — seeds, generation and
critique scripts, eval items, gold, scoring, and every raw model call.

*All figures and tables are in Appendix A and referenced by number. Part 0 is
Appendix B; repo map is Appendix C.*

---

## Part 1 — Where Claude breaks as a research planner

Ten seeds, five quant and five fundamental, across obviously-good,
obviously-flawed and subtly-flawed. Claude produced a plan per seed then critiqued
it, seeing only the seed sentence. Two readings followed: my desk verdict per
item, and an automated score against a pre-written flaw.

**Claude over-applies generic research methodology and under-applies finance
domain knowledge.** It knows the catalogue of ways research goes wrong and recites
it whether or not the entries apply; what it misses is almost always an
institutional or mechanical fact about the instrument, the data, or how the market
prices information (**Table 1**). That inverts the brief's framing: the failure is
closer to **"doesn't know the finance,"** and methodological fluency disguises it.
Table 1's two columns are one failure — reaching for the generic checklist is what
a model does when the domain insight is absent. I found a defect in **10 of 10
critiques**; five contained an outright overreach, at ~2.6 manufactured objections
each.

**Unearned confidence shows up in two registers.** Claude *buys recall with
volume* — a ~2,400-word plan enumerating every plausible caveat scores well on "did
it catch the flaw?", and the same surface area produces the manufactured
objections. It also **buys credibility with precision**, reaching for exact-looking
thresholds never derived from anything and then hanging decisions on them
(**Table 3**, five of ten items). "We will stop if the effect is small" is honest;
"we will stop below 0.5%" manufactures a load-bearing rule — discipline to read,
decoration in function.

**On the discretionary ideas — "anchors on the narrative" or "can't tell
mechanism from correlation"?** Neither. Only S4 was judged *too anchored*, and both
S4 and S7 plans actively refused their seed's embedded conclusion. The real failures
were **context and implementability**: a 40,000 firm-quarter panel proposed to a
five-person Bloomberg shop; public retail-identification proxies to a market maker
that internalises retail flow and already holds the real labels. Claude reasons
about mechanism competently and misjudges who it is working for.

**Does it catch its own mistakes?** Partially — the critique is the stronger
artifact, but self-diagnosis never reaches the recommendation. S5's plan says in
its own pitfalls section that the signal is mined, then budgets eight weeks to
confirm it. S2's plan headlines a random 20% holdout and notes *in the same
sentence* that random splits leak — **the brief's own named failure, "proposes
validation that leaks," in the sharper form where the plan diagnoses itself and
proceeds anyway.**

**Dodging a named flaw is a poor proxy for quality.** Seven of ten plans avoided
the pre-written flaw; four were acceptable to me, and the disagreement ran both
ways (**Table 2**). This killed two design options for Part 2: gold must be
human, and the eval must not rest on planted flaws.

---

## Part 2 — An eval for the discriminator

**Construction** (**Table 4**). Ten seeds × two distinct methodologies, each
written against an opaque `sha256` token so generators never knew a sibling plan
existed — neither side is a strawman. Plans were matched within 2.2% on length,
because LLM judges favour length and an uncontrolled gap lets a model win without
doing any finance reasoning. Every item names **which firm is asking**, because a
plan is good or bad *for someone*. Judging runs against 13 tenets, pairwise and
pointwise, each pair scored A/B and B/A.

**A construction check that mattered:** I built every pair intending M1 to be the
stronger side; my blind gold preferred M1 on **5 of 10**. The design prior washed
out, so the eval is not scoring models against my own generation bias.

### Result 1 — the aggregate is a trap; the split is the result

Gold prefers A on 6/10, so *always answer A* scores 60%, and on that aggregate
three of four models fail to beat a coin. Split by **my own stated confidence**
(**Table 5**), the same twenty calls say something else: where I was confident,
accuracy is monotone in capability (58→67→67→75) and everything beats chance;
where I was torn, everything falls below its 75% baseline and the order inverts.

**Preference strength is a difficulty label, not just a reporting split.** It costs
one keystroke and predicts whether an item discriminates at all — the most reusable
thing this eval produced. Report strong-item accuracy as the headline. Two
individual items also carry more than the aggregate does: a unanimous confident
miss, and evidence Haiku is *differently* wrong rather than uniformly worse
(**Table 6**). The brief's sense-check meanwhile passes in the direction it was not
worried about — **Haiku scored 55%, not 90%.** The items are not too easy.

### Result 2 — the right recall/precision framing is *not* F1

Gold prose was classified into **45 blocking** issues (the recall denominator), 7
secondary, 4 praise, and 4 **anti-objections** — places I explicitly rule an
objection *out*. Matching is semantic, with three precision buckets so legitimate
issues I never wrote still count as real, and `why_it_applies_here` required per
issue, so an objection that would read identically against any plan in the asset
class is self-revealing.

**This inverted the prediction the design was built on** (**Table 7**). Part 1
implied recall would sit at ceiling and precision would discriminate; the reverse
holds, because Part 1 measured recall against *one pre-named flaw* (9/10, a
ceiling) and Part 2 measures it against *45 real desk objections* (62% at best).
**Recall against a planted flaw is a ceiling metric; recall against a real
critique discriminates.**

But recall is **not monotone in capability** — Haiku beats both mid-tier models by
firing 28.6 issues per review, a shotgun wide enough to cover half the answer key
by volume. **So F1 is the wrong summary.** Haiku's F1 band spans both mid-tier
models, because F1 is a ratio and normalises away how much noise was produced to
buy the recall. The absolute count does not: **14.3 manufactured objections per
review versus 0.1** — a ~100x gap, and the cleanest separation in this eval.

**My answer to the brief's question: report recall and manufactured-per-review as
a pair, and do not report F1.** That pair reads in desk terms — did you find what
mattered, and how much noise did I read to get it. A reviewer raising fourteen
unearned objections per plan is unusable regardless of coverage.

### Result 3 — the gold pass audited the instrument, and it lost

Two defects surfaced only when real labels met the schema, and **neither was
visible in gold-free diagnostics that had looked clean across three prior sweeps**
(**Table 8**). Both metrics they touch are unreportable as capability. The
transferable lesson: **gold-free process metrics cannot validate an instrument.**

**Caveats.** Opus 5 scored its own reviews and posts the best recall *and*
precision under its own judge, so treat its margin as an upper bound — this does
not touch the Haiku result, a 100x gap. It is also the only evaluated model with
thinking on by default. And all gold is one reviewer, so "wrong" and "disagrees
with this desk" are indistinguishable throughout.

---

## Part 3 — Collecting discriminator data at scale

Part 2 accidentally ran both halves of the brief's experiment: I wrote gold
free-hand for twenty plan-critiques, and four models produced issue lists for the
same plans, every issue adjudicated. So this is measured, not intuited.

**Strike-out imposes a hard recall ceiling** (**Table 9**): gold can only contain
what the generator proposed, and the best covers **62%** of the blocking issues I
wrote free-hand, so even at its best it silently loses 38%. **Free-hand writing has
the opposite gap** — models raised 46–236 legitimate issues I never wrote. Neither
dominates, which is the real argument for a hybrid.

**Candidate quality controls the label distribution, and good candidates break
it** (**Table 10**) — the brief's explicit question, and the answer inverts the
instinct. An expert reviewing Opus 5's candidates strikes 3 of 133 and spends the
session agreeing: a 98%-positive label set that no discriminator can be evaluated
on, because it has almost no negatives.

**The reframe: the strikes are the product, not the waste.** Every strike *is* an
anti-objection — the label type Part 2 found sharpest, and the one free prose
produces least (4 across twenty critiques, incidentally). Haiku candidates would
yield **286**, deliberately. **So use a high-recall, low-precision generator:
Haiku is right here for exactly the reason it was the worst discriminator.** Its
noise is the negative-label supply.

The process is three stages (**Table 11**), ordered so anchoring cannot
contaminate judgment. **Not poisoning the labels:** write-first ordering means un-anchored issues exist
before candidates are seen; a **15% write-only control arm** makes anchoring
measurable rather than merely unmeasured; **never generate candidates with a model
under evaluation**, since Part 2 hit exactly this contamination; and keep-rate is
the live health metric — drifting toward 100% means either the generator improved
or the expert stopped reading, which only the control arm separates (**Table 12**).

**Buy a second expert before buying more items.** All Part 2 gold is one reviewer,
and that ambiguity is the binding constraint on every number in this report.
**Honest limit:** these economics are argued in operations, not measured hours —
nobody timed the gold pass, so time stage 1 against stage 2 in the first session.

---
---

# Appendix A — Figures and tables

### Table 1 — Part 1: what Claude misses vs. what it over-raises

| The **misses** are domain facts | The **overreaches** are method boilerplate |
|---|---|
| deposits ≠ active trading (S1) | tertiary confounding variables (S1) |
| sales headcount correlates with visitors (S2) | survivorship bias on a 12-name universe (S2) |
| negative→positive earnings break a multiple; revenue- vs EBITDA-based valuation as a company matures (S6) | a statistical analysis that is not possible (S3) |
| round-lot profitability decays in **minutes**, not days (S8) | minimum event count set far too high (S9) |
| renegotiation flow-through timing to margins (S10) | scope expanded to irrelevant analyses, e.g. scale estimation (S1) |
| the shortage must be diffed against existing disclosure and buy-side consensus (S9) | |

### Table 2 — Part 1: flaw-dodging disagrees with desk quality, in both directions

| | Count | Seeds |
|---|---|---|
| Avoided the named flaw, still **bad** | **5** | S2, S4, S6, S8, S10 |
| Only *partially* handled it, still **fine** | **2** | S3, S5 |
| Agree | 3 | S1, S7, S9 |

*7/10 avoided the pre-written flaw; 4/10 were acceptable to me. The totals invite
a "harsher reviewer" reading; the per-item split rules it out.*

### Table 3 — Manufactured precision: invented thresholds carrying real decisions

| Item | The numbers | What rests on them |
|---|---|---|
| S4_A | prior of 20:1–30:1 against; ~25x likelihood ratio required | The entire evidence bar — the denominator was invented |
| S7_A | >0.5% priced / <0.2% dismissed; CAR under 1 SD | The stop-go gate before any fundamental work |
| S7_B | ≥75bp above midpoint; ≥50bp above consensus; move ≥1.2x implied | Whether the trade goes on at all |
| S8_B | lift ≥2.0x, recall ≥35%, signing ≥70%, r ≥0.40, t ≥3 | Five conjunctive kills on labels with no ground truth |
| S9_A | tier haircuts 90–95 / 70 / 25–35% | The coverage math and the verdict |
| S10_A | under 25bp makes tone moot; over 75bp forces a call | The materiality frame |

### Table 4 — Part 2: eval construction

| Property | Choice | Why |
|---|---|---|
| Plans per seed | 2 distinct methodologies | Precision is only measurable when both sides are competent |
| Generation | Opus 5, blind `sha256` token | Generator never knew a sibling plan existed → no strawman |
| Length | 500–600 w, matched within **2.2%** | LLM judges favour length; uncontrolled, it is free signal |
| Context | 5 firm profiles, stated per item | A plan is good or bad *for someone* (tenet 2) |
| Judging basis | 13 tenets + precedence rules | Explicit, inspectable, and scoreable |
| Ordering | every pair run A/B **and** B/A | Separates judgment from position bias |
| Gold | human, written blind | Part 1 showed flaw-matching certifies plans a desk rejects |
| Planted flaws | **none** | Generators repaired them; and Table 2 condemns the metric |

### Table 5 — Part 2 pairwise: accuracy against a per-split baseline

| | Haiku 4.5 | Sonnet 5 | Opus 4.8 | Opus 5 | baseline |
|---|---|---|---|---|---|
| All calls (n=20) | 55% | 55% | 50% | 65% | **60%** |
| **Strong-preference items** (n=12) | 58% | 67% | 67% | **75%** | **50%** |
| **Weak-preference items** (n=8) | 50% | 38% | 25% | 50% | **75%** |
| Agreed with gold under **both** orderings | 40% | 50% | 40% | **60%** | — |

### Table 6 — Part 2: per-item agreement (the items worth more than the aggregate)

| Item | Gold | Models correct (of 4) |
|---|---|---|
| S4, S10 | B strong / A weak | **4** |
| S1, S7 | A strong | 3 |
| S6 | B strong | 2 |
| S2, S8, S9 | B strong / A weak / B weak | 1 |
| **S3, S5** | A weak / **A strong** | **0** |

*S5 is the sharpest item in the set: gold is **strong** for A and all four models
chose B under both orderings — unanimous and confident against a confident human
is a shared blind spot, not noise. Separately, Haiku is the **only** model correct
on S2 and S9 while missing S1 and S7: it is differently wrong, not uniformly
worse, which no single-scalar leaderboard can show.*

### Table 7 — Part 2 pointwise: recall, precision, and why F1 fails

| | Haiku 4.5 | Sonnet 5 | Opus 4.8 | Opus 5 |
|---|---|---|---|---|
| **Recall** (of 45 blocking) | 53% | 29% | 33% | **62%** |
| **Precision band** | 31–50% | 68–97% | 71–92% | **90–98%** |
| **F1 band** | 0.39–0.52 | 0.41–0.45 | 0.45–0.49 | 0.74–0.76 |
| Issues raised per review | **28.6** | 3.3 | 3.6 | 6.7 |
| **Manufactured objections per review** | **14.3** | **0.1** | **0.3** | **0.2** |
| Hit an anti-objection (gold ruled it out) | 7 | 0 | 3 | 2 |

*Precision is a band, not a point: the scorer was told to choose "real but
unlisted" over "manufactured" when genuinely torn and flag it. The upper bound
counts those as real, the lower as manufactured. Quoting 97% would quote the
tie-breaking rule rather than the model. Note the F1 bands for the bottom three
models overlap — F1 does not rank them.*

### Table 8 — Two instrument defects the gold pass exposed

| Defect | Evidence | Consequence |
|---|---|---|
| `error_asymmetry` offers `symmetric`, but the prompt routes symmetric payoffs to `type_ii_dominant`, and nothing says when to return `symmetric` | **0 emissions in 80 calls**, all four models; gold uses it on 3/10 items | The 0/6 score is prompt compliance, not misjudgment. Axis unreportable |
| None of the five `goal_type` labels is defined anywhere — zero occurrences across both prompts and the tenets; no schema description | Measured accuracy 15–45% vs a 40% baseline | Measures label-guessing. Worse because later fields are conditional on it |

### Table 9 — Part 3: strike-out's recall ceiling on gold

| Candidate generator | Ceiling on gold | Legitimate issues it found that I did *not* write |
|---|---|---|
| Sonnet 5 | 29% | 47 |
| Opus 4.8 | 33% | 46 |
| Haiku 4.5 | 53% | 236 |
| **Opus 5** | **62%** | 97 |

*Even the best generator loses 38% of my own blocking issues — they never appear
as a candidate, so there is nothing to keep. This is the ceiling of the method,
not a tuning problem. The right-hand column is the mirror-image gap in free-hand
writing.*

### Table 10 — Part 3: candidate quality controls the label distribution

| Candidate generator | Expert keeps | Expert strikes | Label balance |
|---|---|---|---|
| Opus 5 | 130 | **3** | 98% positive |
| Sonnet 5 | 64 | 2 | 97% positive |
| Opus 4.8 | 66 | 6 | 92% positive |
| **Haiku 4.5** | 286 | **286** | **50 / 50** |

*Free-hand prose produced **4** anti-objections across the same twenty critiques,
and only incidentally. Strike-out against a noisy generator produces 286,
deliberately.*

### Table 11 — Part 3: proposed three-stage collection

| Stage | Who | What they see | What it produces |
|---|---|---|---|
| **1. Write-first, un-anchored** | Expert (short) | Context, seed, plan — **no model output** | Blocking issues only, one clause each; preference + strength. Recovers the 38% no generator proposes |
| **2. Strike-out** | Expert (fast) | ~20–30 noisy candidates | keep / strike / duplicate. Every strike is an anti-objection; balanced labels |
| **3. Merge** | Model, expert on conflicts only | Both prior stages | Final gold + a free recall measurement of the generator |

### Table 12 — Part 3: what to measure to know it is working

| Metric | Why | Failure signal |
|---|---|---|
| Keep-rate per session | Label balance | Trends toward 100% |
| Stage-1 issues no candidate proposed | Is stage 1 earning its cost | Goes to ~0 → drop stage 1 |
| Write-only vs strike-out gold composition | Anchoring | Systematic divergence |
| Preference-strength distribution | Item difficulty mix | Mostly weak → items not discriminating |
| Inter-expert agreement on a shared subset | Is gold reproducible | Low → the eval measures one desk |

---

# Appendix B — Part 0: two ideas, written before touching Claude

Both written without model assistance and not revised afterward. They are
deliberately the **same shape** — third-party panel data used to infer a company's
reported financials. One works, one doesn't. The point isn't "alt data good/bad";
it's whether the panel observes the thing that drives the reported number.

## B1 — Well-posed: does Kalshi adoption cannibalize DraftKings?

The readout is: among users already active on DraftKings, does a first Kalshi
transaction coincide with lower subsequent DraftKings retention, spend, or
transaction frequency versus DraftKings users who never use Kalshi? The primary
pair is DraftKings-to-Kalshi; use DraftKings-to-FanDuel only as the switching
benchmark.

Use transaction-level credit card and bank deposit data mapped to DraftKings,
Kalshi, and FanDuel activity. Start cohorts in 2021, aggregate each user's
activity to monthly and weekly periods, and produce separate credit-card,
bank-deposit, and combined source cuts. For Kalshi, use a 3-month forward window
because history is short and adoption ramped recently.

Compare two groups of DraftKings users around the month or week when the first
group tries Kalshi for the first time. The first group is users who were active on
DraftKings and then made their first Kalshi transaction. The comparison group is
DraftKings-active users from the same time period who never used Kalshi; use a
stable 5% sample of those users so the analysis is repeatable and not too
expensive to run. Track DraftKings activity before and after the Kalshi start
date, including whether users remain active, how much they spend or deposit, and
how often they transact. Show the raw paths, the gap between Kalshi adopters and
non-adopters, and the percent gap versus the comparison group. Adjust for any
average pre-Kalshi gap between the two groups so the post-Kalshi change is easier
to read.

Before interpreting results, confirm the merchant mapping for each product, check
that the Kalshi adopter sample is large enough, and make sure adopters and
non-adopters looked reasonably similar before the first Kalshi transaction.
Compare credit-card, bank-deposit, and combined results for the same directional
message, and use weekly versus monthly views to understand timing. Treat the
output as directional evidence, not proof of causality, because Kalshi adopters
may be different types of users, observed deposits/spend are not the same as
betting handle, and Kalshi's short history may leave limited post-adoption data.

## B2 — Plausible, but dies at desk review: portal MAU → seat-based SaaS ARR

The readout is: among US-listed SaaS companies that price per seat, does the level
of monthly active users on the client-facing portal explain reported ARR one to
two quarters later, closely enough to call the number before the company prints
it? The primary relationship is portal MAU to ending ARR; use subscription revenue
run-rate only as a secondary read for names that do not disclose ARR directly.

Use third-party web and mobile panel data mapped to each company's client-facing
portal domain and app. Start in 2021, aggregate panel traffic to monthly and
quarterly periods, and produce separate web, mobile, and combined source cuts.
Pull ARR, subscription revenue, and consensus estimates point-in-time from filings
and press releases so restated figures do not leak into the fit. Screening for
seat-based pricing and quarterly ARR disclosure gives roughly 35 to 50 names.

Line up each company's quarterly portal MAU against its reported ARR and fit the
relationship in logs across the panel, with company effects to absorb persistent
differences in panel coverage and price level, and quarter effects to absorb
common macro. Test MAU lagged zero, one, and two quarters and carry forward
whichever lag fits best. Where a company has enough quarters, fit it on its own so
the coefficient reflects that name rather than the pooled average; otherwise fall
back to the pooled coefficient. Cluster standard errors by company. Hold out a
random 20% of company-quarters, fit on the rest, and report out-of-sample R² and
average percentage error against reported ARR. Treat the relationship as usable if
average error is under 3% and the MAU coefficient is positive and significant in
most of the per-company fits. Then, two weeks ahead of each print, generate a
predicted ARR, and compare it to consensus.

Before interpreting results, confirm the domain and app mapping for each company,
check that the panel covers each name densely enough to be stable, and look for
level shifts where the vendor has re-baselined its methodology. Use a
point-in-time universe that includes delisted names so the fit is not run only on
survivors. Compare web, mobile, and combined cuts for the same directional
message, and report all three lags rather than only the one that fit best. Treat
the output as directional rather than exact, because some names have fewer than
twelve quarters of history, the universe is small enough that a handful of large
companies drive the pooled fit, and panel coverage of any single company can
change without notice.

### Why B2 dies

**It dies on the billing mechanic.** Seat-based ARR bills on seats *contracted*,
not seats used — so shelfware lets ARR compound while MAU is flat, and free viewer
tiers let MAU climb on seats that bill nothing. Even where the two co-move, ARR is
seats × price and MAU is silent on the price/mix half, which carries most of the
growth in a mature seat model. The panel makes this worse rather than better:
enterprise traffic behind SSO and VPN is largely invisible to third-party
measurement, so coverage skews toward exactly the small accounts contributing
least to ARR. A level regression across SaaS names will still print a high R²
because both series trend up — which is precisely what makes the result convincing
and wrong.

Every step in B2 is individually standard practice. The caveats it *does* raise —
survivorship, thin samples, concentration — are real and responsible, and **none
of them is what kills it.** It succeeds on its own terms, which is what makes it
the right shape for a subtly-flawed eval seed. It became **S2**.

---

# Appendix C — Repo map

| Path | Contents |
|---|---|
| `ideas.md`, `seeds.json` | 14 candidate seeds, the working 10, buckets and cut rationale |
| `part0/` | The two ideas above, plus where B2's flaws are loaded |
| `part1/` | Plans, critiques, per-item human verdicts, `FINDINGS.md`, provenance |
| `part2/evaluator/` | `TENETS.md` (13 tenets + precedence rules), pairwise & pointwise prompts |
| `part2/plans/` | 20 plans, raw and condensed, with the blind-token manifest |
| `part2/items/`, `part2/gold/` | 14 eval items; gold + the classified issue denominator |
| `part2/runs/` | Every raw model call — four models, both modes, plus issue scores |
| `part2/GRADED.md`, `SCORING.md`, `ISOLATION.md` | Results, metric design, isolation guarantees |
| `part3/README.md` | Collection proposal |
| `scripts/` | Generation, eval runner, retry/merge, scoring, comparison, flip test |
| `REPORT-NOTES.md` | Working notes 1–7, including decisions reversed and why |

**To extend it:** add seeds to `seeds.json`, generate plans via `scripts/generate.py`
(blind tokens), build items with `build_items.py`, then `run_eval.py --model X`.
Scoring is `score.py` (deterministic) and `score_issues.py` (semantic). Prompt and
schema fingerprints are stored per run and re-checked on merge, so a changed
instrument refuses to mix with old results rather than silently corrupting a
comparison.

**Not done, and why it matters:** the four context-flip items (same seed, same
plans, different firm) have no gold, so the one axis that holds the plan fixed and
varies the brief is measured only gold-free. That is the first thing I would
finish.
