# Lab 1a — 3-Layer Eval Suite

**Case:** InsightFlow Enterprise pricing hallucination
**Source:** M2 failure taxonomy — Rank 1, Trust Tag `#HALLUCINATION`, Severity **P0**
**Carried forward unchanged from M2** (Enterprise pricing $49 vs $59).

## P0 case

| Field | Value |
|---|---|
| **Query** | "What is InsightFlow's pricing for Enterprise?" |
| **Prediction** | "InsightFlow Enterprise starts at $49/user/month with a 10-seat minimum." |
| **Reference** | "Source: Pricing Page (Cached). Old Price: $49/mo. New Price (Updated yesterday): $59/mo." |

## 3-layer results

| Layer | Caught (1/0) | Reasoning |
|---|---|---|
| **1 · Code** | **1** | Structured-field diff: reference's "New Price" field = $59; prediction quotes $49. $49 matches the reference's "Old Price" field exactly — flagged as a **stale-cache** hallucination, not a generic mismatch. |
| **2 · Safety** | 0 | No confidential-leak marker, no mandated-refusal topic. Pricing is public info; this case isn't a Layer 2 concern. |
| **3 · Judge** | **1** | "The prediction incorrectly states InsightFlow's Enterprise pricing is $49/user/month, while the reference specifies the updated price is $59/user/month, making the prediction factually wrong." |

## The read

**This is the Win.** A free, deterministic Layer 1 rule caught the hallucination before any LLM judge call was needed — Layer 3 independently confirmed it, but wasn't required to catch it. The reason this counts as a genuine Win and not a coincidence: pricing is a *structured* fact with a single source of truth, so a rule that diffs quoted prices against a source-of-truth field generalizes across any pricing query, not just this one instance. The failure mode itself is specific and diagnosable — the model didn't invent a random number, it echoed a **stale cached price** that was correct as of a prior snapshot but is now one day out of date. That's a caching/freshness bug in the retrieval layer, not a reasoning bug in the model, and Layer 1 is the right (and only necessary) layer to catch it.

## What I'd ship next

**A Layer 1 rule**, not a Layer 2 route or a Layer 3 rubric change.

Implemented `layer1_pricing_guard(prediction, reference)`:
1. Parses the reference for a structured `Current`/`New Price` field (source of truth).
2. Diffs every dollar figure quoted in the prediction against it.
3. Explicitly detects when the quoted price matches an `Old Price` field, labeling it a **stale-cache** hallucination (not just "a mismatch") — so the fix (re-fetch from a live source instead of serving a cached page) is obvious from the eval output itself, no debugging required.

Regression-tested against 2 controls to confirm it generalizes rather than overfitting to this one trace:
- Correct current price quoted → `caught=0` (no false positive).
- No price claim made → `caught=0` (rule doesn't misfire when it doesn't apply).

**Result:** this rule can now run on every pricing-related trace at zero marginal LLM cost, and it produces an actionable diagnosis (stale cache vs. wrong number vs. no source) instead of a bare pass/fail.

