# Report notes — working file

Raw material for the ≤3-page report. Not a deliverable itself. Findings recorded as
they're established, with the numbers that back them.

---

## Note 1 — Output length: what the brief constrains, and where length actually hurts

**Date:** 2026-08-09
**Question that prompted it:** are the generated plans too long for the assignment?

### What the brief constrains

The only length cap in the take-home is on the report: "≤3 pages text + **unlimited
examples/tables**." Plans, critiques, and eval items are repo artifacts and appendix
material. They do not count against the cap. Nothing in the current repo violates the
instructions on length.

### Measured lengths

Part 1 (`part1/plans/`, `part1/critiques/`):

| | mean words | range |
|---|---|---|
| plans (S1–S10) | 2,380 | 1,966 – 2,716 |
| critiques (S1–S10) | 2,198 | 1,559 – 3,164 |

Part 2 (`part2/items/S*.json`):

| seed | plan_A | plan_B | asymmetry | A method | B method |
|---|---|---|---|---|---|
| S1 | 2,278 | 2,748 | +21% | M1 | M2 |
| S2 | 2,565 | 3,652 | **+42%** | M2 | M1 |
| S3 | 2,783 | 3,033 | +9% | M2 | M1 |
| S4 | 2,617 | 1,954 | −25% | M1 | M2 |
| S5 | 3,853 | 2,284 | **−41%** | M2 | M1 |
| S6 | 2,052 | 2,612 | +27% | M1 | M2 |
| S7 | 2,614 | 2,920 | +12% | M1 | M2 |
| S8 | 2,589 | 2,621 | +1% | M2 | M1 |
| S9 | 2,609 | 2,451 | −6% | M2 | M1 |
| S10 | 2,177 | 2,568 | +18% | M1 | M2 |

Means by methodology: **M1 = 2,578 w, M2 = 2,720 w.** No systematic methodology-length
confound — the generation setup is clean on that axis. The problem is *within-item*
asymmetry, which runs to ±42%.

### Verdict: Part 1 plans stay long

The brief asks for hypothesis, data, methodology, validation, known pitfalls — five
sections. ~2,400 words is what a genuinely executable plan costs. More importantly Part 1
is a *measurement of what Claude produces cold*: capping the length would characterize a
constrained Claude, not the real one, and would forfeit the claim that the sample is
representative.

### The framing this unlocks (→ goes in the Part 1 findings section)

Length is not a side issue — **it is the mechanism behind the headline result.**

A 2,400-word plan that enumerates every caveat makes "did it avoid the gold flaw?"
easy to score generously. Recall near ceiling (9 caught / 1 partial / 0 missed) is
partly an artifact of surface area. And the same surface area is what produces
**2.6 manufactured objections per critique**.

So recall-high and precision-low are not two findings. They are one:

> **Claude buys recall with volume.** It is not discriminating between issues that matter
> and issues that merely exist — it is emitting the union of everything plausibly relevant
> and letting coverage do the work. On a desk this reads as diligence; under a precision
> metric it reads as noise.

This directly answers the brief's "where does it break?" — the failure is not "doesn't know
the finance" (the domain content is largely right). It is **"doesn't know how research goes
wrong"**, specifically the triage step: a desk reviewer's skill is knowing which three of
twenty objections would actually change the trade.

Corollary worth stating: this makes the *critique* the weaker artifact of the two, not the
plan. A 2,200-word "desk review" is a category error — a real desk review is 5–8 bullets,
and its value is in what it leaves out.

### Verdict: Part 2 eval items are too long — fix before filling gold

~5,300 words of paired plans per item, ~53,000 words across the ten. Three concrete
problems, in order of severity:

1. **The planted flaw stops being the deciding difference.** At ~2,600 words per side, A
   and B differ in dozens of places. When the discriminator picks A, that choice cannot be
   attributed to the flaw that was planted. The item no longer measures what it was
   designed to measure, and `why_B_is_convincing` becomes unfalsifiable.
2. **Length bias contaminates the pairwise metric.** LLM judges systematically favor longer
   responses. A 42% within-item length gap (S2) is a free signal the discriminator can
   exploit without doing any finance reasoning. The M1/M2 balance above means this is
   per-item noise rather than a systematic bias — recoverable, but only by fixing it.
3. **It breaks the sense-check the brief hands us.** "Performance should scale with model
   intelligence (Haiku 4.5 < Sonnet 4.5 < Opus 4.5 < Opus 4.8)." Noisy long-form pairs
   compress that spread. If the gradient comes out flat we won't be able to distinguish
   "items are bad" from "items are noisy."

**Fix:** condense each eval plan to **400–700 words, matched within ~10% of its pair**, with
the planted flaw as the only substantive difference. Derive by *editing down* the existing
`part2/plans/raw/*.md` rather than regenerating short — this preserves the "Claude wrote it,
under isolation, seeing only its token" provenance in `part2/plans/_manifest.json` and
`part2/ISOLATION.md`, and lets us state exactly what was changed. The brief explicitly
permits it: "you may use Claude / edit / pick your methodology for generating these."

Record the edit step in `part2/ISOLATION.md` so the provenance chain stays auditable.

**Sequencing:** do this *before* filling `part2/gold/S*.json` — those are still stubs
(~10 words each), they are the human labels the entire eval rests on, and shortened plans
make them substantially faster to write.

### One-line version for the report

> Plan length is the finding, not a formatting choice: Claude achieves near-ceiling recall
> by emitting everything plausibly relevant, which is also why its critiques carry 2.6
> manufactured objections apiece. The eval in Part 2 therefore has to control for length
> explicitly, or it measures verbosity instead of judgment.

---

## Note 2 — Condensation applied, and what it exposed

**Date:** 2026-08-09
**Status:** Note 1's Part 2 fix is done. A separate problem surfaced during verification.

### Condensation: done

All 20 Part 2 plans edited down from `raw/` to `short/`, then items rebuilt.

| | before | after |
|---|---|---|
| total | 53,020w | 11,969w (4.4x) |
| per plan | 1,956–3,855w | 583–600w |
| max within-pair gap | 42% (S2) | **2.2%** (S10) |

Length bias is now controlled: every pair matches within ~2%, so a
length-driven judge gets no free signal. Condensing agents ran on Sonnet, scoped
as tightly as the generators (one file, "editing task not a review", forbidden
from adding caveats or fixing methodology), and never saw seed id, slot, pair,
or planted flaw. Recorded in `part2/ISOLATION.md`.

### The problem this exposed: generators repaired their own planted flaws

Verifying that the flaws survived condensation revealed they were often not
there to begin with. **8 of 10 intended-weaker plans reference their own core
flaw in the original `raw/` text.**

| seed | intended-weaker flaw | self-reference |
|---|---|---|
| S1 | denominator circularity | 2 hits — **and fixes it** |
| S2 | spurious trend / leaky holdout | 0 |
| S3 | circular internal validation | 0 |
| S4 | n=1, no base rate | 3 |
| S5 | crowded / surprise collinearity | 4 |
| S6 | tautology / mechanical denominator | 7 |
| S7 | published = consensus | 4 |
| S8 | proxy polarity unvalidated | 7 — **and partly fixes it** |
| S9 | backlog inflated / frame accepted | 7 |
| S10 | guardedness confounded at source | 8 |

S1_B states it explicitly — "DraftKings deposits sit in the denominator of the
regressor and are the outcome... a large negative coefficient even in a world
with zero behavioral substitution, purely from arithmetic" — then makes
baseline-deflated intensity the primary spec, repairing it. S8_A says round-lot
imbalance is "tested as a retail proxy, not assumed" and residualizes against a
sub-penny companion signal.

**Cause:** generators were instructed to "build the strongest possible version of
this approach." Asked for the strongest version of a flawed method, Claude
diagnoses and repairs the flaw. The instruction that was meant to prevent
strawmen also prevented the planted contrast.

### Why this is not purely bad news

A mention is not a repair, and the distinction decides the item:

- **names it and fixes it** -> contrast destroyed, item is broken
- **names it and proceeds anyway** -> contrast *intact*, and it is a **better**
  item than designed — it is exactly the Part 1 failure mode (Claude names a risk
  in its pitfalls section, then proceeds as if it had not), which is the sharpest
  thing a discriminator can be asked to catch

Grep cannot separate these. It needs adjudication per item.

### Implication for the report (-> Part 2 design section)

This is itself a finding about **generating** eval data, worth stating:

> You cannot reliably plant a methodological flaw in a Claude-written plan by
> specifying a flawed methodology. Told to build the strongest version, it
> repairs the flaw — 8 of 10 times here. Constructing convincingly-worse plans
> requires either constraining the model from self-correcting, or accepting a
> weaker contrast and letting the gold labeller find it.

Sequencing note: this must be resolved **before** gold, same as Note 1. Writing
gold against items whose contrast has silently collapsed wastes the most
expensive human input in the project.

### Resolution: the design never needed a planted flaw

Reframed. An item works if the two plans are **distinct strategies** for the same
question; there is no prior that one must carry a defect. Gold preference is the
human's judgment about which approach they would staff — a real decision, not the
recovery of a hidden answer.

Under that framing the self-repair is not a defect at all. It is Claude doing
good work inside its assigned approach, which is exactly what "build the
strongest version of this methodology" asked for. The pairs are better for it:
two strong, genuinely different approaches is a closer match to an actual desk
decision than good-versus-sabotaged.

**Distinctness verified across all 10 pairs** (method vocabulary of A and B is
non-overlapping in every case):

| seed | plan A | plan B |
|---|---|---|
| S1 | cohort-stacked DiD, matched never-adopters | within-user share regression, PPML |
| S2 | log-levels panel FE nowcast | billable-seat gate, growth space |
| S3 | HDBSCAN/UMAP clustering, silhouette | external anchor, institution holdout, abstain |
| S4 | base rate across comparables | single-name documents, expected-loss sizing |
| S5 | DeBERTa fine-tune, quintile portfolios | two-week triage, kill gate, replication |
| S6 | regime/over-reaction, event ordering | cross-sectional panel, industry elasticities |
| S7 | CAR + revision price-in gate | logo-to-ACV ramp, Monte Carlo |
| S8 | RLOIB signal, Fama-MacBeth, cost model | venue partition, proxy benchmark, kill gate |
| S9 | RPO forward-commitment model vs EV | historical LTA outcomes, take-or-pay terms |
| S10 | COGS sizing, sensitivity, switching costs | defensiveness index over transcripts |

No regeneration needed. `forks.json` and `gold/design-intent.json` reframed;
`design-intent.json` is now explicitly a record of prior intent, not an answer key.

### What survives as a report finding

The generalizable observation stands on its own, independent of whether we wanted
the flaw:

> Told to build the strongest version of a specified methodology, Claude
> diagnoses and repairs that methodology's weaknesses — in 8 of 10 cases here,
> including explicitly naming a circular regressor and re-specifying around it.
> This is a capability result, and it is also a practical constraint on eval
> construction: you cannot plant a methodological flaw by specifying a flawed
> method.

---

## Note 3 — Part 1 findings rewritten against the human verdicts

**Date:** 2026-08-09
**Trigger:** the `your_verdict` column got filled in, which changed the headline.

### Dodging the named flaw is a poor proxy for plan quality

The two readings answer different questions — the subagent asked "did this handle
the one flaw specified in advance", the human asked "is this good work". The
totals (7/10 avoided vs 4/10 acceptable) invite a harshness reading; the per-item
breakdown rules it out, because the disagreement runs **both ways**:

| | Count | Seeds |
|---|---|---|
| Avoided the named flaw, still bad | **5** | S2, S4, S6, S8, S10 |
| Partially handled it, still fine | **2** | S3, S5 |
| Agree | 3 | S1, S7, S9 |

The five failed for reasons the gold flaw never named — "overly complex and
disorganized" (S6), "poor assumptions on context and implementability" (S8), "too
anchored" (S4). The two carried a partial version of the flaw and were still
worth staffing.

> **A plan's quality is not reducible to whether it dodged one pre-named
> defect.** Flaw-based scoring measures something narrower than what a desk
> decides on.

Same caution on the critique numbers: 9/10 "caught the flaw" versus 10/10 with a
defect. Both true — catching the named flaw and being a good review are different
achievements, and the recall figure is not a quality score.

Consequences for Part 2: gold must be human (a flaw-matching standard would have
certified five plans the desk rejected), and the eval should not rest on planted
flaws — a design abandoned for a separate reason, independently condemned here.

### Headline reversed

Earlier draft (automated scoring only): *the failure is not "doesn't know the
finance" — the domain content is largely right — it is "doesn't know how research
goes wrong."*

Human verdicts invert this. Sorting the ten verdicts by what was missed versus
what was over-raised:

- **Misses are domain facts** — deposits ≠ active trading (S1); sales headcount
  correlated with visitors (S2); negative→positive earnings breaking a multiple
  and revenue- vs EBITDA-based valuation as a company matures (S6); round-lot
  profitability decaying in minutes not days (S8); renegotiation flow-through
  timing (S10); diffing a shortage against disclosure and consensus (S9).
- **Overreaches are method boilerplate** — tertiary confounders (S1);
  survivorship bias and a 12-name universe (S2); an impossible statistical
  analysis (S3); an inflated minimum event count (S9).

> **Claude over-applies generic research methodology and under-applies finance
> domain knowledge.** It knows the catalogue of ways research goes wrong and
> recites it whether or not the entries apply; what it misses is almost always an
> institutional or mechanical fact. The two are the same failure — reaching for
> the generic checklist is what a model does when the domain-specific insight
> isn't there.

### On the discretionary seeds

The brief asks whether the fundamental failures are "anchors on the narrative" or
"can't tell mechanism from correlation." Evidence supports **neither** as the
main story. Only S4 was judged *"too anchored"*; S9's plan explicitly refused the
management frame. The real discretionary failures were **context and
implementability** — S8 *"why would an asset manager care about this?"*, S9
*"underassumes firm's market data tooling."* Reasoning about mechanism is
competent; judging who the work is for is not.

### Self-critique

Holds up, and in one direction: the critique is the stronger artifact. Human
verdicts note "better simplification" (S6), "better scoped" (S10), "picked up on
the wrong construct and overengineering" (S3). On the obviously-flawed seeds the
plan proposed a kill once, the critique three times. Claude knows an idea is weak
while writing the plan and scopes the full project anyway.

---

## Note 4 — Haiku 4.5 sweep (baseline model)

**Date:** 2026-08-09
**Status:** 28 pairwise + 28 pointwise calls, 0 errors after retries. Gold not yet
written, so these are the diagnostics that do not require an answer key.

### Pairwise — works, with two real weaknesses

| Diagnostic | Result |
|---|---|
| Self-consistent across A/B and B/A | **11 / 14** (chance 50%) |
| Agreement with my design intent | **10 / 11** consistent items = 91% |
| `goal_type` stable across orderings | **8 / 14** |
| Slot-A rate | 61% |
| Mean confidence | 3.7 / 5 |

Three items (S1, S6, S7) flip when the plans are swapped — on those Haiku is
responding to presentation, not discriminating. `goal_type` instability is worse:
the same seed and plans are read as `mechanism_test` one way and
`single_name_kpi` the other on 6 of 14. Since every later tenet is conditional on
the goal read, that instability propagates.

**Construction flaw, recorded:** in all ten forks I made M1 the intended-stronger
methodology. Slot assignment is mixed 5/5 so nothing leaks to an evaluator (it
never sees M1/M2), but my prior was never varied. That is why "agreement with
design intent" is cleanly measurable here — and why it must not be mistaken for
accuracy.

**The 91% needs care.** The brief's sense-check says a 90% Haiku score means the
items are too easy. But this is 91% agreement with *my prior*, not with gold. In
Part 1 the human verdicts diverged sharply from automated scoring; if gold
diverges from my intent on even three items, real accuracy falls toward 65%.
**Gold decides whether this eval is too easy, and nothing else can.** It also
promotes the no-tenets control from optional to necessary: with 13 tenets in the
prompt, 91% may be rubric-following rather than judgment.

### Pointwise — Haiku cannot follow the precision instruction

| | |
|---|---|
| Issues per review | mean **26.3**, median 17, max **90** |
| Severity split | ~90% secondary |
| Truncations before retry | 7 / 28 at 32k; 2 still failed at 48k; cleared at 64k |
| `explainability_fit` "over-" value used | **0 times** |
| Top category | `measure_validity` raised **194 times** |

A desk review is 3–8 findings. Haiku produced a median of 17 and one review of
90 against a 600-word plan — a ~15x overshoot. It marks ~90% of them secondary,
so it partly knows they do not matter, and emits them anyway. This is the
exhaustiveness/Type I failure the error-asymmetry tenet exists to catch.

**Consequence for scoring:** against a gold list of ~4 blocking issues, a
90-issue review scores near-zero precision by construction. The metric would
measure verbosity, not judgment.

**Partly the prompt's fault, and worth saying so.** The pointwise prompt is
~2,600 words over 13 tenets, most with sub-checks. A literal-minded model reads
it as a checklist and emits one issue per checkpoint. `measure_validity` at 194
mentions — the newest and most narrowly scoped tenet — supports that reading.

**What did work:** `why_it_applies_here` was never left generic (0 of 585 issues
had a justification under 80 characters). Forcing per-issue justification
produces real reasoning even when the model over-produces.

### Open decision before running Sonnet / Opus

Cap `issues` with `maxItems` (~12) so the model must *select* rather than
enumerate. This fixes truncation, makes precision meaningful, and matches what a
desk review actually is. It changes the instrument, so it must be decided before
the other models run — and the Haiku data would need re-running to stay
comparable.
