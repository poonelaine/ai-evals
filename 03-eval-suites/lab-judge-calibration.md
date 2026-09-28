# Lab (async) — Judge Calibration

**Dataset:** 20-row Cortex audit table (InsightFlow / DataViz / Competitor grounding queries)
**Rater 1 (Judge):** `judge_score` (0 = failure, 1 = pass)
**Rater 2 (Gold):** `Audit Status` (Confirmed Failure → 0, PASS / PASS (Guardrail) → 1)

## κ result

**Cohen's κ = 1.0** — perfect agreement, 0 disagreements across all 20 rows.

## Confusion matrix

| | Judge = Fail (0) | Judge = Pass (1) |
|---|---|---|
| **Audit = Failure** | 9 | 0 |
| **Audit = Pass** | 0 | 11 |

9 confirmed failures and 11 passes, every single one matched between judge and audit. No off-diagonal cells.

## Diagnosis

κ ≥ 0.60 is met (1.0 ≫ 0.60), so on the surface this is a clean pass. But a perfect 20/20 agreement on a binary task is itself worth scrutinizing rather than taking as unqualified proof of calibration.

**Caveat:** the `Audit Status` column in this audit table appears to be *derived directly from* `judge_score` — every row where the judge scored 0 is labeled "Confirmed Failure," and every row where the judge scored 1 is labeled "PASS," with zero exceptions. This is consistent with the audit being a review/confirmation of the judge's own calls rather than a fully independent, blind re-read of each query/prediction/reference from scratch. If that's the case, κ = 1.0 doesn't prove the judge is well-calibrated in the strict sense (agreement between two *independent* raters) — it shows that the judge and the audit process are self-consistent, which is a weaker but still meaningful signal: it means at minimum the judge isn't producing calls that a reviewer, looking at the same evidence, would overturn.

**Bias check:** across the 9 confirmed failures, the judge correctly caught a range of failure types without an obvious single bias — pricing (row 0), false capability claims (row 2), speaker-status confusion (row 3), ungrounded sentiment embellishment (row 4), inverted competitive facts (row 6), missed compliance badge (row 7), primary/secondary color confusion (row 11), HQ vs. engineering-hub misclassification (row 13), and brand-voice/tone violation (row 15, tagged `#UX_TRUST` rather than `#HALLUCINATION`). The judge is not just flagging "long" or "short" responses — both PASS and Failure rows contain a mix of short and detailed answers (e.g., row 9 "Mark Johnson" passes; row 16 "Yes, but only for Enterprise tiers" passes), so there's no evidence of a length bias in this set.

## Rubric revision

Not required this round — κ already exceeds the 0.60 threshold with no disagreements to diagnose a rubric fix from. Recommended forward step (not a blocker): on a future calibration pass, have the audit performed *before* seeing `judge_score` (a truly blind second read) rather than as a post-hoc review, so κ measures independent agreement rather than confirmation of the judge's own output.
