---
marp: true
theme: default
paginate: true
size: 16:9
math: katex
style: |
  /* Anthropic brand: Poppins headings / Lora body, warm palette, orange accent */
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&family=Lora:ital,wght@0,400;0,500;0,600;1,400;1,500&display=swap');
  section {
    font-family: "Lora", Georgia, serif;
    font-size: 25px;
    color: #141413;
    background: #faf9f5;
    padding: 48px 56px;
  }
  h1 { font-family: "Poppins", Arial, sans-serif; font-weight: 700; font-size: 44px; color: #141413; }
  h2 { font-family: "Poppins", Arial, sans-serif; font-weight: 600; font-size: 33px; color: #141413;
       border-bottom: 3px solid #d97757; padding-bottom: 6px; }
  [data-marpit-advanced-background-container] figure {
    background-origin: content-box;
    background-position: center;
    padding: 26px 34px;
  }
  strong { color: #d97757; font-weight: 600; }
  em { color: #6b6a63; }
  table { font-size: 21px; border-collapse: collapse; }
  th { background: #e8e6dc; color: #141413; }
  td, th { border: 1px solid #b0aea5; padding: 4px 10px; }
  a { color: #6a9bcc; }
  section.lead { justify-content: center; }
  section.lead h1 { font-size: 44px; line-height: 1.18; }
  footer { color: #b0aea5; font-size: 14px; }
  .small { font-size: 19px; color: #6b6a63; }
  .heldfixed { color: #6a9bcc; font-weight: 600; }
  .byline { color: #6b6a63; font-weight: 600; }
  section.tight { font-size: 23.5px; line-height: 1.5; padding: 38px 52px; }
  section.tight h2 { font-size: 33px; margin: 0 0 18px 0; padding-bottom: 6px; }
  section.tight p { margin: 12px 0; }
  section.tight ul { margin: 15px 0; padding-left: 26px; }
  section.tight li { margin-bottom: 11px; }
  section.tight table { font-size: 19px; margin: 16px 0; }
  section.tight td, section.tight th { padding: 6px 10px; line-height: 1.4; }
  section.tight .small { font-size: 18px; }
  section.tenets td:first-child { white-space: nowrap; }
---

<!-- _class: lead -->
<!-- _paginate: false -->

# Two models, paired, grade plans at half the expert cost

**Across 10 investment-research seeds, Haiku 4.5 and Opus 5 together propose 76% of the blocking issues a human reviewer found — the whole achievable ceiling, since Sonnet 5 and Opus 4.8 add nothing the pair misses. Opus 5 then adjudicates both models' candidates and resolves ~70% of them without a human.**

Neither model does this alone. Opus 5 supplies coverage and confident adjudication; Haiku supplies the volume of invented objections that makes precision measurable. Estimated cost: **9.2 expert min an item against 18 free-hand**, at F1 ≈ 0.90.

<span class="byline">Mayank Mahajan</span>

<!--
Part 1 asks whether Claude turns a half-formed research idea into an executable plan. Part 2 builds
an eval for whether it can tell a good plan from a bad one and runs four models against human gold.
Part 3 proposes how to collect that gold at scale. One finding runs through all three: volume
substitutes for judgment, and it reads as rigor until precision is scored.
-->

---

<!-- _class: tight -->

## Key takeaways

- Haiku 4.5 covers 53% of blocking gold issues, beating Sonnet 5 (29%) and Opus 4.8 (33%), by raising **28.6 objections per review against Opus 5's 6.7**. Recall ranks it second; F1 does not fix the ordering.

| | Haiku 4.5 | Sonnet 5 | Opus 4.8 | Opus 5 |
|---|---|---|---|---|
| Pairwise accuracy, **strong** items *(baseline 50%)* | 58% | 67% | 67% | **75%** |
| Blocking-issue recall *(of 45)* | 53% | 29% | 33% | **62%** |
| Blocking issues **no other model found** | **4** | 0 | 0 | **6** |
| **Invented objections / review** | **14.0** | 0.10 | 0.15 | 0.05 |
| Precision | 50.0% | 97.0% | 91.7% | **97.7%** |

- **Invented-per-review is precision rescaled by volume** — same ranking. The count is what reaches the reviewer: at 90% precision, 3 issues/review costs 0.3 junk items, 100 costs 10.
- On `strong` items the ranking holds and all four beat chance. On `weak` items all four lose to always-answer-A, so those items measure nothing.
- Haiku's objections are **not a subset** of Opus 5's — 6 blocking issues Opus 5 misses, lifting the pair to **76%** from 62%. Less overlap buys a more complete review; precision is the price.

<!--
The weak-item result is the one I would most want challenged: two of four models sit exactly at 50%
there, and the right baseline is not a coin flip but always-answer-A, which scores 75% on the weak
subset given its 3A/1B split.
-->

---

<!-- _class: tight tenets -->

## The instrument: 13 tenets, grouped by when they apply

| | Tenet |
|---|---|
| **A · Frame** | **1** Understand the actual goal before judging anything · **2** Fit the plan to the firm and the person · **3** Establish the variant view — where is the edge? |
| **B · Design** | **4** Simplest thing that could resolve the question, first · **5** Measure the actual target, keep the measure meaningful · **6** Match horizon to phenomenon, not data frequency · **7** Match evidence standard to what the setting supports · **8** Breadth over strength — test where it should *not* work |
| **C · Execution** | **9** Sequence for information — get to 60 before going to 99 · **10** Assume AI execution; human checkpoints, calibrated explainability |
| **D · Honesty** | **11** Always ask what would make this wrong · **12** Do not invent economics — stay inside the question as framed |
| **E · Review** | **13** Standard objections must earn their place in *this* context |

- A frame error (A) invalidates everything downstream, so the groups are ordered, and precedence rules resolve the designed conflicts: rigor *vs* speed, breadth *vs* firm fit, explainability *vs* performance.
- Tenet 13 makes an invented objection a scoreable error rather than a matter of taste. Every item carries firm context, because a plan is good or bad **for someone**.
- **2** and **7** were decisive on five items each. **6**, **9** and **12** were never decisive.
- Invented numeric thresholds appear in **5 of 10** plan pairs — the failure tenet 12 exists to catch.

<!--
The tenets are the shared standard for the human gold and the model under test — same document, both
sides. Tenet 13 was added after Part 1, and it is what makes the precision axis scoreable.
-->

---

<!-- _class: tight -->

## Part III — spend the expert only where models are weakest

No model proposes 24% of blocking issues; only an expert reading both plans and writing from scratch finds them, at ~18 min an item. **Run the full pass on fewer items, not a cheaper one on all.**

| # | Step | Expert min |
|---|---|---|
| 1 | **Generate** — Haiku 4.5 + Opus 5 critique both plans, all items (~70 candidates/item) | 0 |
| 2 | **Adjudicate** — Opus 5 buckets + dedups, confidence-flagged | 0 |
| 3 | **Re-adjudicate** — a second model forces a call on the *uncertain* pile only | 0 |
| 4–5 | **Rank & split** — score items by auto-tier weakness; top ~5 of 10 to full depth | 0 |
| 6 | **Full-depth pass**, 5 × 18 min — frame enums · read both plans · free-recall write, *candidates unseen* | **90** |
| 7–9 | **Merge** · **audit** ~4 auto-accepted cards · **publish** recall beside audited precision | **2** |
| | **Total — 92 min / 10 items** | **9.2 / item** |

- **9.2 expert min/item at F1 ≈ 0.90**, against 18 min/item free-hand. F1 0.95 costs 13.1 min.
- Expert triage is the worst value here: 9.4 min for ~8pp. A second model takes the uncertain pile.
- <span class="heldfixed">The cost:</span> auto-accepting leaves Haiku's ~22 invented cards/item unlabelled — the negatives that ranked the models.

<!--
The 18 min/item free-hand figure is the one observational number here: 10 items in a ~3-hour block.
Everything else on this slide is an estimate. The frontier is a mix, not a compromise — every
intermediate uniform protocol is dominated, because a partial pass still costs 16 of the 18 minutes
and caps at F1 0.86.
-->

---

<!-- _class: tight -->

## Next steps

1. **Measure adjudicator precision.** How often the model is wrong on candidates it confidently marks real is assumed, not known, and the whole Part III cost model rests on it. An audit of ~4 cards per 10 items settles it in the first session; at 0.70 rather than 0.95 the cost rises only to 11.5 min/item.
2. **Add a second labeller.** All gold is one desk, so *"wrong"* and *"disagrees with me"* are nowhere separable. A second labeller on the same 10 items turns `weak` from a self-report into measured inter-expert disagreement.
3. **Fill the four context-flip items.** Same seed, same plans, different firm — the one axis isolating tenet 2. Measured gold-free only today, so context-sensitivity is unquantified.
4. **Rescore with a non-Opus-5 adjudicator.** Opus 5 scored its own reviews and posts the best recall *and* precision under its own judge. The Sonnet / Opus 4.8 inversion (97.0 *vs* 91.7) is where that could move the ranking.

<span class="small">Ordered by what each unblocks: (1) the cost model · (2) every accuracy number · (3) a missing axis · (4) the margin on one comparison.</span>

<!--
(1) and (2) are the load-bearing ones. (2) is the deepest limitation in the work: with one reviewer,
a model that disagrees with this desk is indistinguishable from a model that is wrong, and that
applies to every number on slide 2.
-->
