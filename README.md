# Finance Domain Research Lead — take-home

Can Claude turn a half-formed investment research idea into an executable plan,
and can it tell a good plan from a bad one? Part 1 measures the first. Part 2
builds an eval for the second.

## Read these first

| | |
|---|---|
| **`part1/FINDINGS.md`** | The qualitative read. Recall is at ceiling (9 caught / 1 partial / 0 missed) and 7 of 10 plans avoid the known flaw unprompted — the real weakness is **precision**, 2.6 manufactured objections per critique. |
| **`part1/findings-table.md`** | The brief's seed / plan / critique / verdict table, with human verdicts filled in. |
| **`part2/evaluator/TENETS.md`** | The 13 judging tenets, grouped by when in a review they apply, with precedence rules for where they conflict. |
| **`part2/SCORING.md`** | The recall/precision framing, and the four axes that actually separate models. |
| **`REPORT-NOTES.md`** | Working notes: findings as they were established, with the numbers behind them. |

## Layout

```
ideas.md                seed catalog — 14 candidates, the working 10, why each was kept or cut
seeds.json              the 10 seeds as data (`text` is the only field a model ever sees)

part0/                  my own two ideas, written before touching Claude
part1/                  plans, critiques, per-seed scores, findings table, qualitative read
part2/
  forks.json            the two methodologies per seed
  contexts.json         firm context per item, and the four context-flip items
  plans/raw/            the 20 generated plans (opaque token filenames)
  plans/short/          the same plans edited to 500-600w — what items use
  items/S1..S10,*F      assembled eval items: context, seed, plan_A, plan_B, gold
  gold/                 QUARANTINED — human labels + my private design intent
  evaluator/            TENETS.md and the two prompts (pairwise, pointwise)
  ISOLATION.md          what each agent tier may and may not know
  SCORING.md            metrics, diagnostics, known limitations
scripts/                generation, assembly, scoring, eval runner
```

## Running it

```sh
export ANTHROPIC_API_KEY=...            # or: ant auth login

# Part 1 — seed -> plan -> critique, fresh context per call
python scripts/generate.py
python scripts/collect.py               # -> part1/raw.json
python scripts/summarize.py             # -> part1/scorecard.md
python scripts/render_part1.py          # -> part1/findings-table.md

# Part 2 — assemble items, then sweep models
python scripts/build_items.py
python scripts/run_eval.py --mode both --model claude-opus-5
python scripts/run_eval.py --mode both --model claude-haiku-4-5
```

Same items and same prompt text for every model — only `max_tokens` varies, and
it is recorded. Structured outputs force a common response shape so weaker models
are not hand-tuned into parseability.

## The methodological commitment

Every plan and critique here was produced by a model that saw **only the seed
sentence** and, in Part 2, its own assigned methodology — never the known flaw,
the bucket label, a sibling seed, the paired plan, or any repo file. The flaws in
these seeds were enumerated in conversation *before* generation, so a plan written
by a model already told where the flaw is would not be a sample of what Claude
produces cold. `part2/ISOLATION.md` sets out the three agent tiers and what each
may know.

## Status

Part 0 and Part 1 are complete. Part 2 is built and unblocked except for two
things: the human gold pass (`part2/gold/HOW-TO-FILL.md`, 9 fields × 14 items)
and API credits for the model sweep. Part 3 is not started.
