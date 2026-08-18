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
  section.tight { font-size: 22px; }
  section.tight li { margin-bottom: 2px; }
---

<!-- _class: lead -->
<!-- _paginate: false -->

# Claude buys recall with volume, and credibility with precision

**Across 10 investment-research seeds, a weaker model matches a stronger one's issue coverage by firing 9× more objections — half of them invented. The same failure appears in the plans themselves as arbitrary numeric thresholds.**

So the metric that ranks discriminators is not recall or F1, but **manufactured objections per review** — and the eval must be built to make that visible.

<span class="byline">Mayank Mahajan</span>

<!--
Part 1 asks whether Claude turns a half-formed research idea into an executable plan. Part 2 builds
an eval for whether it can tell a good plan from a bad one, and runs four models against human gold.
Part 3 proposes how to collect that gold at scale. The spine is one finding in two registers:
volume substitutes for judgment, and it looks like rigor until you score precision.
-->

---

<!-- _class: tight -->

## Key takeaways

- **Recall does not rank the models, and F1 does not fix it.** Haiku 4.5 covers 53% of blocking gold issues — beating Sonnet 5 (29%) and Opus 4.8 (33%) — by raising **28.6 objections per review against Opus 5's 6.7**. Only **manufactured-per-review** produces the expected gradient: **14.0 / 0.10 / 0.15 / 0.05**.

| | Haiku 4.5 | Sonnet 5 | Opus 4.8 | Opus 5 |
|---|---|---|---|---|
| Pairwise accuracy, **strong** items *(baseline 50%)* | 58% | 67% | 67% | **75%** |
| Blocking-issue recall *(of 45)* | 53% | 29% | 33% | **62%** |
| **Manufactured objections / review** | **14.0** | 0.10 | 0.15 | 0.05 |

- **Confidence is a free difficulty label.** On items I marked `strong`, the ranking holds and all four beat chance. On `weak` items **all four lose to always-answer-A** — those items measure nothing. Score the sure items as the headline.
- **The same defect shows up in the plans.** *Manufactured precision* — invented numeric thresholds (a 20:1 prior, a 25× likelihood-ratio bar, 0.5%/0.2% entry gates) — appeared in **5 of 10** pairs. Unearned specificity reads as rigor.
- **Two generators are the whole ceiling.** Haiku ∪ Opus 5 covers **76%** of blocking issues; **Sonnet 5 and Opus 4.8 contribute zero unique findings.**

<!--
The headline table is the deliverable's core. The weak-item result is the one I'd most want
challenged: two of four models sit exactly at 50% there, and the correct baseline is not a coin
flip but always-answer-A, which scores 75% on the weak subset given its 3A/1B split.
-->

---

<!-- _class: tight -->

## The instrument: 13 tenets, grouped by when they apply

| | Tenet |
|---|---|
| **A · Establish the frame** | **1** Understand the actual goal before judging anything · **2** Fit the plan to the firm and the person · **3** Establish the variant view — where is the edge? |
| **B · Is the design right for that frame** | **4** Simplest thing that could resolve the question, first · **5** Measure the actual target, keep the measure meaningful · **6** Match horizon to phenomenon, not data frequency · **7** Match evidence standard to what the setting supports · **8** Breadth over strength — test where it should *not* work |
| **C · Execution** | **9** Sequence for information — get to 60 before going to 99 · **10** Assume AI execution; human checkpoints, calibrated explainability |
| **D · Intellectual honesty** | **11** Always ask what would make this wrong · **12** Do not invent economics — stay inside the question as framed |
| **E · The review itself** | **13** Standard objections must earn their place in *this* context |

- Groups are **ordered**: a frame error (A) invalidates everything downstream, so precedence rules resolve the designed conflicts — rigor *vs* speed, breadth *vs* firm fit, explainability *vs* performance.
- **Tenet 13 is the precision tenet** and exists because of the failure on slide 2. Every item carries firm context, because a plan is good or bad **for someone**.
- Decisive tenets in gold cluster hard: **2** and **7** drove five items each; **6, 9, 12** were never decisive.

<!--
The tenets are the shared standard for the human gold and the model under test — same document,
both sides. Tenet 13 was added after Part 1, and it is the one that makes manufactured objections
scoreable rather than a matter of taste.
-->

---

<!-- _class: tight -->

## Part III — spend the expert only where models are weakest

Models cannot produce the **24% of blocking issues no generator proposes**; recovering those needs an expert who has read both plans writing from scratch. That pass is irreducible at ~18 min. So **don't degrade the protocol — vary how many items receive it.**

| # | Step | Expert min |
|---|---|---|
| 1 | **Generate** — Haiku 4.5 + Opus 5 critique both plans, every item (~70 candidates/item) | 0 |
| 2 | **Adjudicate** — Opus 5 buckets + dedups, confidence-flagged | 0 |
| 3 | **Re-adjudicate** — a second model forces a call on the *uncertain* pile only | 0 |
| 4–5 | **Rank & split** — score items by auto-tier weakness; top ~5 of 10 to full depth | 0 |
| 6 | **Full-depth pass**, 5 items × 18 min — frame enums · read both plans · free-recall write, *candidates unseen* | **90** |
| 7–9 | **Merge · audit** ~4 auto-accepted cards · **publish** recall beside audited precision | **2** |
| | **Total — 92 min / 10 items** | **9.2 / item** |

- **≈2× cheaper than free-hand (18 min/item) at F1 ≈ 0.90.** F1 0.95 costs 13.1 min.
- **Skip expert triage of candidates** — worst value in the protocol: 9.4 min for ~8pp.
- <span class="heldfixed">The cost:</span> auto-accepting discards Haiku's ~22 confidently-manufactured cards per item unlabelled — the very negatives that ranked the models.

<!--
The 18 min/item free-hand figure is the one observational number here: 10 items in a ~3-hour block.
Everything else on this slide is an estimate. The frontier is a mix, not a compromise: every
intermediate uniform protocol is dominated, because a partial pass still costs 16 of the 18 minutes
and caps at F1 0.86.
-->

---

<!-- _class: tight -->

## Next steps

1. **Measure the one unmeasured number.** Adjudicator precision on candidates it confidently marks real is assumed, not known — the whole Part III frontier rests on it. An audit of ~4 cards per 10 items settles it in the first session; at 0.70 rather than 0.95 the cost rises only to 11.5 min/item.
2. **Break the single-reviewer dependency.** All gold is one desk, so *"wrong"* and *"disagrees with me"* are not separable anywhere in this repo. A second labeller on the same 10 items converts `weak` from a self-report into measured inter-expert disagreement.
3. **Fill the four context-flip items.** Same seed, same plans, different firm — the one axis that isolates tenet 2. Currently measured gold-free only, so context-sensitivity is unquantified.
4. **Rescore with a non-Opus-5 adjudicator.** Opus 5 scored its own reviews and posts the best recall *and* precision under its own judge; the Sonnet / Opus 4.8 inversion (97.0 *vs* 91.7) is where that could be moving the ranking.

<span class="small">Ordered by what each unblocks: (1) the cost model · (2) every accuracy number · (3) a missing axis · (4) the margin on one comparison.</span>

<!--
(1) and (2) are the load-bearing ones. (2) is the deepest limitation in the work: with one reviewer,
a model that disagrees with this desk is indistinguishable from a model that is wrong, and that
applies to every number on slide 2.
-->
